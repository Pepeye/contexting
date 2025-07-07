# Rust Code Review

Review Rust code for safety, performance, and idiomatic patterns.

## Review Checklist

### Safety
- [ ] No unsafe blocks without justification
- [ ] Proper error handling with Result types
- [ ] No unwrap() in production code

### Performance
- [ ] Efficient memory usage
- [ ] Appropriate use of references vs ownership
- [ ] Iterator chains over manual loops

### Idioms
- [ ] Use of ? operator for error propagation
- [ ] Proper use of Option and Result types
- [ ] Following Rust naming conventions

## Auto-fixes

```bash
cargo clippy --fix
cargo fmt
```