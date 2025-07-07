# Create Go PRP

Generate a comprehensive Go PRP for feature implementation.

## Feature: $ARGUMENTS

Create a complete PRP optimized for Go development following Go idioms and best practices.

## Process

1. **Research Go patterns** in the codebase
2. **Use prp/templates/prp_base_go.md** template
3. **Include go.mod context** and dependencies
4. **Add Go-specific validation** (go build, vet, test, fmt)

## Validation Gates

```bash
# Go validation pipeline
go build
go vet
go test ./...
go fmt
gofumpt -d .
```

Save as: `prp/{feature-name}-go.md`