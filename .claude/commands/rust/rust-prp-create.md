# Create Rust PRP

Generate a comprehensive Rust PRP for feature implementation.

## Feature: $ARGUMENTS

Create a complete PRP optimized for Rust development following Rust idioms and safety principles.

## Process

1. **Research Rust patterns** in the codebase
2. **Use prp/templates/prp_base_rust.md** template
3. **Include Cargo.toml context** and dependencies
4. **Add Rust-specific validation** (cargo check, clippy, tests)

## Validation Gates

```bash
# Rust validation pipeline
cargo check
cargo clippy -- -D warnings
cargo test
cargo fmt --check
```

Save as: `prp/{feature-name}-rust.md`