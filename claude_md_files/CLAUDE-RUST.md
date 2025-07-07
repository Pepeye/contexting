# CLAUDE-RUST.md

Comprehensive development guidelines for Rust projects using Claude Code.

## Core Rust Development Principles

### Safety First
- **Memory Safety**: Leverage Rust's ownership system
- **Type Safety**: Use the type system to prevent errors
- **Concurrency Safety**: Use async/await and channels appropriately
- **Error Handling**: Always use `Result<T, E>` for fallible operations

### Performance Oriented
- **Zero-cost Abstractions**: Prefer iterators over manual loops
- **Efficient Memory Usage**: Understand borrowing vs ownership
- **Compile-time Optimization**: Let the compiler do the work

## Essential Development Commands

```bash
# Development cycle
cargo watch -x check -x test -x run

# Code quality
cargo clippy -- -D warnings
cargo fmt --check
cargo audit

# Testing
cargo test
cargo test --doc
cargo bench

# Performance profiling
cargo build --release
RUSTFLAGS="-C target-cpu=native" cargo build --release
```

## Project Structure

```
rust-project/
├── Cargo.toml              # Dependencies and metadata
├── Cargo.lock              # Locked dependencies
├── src/
│   ├── lib.rs or main.rs   # Entry point
│   ├── module1/            # Feature modules
│   │   ├── mod.rs
│   │   └── submodule.rs
│   └── utils/              # Utility modules
├── tests/                  # Integration tests
├── benches/                # Benchmarks
├── examples/               # Usage examples
└── docs/                   # Documentation
```

## Code Quality Standards

### Mandatory Lints

```rust
#![deny(unsafe_code)]
#![warn(
    missing_docs,
    rust_2018_idioms,
    missing_debug_implementations,
    missing_copy_implementations,
    trivial_casts,
    trivial_numeric_casts,
    unused_import_braces,
    unused_qualifications
)]
```

### Error Handling Patterns

```rust
// Use thiserror for custom error types
#[derive(Debug, thiserror::Error)]
pub enum AppError {
    #[error("Database error: {0}")]
    Database(#[from] sqlx::Error),
    #[error("Validation error: {message}")]
    Validation { message: String },
    #[error("Not found: {resource}")]
    NotFound { resource: String },
    #[error("IO error: {0}")]
    Io(#[from] std::io::Error),
}

pub type Result<T> = std::result::Result<T, AppError>;

// Prefer ? operator for error propagation
fn fallible_operation() -> Result<String> {
    let content = std::fs::read_to_string("file.txt")?;
    
    if content.is_empty() {
        return Err(AppError::Validation {
            message: "File is empty".to_string(),
        });
    }
    
    Ok(content.trim().to_string())
}
```

### Async Patterns

```rust
// Use tokio for async runtime
#[tokio::main]
async fn main() -> Result<()> {
    let result = fetch_data().await?;
    process_data(result).await?;
    Ok(())
}

// Proper async error handling
async fn fetch_data() -> Result<Data> {
    let response = reqwest::get("https://api.example.com/data").await
        .map_err(|e| AppError::Validation { message: format!("HTTP request failed: {}", e) })?;
    let data = response.json::<Data>().await
        .map_err(|e| AppError::Validation { message: format!("JSON parsing failed: {}", e) })?;
    Ok(data)
}

// Concurrent processing pattern
pub async fn process_items(items: Vec<Item>) -> Vec<Result<ProcessedItem>> {
    use futures::stream::{self, StreamExt};

    stream::iter(items)
        .map(|item| async move { process_item(item).await })
        .buffer_unordered(10)
        .collect()
        .await
}

// Parallel operations with tokio::join!
async fn fetch_multiple_data() -> Result<(Data1, Data2)> {
    let (result1, result2) = tokio::join!(
        fetch_data_source1(),
        fetch_data_source2()
    );
    
    Ok((result1?, result2?))
}
```

## Testing Patterns

### Unit Tests

```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_basic_functionality() {
        let result = my_function("input");
        assert_eq!(result, expected_output);
    }

    #[tokio::test]
    async fn test_async_function() {
        let result = async_function().await.unwrap();
        assert!(result.is_valid());
    }
}
```

