# Iterator Adaptors Reference

All adaptors are lazy — they return a new iterator without doing work until consumed.

## Transforming

### map

```rust
let names: Vec<String> = users.iter().map(|u| u.name.clone()).collect();
```

### flat_map

Flattens nested iterators:
```rust
let words: Vec<&str> = lines.iter().flat_map(|line| line.split_whitespace()).collect();
```

### flatten

```rust
let all: Vec<i32> = vec![vec![1,2], vec![3,4]].into_iter().flatten().collect();
// [1, 2, 3, 4]
```

### map_while

Maps while predicate holds, then stops:
```rust
let squares: Vec<_> = (0..).map_while(|x| {
    let sq = x * x;
    if sq < 100 { Some(sq) } else { None }
}).collect();
```

## Filtering

### filter

```rust
let evens: Vec<_> = (0..20).filter(|x| x % 2 == 0).collect();
```

### filter_map

Filter and transform in one step:
```rust
let parsed: Vec<i32> = strings.iter()
    .filter_map(|s| s.parse().ok())
    .collect();
```

### take_while / skip_while

```rust
let prefix: Vec<_> = items.iter().take_while(|x| x.is_some()).collect();
let suffix: Vec<_> = items.iter().skip_while(|x| x.is_none()).collect();
```

## Sizing

### take / skip

```rust
let first_5: Vec<_> = iter.take(5).collect();
let after_3: Vec<_> = iter.skip(3).collect();
```

### step_by

```rust
let every_third: Vec<_> = (0..30).step_by(3).collect();
// [0, 3, 6, 9, ...]
```

### chunks (on slices)

```rust
for chunk in data.chunks(3) {
    println!("{chunk:?}");  // &[T] of up to 3 elements
}
```

## Combining

### chain

```rust
let all: Vec<_> = first.iter().chain(second.iter()).collect();
```

### zip

```rust
let pairs: Vec<_> = keys.iter().zip(values.iter()).collect();
// stops at shorter iterator
```

### interleave (itertools)

```rust
use itertools::Itertools;
let merged: Vec<_> = a.iter().interleave(b.iter()).collect();
```

## Inspection

### enumerate

```rust
for (i, item) in items.iter().enumerate() {
    println!("{i}: {item}");
}
```

### inspect

```rust
let result: Vec<_> = data.iter()
    .inspect(|x| eprintln!("before filter: {x:?}"))
    .filter(|x| x.valid)
    .inspect(|x| eprintln!("after filter: {x:?}"))
    .collect();
```

### peekable

```rust
let mut iter = items.iter().peekable();
while let Some(&&next) = iter.peek() {
    if next > threshold {
        break;
    }
    let item = iter.next().unwrap();
    process(item);
}
```

## Accumulation

### fold

```rust
let sum = numbers.iter().fold(0, |acc, &x| acc + x);
let max = numbers.iter().fold(i32::MIN, |acc, &x| acc.max(x));

// Building a complex result
let histogram: HashMap<char, usize> = text.chars()
    .fold(HashMap::new(), |mut map, c| {
        *map.entry(c).or_insert(0) += 1;
        map
    });
```

### scan

Like fold but yields intermediate states:
```rust
let running_sum: Vec<i32> = numbers.iter()
    .scan(0, |state, &x| { *state += x; Some(*state) })
    .collect();
```

### reduce

Like fold but uses first element as initial:
```rust
let product = numbers.iter().copied().reduce(|a, b| a * b);
// Returns Option (None if empty)
```

## Ordering

### sorted (itertools) / sort on Vec

```rust
use itertools::Itertools;
let sorted: Vec<_> = items.iter().sorted_by_key(|x| x.priority).collect();
```

### rev (requires DoubleEndedIterator)

```rust
let reversed: Vec<_> = (0..10).rev().collect();
```

### min_by_key / max_by_key

```rust
let cheapest = products.iter().min_by_key(|p| p.price);
```

## Grouping (itertools)

```rust
use itertools::Itertools;

// Group consecutive elements
for (key, group) in &data.iter().group_by(|x| x.category) {
    println!("{key}: {:?}", group.collect::<Vec<_>>());
}

// Unique elements
let unique: Vec<_> = items.iter().unique_by(|x| x.id).collect();

// Cartesian product
let combos: Vec<_> = (0..3).cartesian_product(0..3).collect();
```
