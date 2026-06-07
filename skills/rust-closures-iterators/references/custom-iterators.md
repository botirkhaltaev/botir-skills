# Custom Iterators

## Implementing Iterator

```rust
struct Counter {
    start: u32,
    end: u32,
}

impl Counter {
    fn new(start: u32, end: u32) -> Self { Self { start, end } }
}

impl Iterator for Counter {
    type Item = u32;

    fn next(&mut self) -> Option<u32> {
        if self.start < self.end {
            let val = self.start;
            self.start += 1;
            Some(val)
        } else {
            None
        }
    }

    // Optional: provide size_hint for allocation optimization
    fn size_hint(&self) -> (usize, Option<usize>) {
        let remaining = (self.end - self.start) as usize;
        (remaining, Some(remaining))
    }
}
```

## IntoIterator

Makes a type usable in `for` loops:

```rust
struct Grid { cells: Vec<Vec<Cell>> }

impl IntoIterator for Grid {
    type Item = Cell;
    type IntoIter = std::vec::IntoIter<Cell>;

    fn into_iter(self) -> Self::IntoIter {
        self.cells.into_iter().flatten().collect::<Vec<_>>().into_iter()
    }
}

// Reference iteration
impl<'a> IntoIterator for &'a Grid {
    type Item = &'a Cell;
    type IntoIter = GridIter<'a>;

    fn into_iter(self) -> GridIter<'a> {
        GridIter { grid: self, row: 0, col: 0 }
    }
}

struct GridIter<'a> { grid: &'a Grid, row: usize, col: usize }

impl<'a> Iterator for GridIter<'a> {
    type Item = &'a Cell;
    fn next(&mut self) -> Option<&'a Cell> {
        // ... advance row/col and return next cell
    }
}
```

## DoubleEndedIterator

Iterate from both ends:

```rust
impl DoubleEndedIterator for Counter {
    fn next_back(&mut self) -> Option<u32> {
        if self.end > self.start {
            self.end -= 1;
            Some(self.end)
        } else {
            None
        }
    }
}

// Enables .rev()
let reversed: Vec<_> = Counter::new(0, 5).rev().collect();
// [4, 3, 2, 1, 0]
```

## ExactSizeIterator

When you know the exact remaining length:

```rust
impl ExactSizeIterator for Counter {
    fn len(&self) -> usize {
        (self.end - self.start) as usize
    }
}
```

## Iterator Patterns

### Windows / Sliding pairs

```rust
struct Pairs<I: Iterator> {
    iter: I,
    prev: Option<I::Item>,
}

impl<I: Iterator> Iterator for Pairs<I>
where I::Item: Clone
{
    type Item = (I::Item, I::Item);

    fn next(&mut self) -> Option<Self::Item> {
        let current = self.iter.next()?;
        let prev = self.prev.replace(current.clone())?;
        Some((prev, current))
    }
}
```

### Batching

```rust
struct Chunks<I: Iterator> {
    iter: I,
    size: usize,
}

impl<I: Iterator> Iterator for Chunks<I> {
    type Item = Vec<I::Item>;

    fn next(&mut self) -> Option<Vec<I::Item>> {
        let mut chunk = Vec::with_capacity(self.size);
        for _ in 0..self.size {
            match self.iter.next() {
                Some(item) => chunk.push(item),
                None => break,
            }
        }
        if chunk.is_empty() { None } else { Some(chunk) }
    }
}
```

### Infinite Iterators

```rust
// Generate values forever
let ones = std::iter::repeat(1);
let counter = std::iter::successors(Some(1), |&prev| Some(prev + 1));
let random = std::iter::from_fn(|| Some(rand::random::<u32>()));

// Always use .take() or .take_while() to bound them
let first_100: Vec<_> = counter.take(100).collect();
```

### Fallible Iterators

```rust
struct LineReader { /* ... */ }

impl Iterator for LineReader {
    type Item = Result<String, io::Error>;

    fn next(&mut self) -> Option<Result<String, io::Error>> {
        // Returns None on EOF, Some(Err) on I/O error
    }
}

// Collecting fallible iterators
let lines: Result<Vec<String>, io::Error> = reader.collect();
```

## Performance Tips

1. **Use `size_hint()`** — helps `collect()` pre-allocate
2. **Implement `ExactSizeIterator`** when possible
3. **Prefer `iter()` over `into_iter()`** when you don't need ownership
4. **Chain adaptors** rather than collecting intermediate results
5. **Use `Iterator::for_each`** instead of `for` loop when side-effect-only (enables parallelism with rayon)