### Integration Tests

```rust
// tests/integration_test.rs
use my_crate::*;

#[tokio::test]
async fn test_full_workflow() {
    let service = Service::new().await;
    let result = service.process("test_data").await.unwrap();
    assert_eq!(result.status, "success");
}
```

## Validation Gates

### Level 1: Compilation and Style
```bash
cargo check                     # Fast compilation check
cargo clippy -- -D warnings    # Linting with warnings as errors
cargo fmt --check              # Format verification
```

### Level 2: Testing
```bash
cargo test                      # Unit tests
cargo test --doc                # Documentation tests
cargo test --workspace          # All workspace tests
```

### Level 3: Security and Performance
```bash
cargo audit                     # Security vulnerabilities
cargo bench                     # Performance benchmarks
cargo outdated                  # Dependency updates
```

## Common Patterns

### Input Validation
```rust
use serde::{Deserialize, Serialize};
use validator::{Validate, ValidationError};

#[derive(Debug, Deserialize, Validate)]
pub struct CreateUserRequest {
    #[validate(email)]
    pub email: String,
    #[validate(length(min = 2, max = 50))]
    pub name: String,
    #[validate(length(min = 8))]
    pub password: String,
}

pub fn validate_user_input(input: &CreateUserRequest) -> Result<()> {
    input.validate()
        .map_err(|e| AppError::Validation { 
            message: format!("Invalid input: {}", e) 
        })?;
    Ok(())
}
```

### Iterator Usage
```rust
// Prefer functional style
let result: Vec<_> = data
    .iter()
    .filter(|&x| x > 0)
    .map(|x| x * 2)
    .collect();

// Over imperative style
let mut result = Vec::new();
for x in &data {
    if *x > 0 {
        result.push(x * 2);
    }
}
```

### Option and Result Handling
```rust
// Use combinators
let processed = input
    .parse::<i32>()
    .map(|n| n * 2)
    .and_then(|n| some_fallible_operation(n))
    .unwrap_or_default();

// Pattern matching for complex cases
match try_operation() {
    Ok(value) => process_success(value),
    Err(AppError::Validation { message }) => handle_validation_error(message),
    Err(AppError::Database(err)) => handle_database_error(err),
    Err(AppError::NotFound { resource }) => handle_not_found(resource),
    Err(AppError::Io(err)) => handle_io_error(err),
}
```

## Anti-Patterns to Avoid

### Forbidden Patterns
- ❌ `unwrap()` in production code (use `expect()` with descriptive messages if necessary)
- ❌ `clone()` without consideration of performance implications
- ❌ Ignoring warnings from clippy
- ❌ Using `unsafe` without thorough documentation and justification
- ❌ Blocking operations in async functions

### Memory Management
```rust
// Bad: Unnecessary cloning
fn process_data(data: Vec<String>) -> Vec<String> {
    data.clone().into_iter().map(|s| s.to_uppercase()).collect()
}

// Good: Use references when possible
fn process_data(data: &[String]) -> Vec<String> {
    data.iter().map(|s| s.to_uppercase()).collect()
}
```

## Performance Tips

### Compilation Optimization
```toml
# Cargo.toml
[profile.release]
codegen-units = 1
lto = true
panic = "abort"

[profile.dev]
debug = 1  # Faster compilation
```

### Runtime Performance
- Use `Vec<T>` over `LinkedList<T>` in most cases
- Prefer `&str` over `String` when you don't need ownership
- Use `Box<[T]>` for immutable arrays
- Consider `Arc<T>` for shared ownership, `Rc<T>` for single-threaded

## PRP Integration

When creating Rust PRPs:
1. Always include `Cargo.toml` dependencies
2. Specify exact crate versions and features
3. Include clippy configuration if needed
4. Document unsafe code usage and justification
5. Provide comprehensive error handling patterns

### Validation Template

```bash
# Include in every Rust PRP
cargo check
cargo clippy -- -D warnings
cargo fmt --check
cargo test
cargo test --doc
cargo audit
```

This ensures consistent, safe, and performant Rust code across all projects.