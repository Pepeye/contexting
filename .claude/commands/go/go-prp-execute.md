# Execute Go PRP

Implement a Go feature using the PRP file.

## PRP File: $ARGUMENTS

## Execution Process

1. **Load PRP** and understand Go-specific requirements
2. **Plan implementation** with Go conventions
3. **Execute** following Go best practices
4. **Validate** with go toolchain

## Go Validation Pipeline

```bash
go build                 # Compile check
go vet                   # Static analysis
go test ./...            # All tests
go fmt                   # Format code
gofumpt -d .            # Strict formatting check
```

Focus on Go idioms: error handling, interfaces, goroutines, channels.