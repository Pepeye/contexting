# Create Rust Base PRP

Generate a comprehensive Rust PRP for feature implementation with deep research and thorough validation.

## Feature: $ARGUMENTS

Create a complete PRP optimized for Rust development following Rust idioms, safety principles, and performance best practices.

## Research Process

During the research process, create clear tasks and spawn subagents as needed using batch tools for thorough investigation.

1. **Codebase Analysis in depth**
   - Search the codebase for similar Rust features/patterns
   - Identify necessary Cargo.toml dependencies and features
   - Note existing Rust conventions and safety patterns
   - Check existing test patterns for validation approach

2. **External Research at scale**
   - Research Rust documentation and best practices
   - Library documentation (include specific URLs)
   - Implementation examples (GitHub/docs.rs/blogs)
   - Safety patterns and common pitfalls
   - Performance considerations specific to Rust

3. **User Clarification**
   - Ask for clarification if needed about Rust-specific requirements

## PRP Generation

Using `prp/templates/prp_base_rust.md` as template:

### Critical Context to Include

- **Rust Documentation**: URLs with specific sections
- **Crate Examples**: Real snippets showing usage patterns
- **Safety Gotchas**: Ownership, borrowing, lifetime issues
- **Performance Patterns**: Zero-cost abstractions, memory efficiency
- **Error Handling**: Result types and error propagation

### Implementation Blueprint

- Start with Rust-specific pseudocode
- Reference real Cargo.toml dependencies
- Include safety and performance considerations
- List tasks in order with Rust keywords

### Validation Gates (Must be Executable)

```bash
# Level 1: Compilation and Safety
cargo check
cargo clippy -- -D warnings  
cargo fmt --check

# Level 2: Testing
cargo test
cargo test --doc

# Level 3: Performance and Security
cargo bench  # if benchmarks exist
cargo audit  # security vulnerabilities
```

## Output

Save as: `prp/{feature-name}-rust.md`

## Quality Checklist

- [ ] All necessary Rust context included
- [ ] Validation gates are executable
- [ ] References existing Rust patterns
- [ ] Clear safety considerations
- [ ] Error handling documented
- [ ] Performance implications noted

Score the PRP on a scale of 1-10 for confidence in one-pass Rust implementation success.