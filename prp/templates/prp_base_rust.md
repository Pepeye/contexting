name: "Rust PRP Template - Safety-First Development"
description: |
  Template optimized for Rust development with emphasis on memory safety,
  performance, and idiomatic Rust patterns.

---

## Goal

[What needs to be built - be specific about Rust implementation requirements]

## Why

- [Business value and user impact]
- [Performance and safety benefits]
- [Integration with existing Rust codebase]

## What

[User-visible behavior and technical requirements for Rust implementation]

### Success Criteria

- [ ] [Specific measurable outcomes]
- [ ] All tests pass with `cargo test`
- [ ] No clippy warnings
- [ ] Memory safety guaranteed

## All Needed Context

### Documentation & References

```yaml
- url: [Rust documentation URL]
  why: [Specific Rust concepts needed]

- file: [path/to/rust_example.rs]
  why: [Pattern to follow, Rust idioms]

- crate: [crate_name]
  version: [version]
  why: [Specific functionality needed]
```

### Cargo.toml Dependencies

```toml
[dependencies]
# List required crates with versions
# serde = { version = "1.0", features = ["derive"] }
```

### Rust-Specific Gotchas

```rust
// CRITICAL: Ownership and borrowing patterns
// CRITICAL: Error handling with Result<T, E>
// CRITICAL: Async/await patterns if needed
```

## Implementation Blueprint

### Data Structures

```rust
// Define structs, enums, and traits
#[derive(Debug, Clone, Serialize, Deserialize)]
struct DataModel {
    // Fields with proper types
}

// Error types
#[derive(Debug, thiserror::Error)]
enum FeatureError {
    #[error("validation failed: {0}")]
    ValidationError(String),
}
```

### Tasks

```yaml
Task 1: CREATE src/feature_name.rs
  - DEFINE core data structures
  - IMPLEMENT error types
  - ADD basic functionality

Task 2: MODIFY src/lib.rs or src/main.rs
  - EXPORT new module
  - INTEGRATE with existing code
```

### Implementation Pattern

```rust
use std::result::Result;

pub fn feature_function(input: &str) -> Result<Output, FeatureError> {
    // Validate input
    if input.is_empty() {
        return Err(FeatureError::ValidationError("empty input".to_string()));
    }
    
    // Implementation logic
    let result = process_input(input)?;
    
    Ok(result)
}

// Helper functions
fn process_input(input: &str) -> Result<ProcessedData, FeatureError> {
    // Implementation details
    todo!()
}
```

## Validation Loop

### Level 1: Compilation & Linting

```bash
cargo check                    # Fast compilation check
cargo clippy -- -D warnings   # Linting with warnings as errors
cargo fmt --check             # Format verification
```

### Level 2: Testing

```bash
cargo test                     # All tests
cargo test feature_name        # Specific feature tests
cargo test --doc               # Documentation tests
```

### Level 3: Performance & Safety

```bash
cargo bench                    # Benchmarks if available
cargo audit                    # Security audit
cargo outdated                 # Dependency updates
```

## Final Checklist

- [ ] All tests pass: `cargo test`
- [ ] No clippy warnings: `cargo clippy`
- [ ] Proper formatting: `cargo fmt --check`
- [ ] Documentation complete
- [ ] Error handling comprehensive
- [ ] Memory safety verified

## Anti-Patterns to Avoid

- ❌ Don't use `unwrap()` without justification
- ❌ Don't ignore ownership rules
- ❌ Don't use `unsafe` without documentation
- ❌ Don't clone unnecessarily
- ❌ Don't use `String` when `&str` suffices