# CLAUDE-GO.md

Comprehensive development guidelines for Go projects using Claude Code.

## Core Go Development Principles

### Simplicity and Clarity
- **Explicit over implicit**: Clear code is better than clever code
- **Composition over inheritance**: Use interfaces and embedding
- **Concurrency via goroutines**: Don't communicate by sharing memory
- **Error handling**: Errors are values, handle them explicitly

### Idiomatic Go
- **gofmt is law**: All code must be formatted with gofmt
- **Effective Go patterns**: Follow established conventions
- **Package design**: Keep packages cohesive and focused
- **Interface design**: Accept interfaces, return structs

## Essential Development Commands

```bash
# Development cycle
go run main.go
air  # Live reload tool

# Code quality
go vet ./...
gofmt -d .
gofumpt -d .
golangci-lint run

# Testing
go test ./...
go test -race ./...
go test -cover ./...

# Performance
go test -bench=.
go tool pprof
```

## Project Structure

```
go-project/
├── go.mod                   # Module definition
├── go.sum                   # Dependency checksums
├── cmd/
│   └── myapp/
│       └── main.go         # Application entry points
├── internal/               # Private application code
│   ├── handlers/
│   ├── models/
│   └── services/
├── pkg/                    # Public library code
├── api/                    # API definitions (OpenAPI, protobuf)
├── web/                    # Web application assets
├── scripts/                # Build and deployment scripts
├── deployments/            # Docker, k8s configs
└── test/                   # Integration tests
```

## Code Quality Standards

### Error Handling Patterns

```go
// Custom error types
type ValidationError struct {
    Field   string
    Message string
}

func (e *ValidationError) Error() string {
    return fmt.Sprintf("validation failed for %s: %s", e.Field, e.Message)
}

// Error wrapping
func processFile(filename string) error {
    file, err := os.Open(filename)
    if err != nil {
        return fmt.Errorf("failed to open file %s: %w", filename, err)
    }
    defer file.Close()

    // Process file...
    if err := processData(file); err != nil {
        return fmt.Errorf("failed to process file %s: %w", filename, err)
    }

    return nil
}
```

### Interface Design

```go
// Small, focused interfaces
type Reader interface {
    Read([]byte) (int, error)
}

type Writer interface {
    Write([]byte) (int, error)
}

// Composition
type ReadWriter interface {
    Reader
    Writer
}

// Accept interfaces, return structs
func ProcessData(r Reader) (*Result, error) {
    // Implementation...
    return &Result{}, nil
}
```

### Concurrency Patterns

```go
// Worker pool pattern
func processJobs(jobs <-chan Job, results chan<- Result, wg *sync.WaitGroup) {
    defer wg.Done()
    for job := range jobs {
        result := processJob(job)
        results <- result
    }
}

// Context usage
func fetchData(ctx context.Context, url string) (*Data, error) {
    req, err := http.NewRequestWithContext(ctx, "GET", url, nil)
    if err != nil {
        return nil, fmt.Errorf("creating request: %w", err)
    }

    resp, err := http.DefaultClient.Do(req)
    if err != nil {
        return nil, fmt.Errorf("executing request: %w", err)
    }
    defer resp.Body.Close()

    // Parse response...
    return data, nil
}
```

## Testing Patterns

### Table-Driven Tests

```go
func TestValidateEmail(t *testing.T) {
    tests := []struct {
        name    string
        email   string
        wantErr bool
    }{
        {
            name:    "valid email",
            email:   "user@example.com",
            wantErr: false,
        },
        {
            name:    "invalid email",
            email:   "invalid-email",
            wantErr: true,
        },
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            err := validateEmail(tt.email)
            if (err != nil) != tt.wantErr {
                t.Errorf("validateEmail() error = %v, wantErr %v", err, tt.wantErr)
            }
        })
    }
}
```

### Benchmark Tests

```go
func BenchmarkProcessData(b *testing.B) {
    data := generateTestData()
    
    b.ResetTimer()
    for i := 0; i < b.N; i++ {
        processData(data)
    }
}
```

### Integration Tests

