---
name: modal-dicts-queues
description: Distributed in-memory data structures on Modal — Dict (key-value store) and Queue (FIFO). Use when sharing state between containers, passing messages, coordinating parallel work, or caching intermediate results.
---

# Modal Dicts and Queues

Use this skill when you need distributed in-memory data structures for inter-container communication, coordination, or ephemeral state.

## When to Use This Skill

- Sharing key-value state between Functions/containers
- Passing messages between producer and consumer Functions
- Coordinating parallel workers (e.g., progress tracking)
- Caching intermediate results across containers
- Building pub/sub or task distribution patterns

## Dict — Distributed Key-Value Store

### Create and use

```python
import modal

kv = modal.Dict.from_name("my-dict", create_if_missing=True)

kv["key"] = "value"       # set from anywhere
value = kv["key"]          # get from anywhere
del kv["key"]              # delete
"key" in kv                # membership check
len(kv)                    # count entries
```

### Ephemeral Dicts (scoped to a context)

```python
with modal.Dict.ephemeral() as d:
    d["temp"] = 42
    # Dict disappears when context exits
```

### Async access

```python
await kv.put.aio("key", "value")
value = await kv.get.aio("key")
```

### Constraints

| Limit | Value |
|-------|-------|
| Max object size | 100 MiB |
| Max entries per update | 10,000 |
| Recommended object size | < 5 MiB |
| Inactivity expiry | 7 days (no reads/writes) |

Objects serialized via `cloudpickle`. Mutable value updates are **not** reflected remotely — you must explicitly `put` back updated objects.

### Locking

Dicts provide a locking primitive for coordinated updates — see `Dict.lock()`.

## Queue — Distributed FIFO Queue

### Create and use

```python
import modal

queue = modal.Queue.from_name("my-queue", create_if_missing=True)

queue.put("task-1")           # enqueue
item = queue.get()            # dequeue (blocks by default)
queue.put(42, partition="p1") # partitioned queue
```

### Partitioned queues

Queues are split into FIFO partitions by string key. Default partition is empty string.

```python
with modal.Queue.ephemeral() as q:
    q.put(0)                          # default partition
    q.put(1, partition="foo")
    q.put(2, partition="bar")
    assert q.get(partition="bar") == 2

    # Custom TTL on partition
    q.put(3, partition="foo", partition_ttl=10)

    # Iterate immutably
    for v in q.iterate():
        print(v)
```

### Blocking/non-blocking/async

```python
# Blocking with timeout
item = queue.get(timeout=5)  # raises queue.Empty after 5s

# Non-blocking
item = queue.get(block=False)  # raises queue.Empty immediately if empty

# Async
item = await queue.get.aio()
```

### Constraints

| Limit | Value |
|-------|-------|
| Max partitions | 100,000 |
| Max items per partition | 5,000 |
| Max item size | 1 MiB |
| Default partition TTL | 24 hours after last put |

### Batch operations

```python
queue.put_many([1, 2, 3])
items = queue.get_many(3, timeout=5)
```

## Dict vs Queue Decision

| Need | Use |
|------|-----|
| Random access by key | Dict |
| Ordered processing | Queue |
| Cross-container cache | Dict |
| Producer/consumer pipeline | Queue |
| Coordination (e.g., address sharing) | Dict |
| Task distribution | Queue |

## Common Patterns

### Progress tracking with Dict

```python
progress = modal.Dict.from_name("progress", create_if_missing=True)

@app.function()
def worker(item_id: int):
    result = process(item_id)
    progress[str(item_id)] = "done"
    return result
```

### Fan-out with Queue

```python
task_queue = modal.Queue.from_name("tasks", create_if_missing=True)

@app.function()
def producer():
    for i in range(1000):
        task_queue.put(i)

@app.function()
def consumer():
    while True:
        try:
            item = task_queue.get(timeout=10)
            process(item)
        except queue.Empty:
            break
```

## Symptom → Fix

| Symptom | Likely cause | Fix |
|---------|-------------|-----|
| `KeyError` on Dict access | Key expired (7-day inactivity) or never set | Check key exists; use `.get(key, default)` |
| `queue.Empty` exception | Queue empty or timeout too short | Increase timeout or use `block=False` |
| Stale Dict values | Mutable objects updated locally but not put back | Always `dict[key] = updated_value` explicitly |
| Serialization error | Object not cloudpickle-serializable | Ensure target object is serializable |

## References

- [dicts-reference.md](references/dicts-reference.md) — Dict API details and patterns
- [queues-reference.md](references/queues-reference.md) — Queue API details and patterns

## Guardrails

- Do not use Queues for persistent storage — they clear 24h after last put
- Do not rely on Dict for data larger than 5 MiB per entry
- Both Dict and Queue require network round-trips (~tens of ms per operation)
- Use async `.aio` variants to avoid blocking on network latency
- Use partitioned Queues to avoid contention across independent workstreams
