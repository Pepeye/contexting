# Go Review Staged and Unstaged Changes

Review staged and unstaged Go code changes with comprehensive analysis.

## Process

1. **Analyze Changes**
   - Review git diff for staged and unstaged changes
   - Identify modified Go files and their impact
   - Check for breaking changes or API modifications

2. **Go-Specific Review**
   - Idiomatic Go patterns
   - Error handling conventions
   - Concurrency safety
   - Performance implications

3. **Validation Commands**

```bash
# Check staged changes
git diff --cached

# Check unstaged changes  
git diff

# Go validation pipeline
go fmt
go vet ./...
go test ./...
go build ./...
```

## Review Focus Areas

### Go Idioms and Best Practices
- [ ] Proper error handling (no ignored errors)
- [ ] Effective interface usage
- [ ] Appropriate use of goroutines and channels
- [ ] Following Go naming conventions

### Concurrency and Safety
- [ ] No race conditions
- [ ] Proper context usage
- [ ] Channel usage patterns are correct
- [ ] Goroutine lifecycle management

### Code Quality
- [ ] Code is gofmt'd and follows standards
- [ ] Comprehensive error messages
- [ ] Tests cover new functionality
- [ ] Documentation is clear and complete

## Validation Checklist

- [ ] Code compiles: `go build ./...`
- [ ] Static analysis passes: `go vet ./...`
- [ ] Tests pass: `go test ./...`
- [ ] No race conditions: `go test -race ./...`
- [ ] Properly formatted: `gofmt -d .`
- [ ] Linting passes: `golangci-lint run`