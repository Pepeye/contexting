# Go Code Review

Review Go code for best practices, performance, and idiomatic patterns.

## Review Checklist

### Best Practices
- [ ] Proper error handling
- [ ] No unused variables or imports
- [ ] Appropriate use of interfaces

### Performance
- [ ] Efficient memory allocation
- [ ] Proper goroutine usage
- [ ] Channel usage patterns

### Idioms
- [ ] Following Go naming conventions
- [ ] Effective use of defer statements
- [ ] Proper package structure

## Auto-fixes

```bash
go fmt
gofumpt -w .
go mod tidy
```