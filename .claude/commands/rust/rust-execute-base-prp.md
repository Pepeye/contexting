# Execute Rust PRP

Implement a Rust feature using the PRP file.

## PRP File: $ARGUMENTS

## Execution Process

1. **Load PRP** and understand Rust-specific requirements
2. **Plan implementation** with Rust safety principles
3. **Execute** following ownership and borrowing patterns
4. **Validate** with cargo tools

## Rust Validation Pipeline

```bash
cargo check              # Compile check
cargo clippy -- -D warnings  # Linting with warnings as errors
cargo test               # Unit and integration tests
cargo fmt --check        # Format verification
```

Focus on Rust idioms: ownership, borrowing, error handling with Result types.