```go
func TestAPIEndpoint(t *testing.T) {
    server := httptest.NewServer(http.HandlerFunc(myHandler))
    defer server.Close()

    resp, err := http.Get(server.URL + "/api/test")
    if err != nil {
        t.Fatalf("Failed to make request: %v", err)
    }
    defer resp.Body.Close()

    if resp.StatusCode != http.StatusOK {
        t.Errorf("Expected status 200, got %d", resp.StatusCode)
    }
}
```

## Validation Gates

### Level 1: Build and Vet
```bash
go build ./...                  # Compilation check
go vet ./...                    # Static analysis
go fmt ./...                    # Format code
gofumpt -w .                    # Strict formatting
```

### Level 2: Testing
```bash
go test ./...                   # All tests
go test -race ./...             # Race condition detection
go test -cover ./...            # Coverage report
go test -short ./...            # Fast tests only
```

### Level 3: Advanced Analysis
```bash
golangci-lint run               # Comprehensive linting
go mod tidy                     # Clean dependencies
go list -m -u all               # Check for updates
govulncheck ./...               # Security vulnerabilities
```

## Common Patterns

### HTTP Server Setup

```go
func main() {
    mux := http.NewServeMux()
    mux.HandleFunc("/health", healthHandler)
    mux.HandleFunc("/api/", apiHandler)

    server := &http.Server{
        Addr:         ":8080",
        Handler:      mux,
        ReadTimeout:  15 * time.Second,
        WriteTimeout: 15 * time.Second,
        IdleTimeout:  60 * time.Second,
    }

    log.Printf("Server starting on %s", server.Addr)
    if err := server.ListenAndServe(); err != nil {
        log.Fatalf("Server failed to start: %v", err)
    }
}
```

### Database Patterns

```go
type UserRepository struct {
    db *sql.DB
}

func (r *UserRepository) GetUser(ctx context.Context, id int) (*User, error) {
    query := "SELECT id, name, email FROM users WHERE id = $1"
    
    var user User
    err := r.db.QueryRowContext(ctx, query, id).Scan(
        &user.ID,
        &user.Name,
        &user.Email,
    )
    if err != nil {
        if errors.Is(err, sql.ErrNoRows) {
            return nil, fmt.Errorf("user not found: %d", id)
        }
        return nil, fmt.Errorf("querying user: %w", err)
    }

    return &user, nil
}
```

## Anti-Patterns to Avoid

### Forbidden Patterns
- ❌ Ignoring errors: `result, _ := operation()`
- ❌ Using panic for normal error flow
- ❌ Not using context for cancellation
- ❌ Goroutines without proper lifecycle management
- ❌ Not checking for nil pointers

### Memory and Performance
```go
// Bad: Creating unnecessary goroutines
for i := 0; i < 1000000; i++ {
    go func(i int) {
        process(i)
    }(i)
}

// Good: Worker pool pattern
const numWorkers = 10
jobs := make(chan int, 100)
var wg sync.WaitGroup

for w := 0; w < numWorkers; w++ {
    wg.Add(1)
    go worker(jobs, &wg)
}

for i := 0; i < 1000000; i++ {
    jobs <- i
}
close(jobs)
wg.Wait()
```

## Performance Tips

### Build Configuration
```bash
# Production builds
CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o main .

# With optimizations
go build -ldflags="-s -w" -o main .
```

### Runtime Performance
- Use `sync.Pool` for frequently allocated objects
- Prefer `strings.Builder` over string concatenation
- Use buffered channels appropriately
- Profile before optimizing: `go tool pprof`

## Dependency Management

```bash
# Initialize module
go mod init github.com/user/project

# Add dependencies
go get github.com/gorilla/mux@v1.8.0

# Update dependencies
go get -u ./...

# Vendor dependencies
go mod vendor

# Verify dependencies
go mod verify
```

## PRP Integration

When creating Go PRPs:
1. Always include `go.mod` requirements
2. Specify exact module versions
3. Include necessary build tags
4. Document any CGO requirements
5. Provide comprehensive error handling examples

### Validation Template

```bash
# Include in every Go PRP
go build ./...
go vet ./...
go test ./...
go test -race ./...
gofmt -d .
golangci-lint run
```

This ensures consistent, idiomatic, and reliable Go code across all projects.