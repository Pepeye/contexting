# Rust Review Staged and Unstaged Changes

Review staged and unstaged Rust code changes with comprehensive analysis.

## Process

1. **Analyze Changes**
   - Review git diff for staged and unstaged changes
   - Identify modified Rust files and their impact
   - Check for breaking changes or API modifications

2. **Rust-Specific Review**
   - Memory safety and ownership patterns
   - Error handling and Result usage
   - Performance implications
   - Code style and formatting

3. **Validation Commands**

```bash
# Check staged changes
git diff --cached

# Check unstaged changes  
git diff

# Rust validation pipeline
cargo fmt --check
cargo clippy -- -D warnings
cargo test
cargo check
```

## Review Focus Areas

### Safety and Correctness
- [ ] No unsafe code without justification
- [ ] Proper error handling with Result types
- [ ] No unwrap() in production code
- [ ] Memory safety maintained

### Performance
- [ ] Efficient algorithms and data structures
- [ ] Appropriate use of references vs ownership
- [ ] No unnecessary allocations or clones

### Code Quality
- [ ] Follows Rust naming conventions
- [ ] Proper documentation and comments
- [ ] Tests cover new functionality
- [ ] API design is ergonomic

## Validation Checklist

- [ ] All changes compile: `cargo check`
- [ ] No clippy warnings: `cargo clippy`
- [ ] Proper formatting: `cargo fmt --check`
- [ ] Tests pass: `cargo test`
- [ ] Documentation builds: `cargo doc`