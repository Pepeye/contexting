# Create Go Base PRP

Generate a comprehensive Go PRP for feature implementation with thorough research and validation.

## Feature: $ARGUMENTS

Create a complete PRP optimized for Go development following Go idioms, simplicity principles, and effective patterns.

## Research Process

During the research process, create clear tasks and spawn subagents as needed using batch tools for comprehensive investigation.

1. **Codebase Analysis in depth**
   - Search the codebase for similar Go features/patterns
   - Identify necessary go.mod dependencies and versions
   - Note existing Go conventions and idioms
   - Check existing test patterns and table-driven approaches

2. **External Research at scale**
   - Research Go documentation and effective patterns
   - Library documentation (include specific URLs)
   - Implementation examples (GitHub/pkg.go.dev/blogs)
   - Concurrency patterns and best practices
   - Error handling conventions

3. **User Clarification**
   - Ask for clarification if needed about Go-specific requirements

## PRP Generation

Using `prp/templates/prp_base_go.md` as template:

### Critical Context to Include

- **Go Documentation**: URLs with specific sections
- **Package Examples**: Real snippets showing idiomatic usage
- **Concurrency Gotchas**: Goroutines, channels, context patterns
- **Interface Design**: Accept interfaces, return structs
- **Error Handling**: Explicit error checking and wrapping

### Implementation Blueprint

- Start with Go-specific pseudocode
- Reference real go.mod dependencies
- Include concurrency and error handling patterns
- List tasks in order with Go idioms

### Validation Gates (Must be Executable)

```bash
# Level 1: Build and Static Analysis
go build ./...
go vet ./...
go fmt ./...
gofumpt -d .

# Level 2: Testing
go test ./...
go test -race ./...
go test -cover ./...

# Level 3: Advanced Analysis
golangci-lint run
go mod tidy
govulncheck ./...
```

## Output

Save as: `prp/{feature-name}-go.md`

## Quality Checklist

- [ ] All necessary Go context included
- [ ] Validation gates are executable
- [ ] References existing Go patterns
- [ ] Clear concurrency considerations
- [ ] Error handling documented
- [ ] Interface design principles followed

Score the PRP on a scale of 1-10 for confidence in one-pass Go implementation success.