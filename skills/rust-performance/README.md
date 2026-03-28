# rust-performance

Distilled Rust optimization guidance for agents, based on The Rust Performance Book.

## Covers

- runtime speed
- memory use and allocation churn
- binary size
- compile times
- Rust-specific hotspots such as hashing, iterators, I/O, logging, and synchronization

This skill is organized for agent use, not as a chapter-by-chapter mirror.

## Install

```bash
npx skills add botirk38/botir-skills --skill rust-performance
```

List all skills in the repo:

```bash
npx skills add botirk38/botir-skills --list
```

## Files

- `SKILL.md` - trigger criteria, triage flow, and guardrails
- `references/measurement.md` - benchmarking and profiling
- `references/build-configuration.md` - Cargo profile and codegen tuning
- `references/allocations-layout.md` - allocations, type size, layout, wrappers
- `references/collections-iterators.md` - iterators, std types, hashing
- `references/io-debugging.md` - buffering, logging, assertions, line handling
- `references/parallelism.md` - concurrency and synchronization trade-offs
- `references/compile-times.md` - timings, macros, monomorphization, linkers
- `references/general-principles.md` - optimization mindset and guardrails

## Source

- Upstream: The Rust Performance Book
- Author: Nicholas Nethercote and contributors
- Source repo: `https://github.com/nnethercote/perf-book`
- License: `MIT OR Apache-2.0`

This skill was distilled from the full published book corpus defined by upstream `src/SUMMARY.md`. The raw scrape was used during authoring and then removed from this repo.

## Best Use Cases

- profiling slow Rust code
- reducing allocations
- tuning Cargo profile settings
- diagnosing slow builds
- evaluating iterator, hashing, or I/O overhead
- improving performance without cargo-culting changes
