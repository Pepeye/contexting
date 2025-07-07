name: "Go PRP Template - Idiomatic Go Development"
description: |
  Template optimized for Go development with emphasis on simplicity,
  concurrency, and idiomatic Go patterns.

---

## Goal

[What needs to be built - be specific about Go implementation requirements]

## Why

- [Business value and user impact]
- [Performance and concurrency benefits]
- [Integration with existing Go codebase]

## What

[User-visible behavior and technical requirements for Go implementation]

### Success Criteria

- [ ] [Specific measurable outcomes]
- [ ] All tests pass with `go test`
- [ ] No vet warnings
- [ ] Proper error handling

## All Needed Context

### Documentation & References

```yaml
- url: [Go documentation URL]
  why: [Specific Go concepts needed]

- file: [path/to/go_example.go]
  why: [Pattern to follow, Go idioms]

- module: [module_name]
  version: [version]
  why: [Specific functionality needed]
```

### go.mod Dependencies

```go
module feature-name

go 1.21

require (
    // List required modules with versions
)
```

### Go-Specific Patterns

```go
// PATTERN: Error handling
if err != nil {
    return nil, fmt.Errorf("operation failed: %w", err)
}

// PATTERN: Interface usage
type FeatureInterface interface {
    Process(input string) (string, error)
}
```

## Implementation Blueprint

### Data Structures

```go
// Define structs and interfaces
type FeatureData struct {
    ID    string `json:"id"`
    Value string `json:"value"`
}

// Error types
type FeatureError struct {
    Op  string
    Err error
}

func (e *FeatureError) Error() string {
    return fmt.Sprintf("%s: %v", e.Op, e.Err)
}
```

### Tasks

```yaml
Task 1: CREATE pkg/feature/feature.go
  - DEFINE core data structures
  - IMPLEMENT interfaces
  - ADD main functionality

Task 2: CREATE pkg/feature/feature_test.go
  - ADD unit tests
  - INCLUDE table-driven tests
```

### Implementation Pattern

```go
package feature

import (
    "context"
    "fmt"
)

func ProcessFeature(ctx context.Context, input string) (*FeatureData, error) {
    // Validate input
    if input == "" {
        return nil, &FeatureError{
            Op:  "ProcessFeature",
            Err: fmt.Errorf("empty input"),
        }
    }
    
    // Implementation logic
    result := &FeatureData{
        ID:    generateID(),
        Value: processInput(input),
    }
    
    return result, nil
}

func processInput(input string) string {
    // Implementation details
    return input
}
```

## Validation Loop

### Level 1: Build & Vet

```bash
go build                       # Compilation check
go vet                         # Static analysis
go fmt                         # Format code
gofumpt -w .                   # Strict formatting
```

### Level 2: Testing

```bash
go test ./...                  # All tests
go test -race ./...            # Race condition detection
go test -cover ./...           # Coverage report
```

### Level 3: Performance

```bash
go test -bench=.               # Benchmarks
go tool pprof                  # Profiling if needed
golangci-lint run              # Comprehensive linting
```

## Final Checklist

- [ ] All tests pass: `go test ./...`
- [ ] No vet warnings: `go vet`
- [ ] Proper formatting: `go fmt`
- [ ] Race condition free: `go test -race`
- [ ] Error handling complete
- [ ] Documentation added

## Anti-Patterns to Avoid

- ❌ Don't ignore errors
- ❌ Don't use panic for normal error handling
- ❌ Don't create goroutines without proper lifecycle management
- ❌ Don't use channels inappropriately
- ❌ Don't forget context cancellation