# Rust Development Utilities

Essential Rust development commands and utilities.

## Quick Commands

```bash
# Development cycle
cargo watch -x check -x test -x run

# Performance build
cargo build --release

# Documentation
cargo doc --open

# Benchmarks
cargo bench
```

## Common Patterns

- Use `Result<T, E>` for error handling
- Leverage ownership and borrowing
- Prefer iterators over loops
- Use `#[derive(Debug)]` for debugging