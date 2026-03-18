# Go Cheatsheet

Quick reference for Go (Golang) from beginner to advanced topics.

---

## Table of Contents
- [Introduction](#introduction)
- [Setup](#setup)
- [Language Basics](#language-basics)
- [Data Types](#data-types)
- [Composite Types](#composite-types)
- [Control Flow](#control-flow)
- [Functions](#functions)
- [Pointers](#pointers)
- [Memory Management](#memory-management)
- [Methods and Interfaces](#methods-and-interfaces)
- [Generics](#generics)
- [Error Handling](#error-handling)
- [Code Organization](#code-organization)
- [Concurrency](#concurrency)
- [Standard Library](#standard-library)
- [Testing and Benchmarking](#testing-and-benchmarking)
- [Ecosystem and Libraries](#ecosystem-and-libraries)
- [Go Toolchain and Commands](#go-toolchain-and-commands)
- [Code Quality and Analysis](#code-quality-and-analysis)
- [Security](#security)
- [Performance and Debugging](#performance-and-debugging)
- [Deployment and Tooling](#deployment-and-tooling)
- [Advanced Topics](#advanced-topics)
- [DevOps Integration](#devops-integration)
- [Related Topics](#related-topics)

---

## Introduction

### Introduction to Go
- Go is a compiled, statically typed language designed for simplicity, performance, and concurrency.
- It is widely used for backend services, cloud-native systems, and CLI tools.

### Why use Go
- Fast compilation and execution.
- Built-in concurrency primitives (goroutines, channels).
- Small language surface and excellent standard library.

### History of Go
- Created at Google by Robert Griesemer, Rob Pike, and Ken Thompson.
- Open-sourced in 2009.
- Focus: developer productivity + scalable systems programming.

---

## Setup

### Setting up the Environment
```bash
go version
go env
```
Install from official site and ensure `go` is in PATH.

### Hello World in Go
```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, Go")
}
```

### `go` command
- Main entry for building, testing, formatting, modules, docs.
- Run `go help` for command list.

---

## Language Basics

### Variables and Constants

#### `var` vs `:=`
```go
var a int = 10
b := 20 // short declaration inside function only
```

#### Zero Values
- Numeric: `0`
- Bool: `false`
- String: `""`
- Pointer/slice/map/function/interface: `nil`

#### `const` and `iota`
```go
const Pi = 3.14

const (
    Sunday = iota
    Monday
    Tuesday
)
```

#### Scope and Shadowing
```go
x := 10
{
    x := 20 // shadows outer x
    _ = x
}
```

---

## Data Types

### Boolean
```go
var ok bool = true
```

### Numeric Types

#### Integers (Signed, Unsigned)
- Signed: `int`, `int8`, `int16`, `int32`, `int64`
- Unsigned: `uint`, `uint8`, `uint16`, `uint32`, `uint64`, `uintptr`

#### Floating Points
- `float32`, `float64`

#### Complex Numbers
- `complex64`, `complex128`

```go
var i int64 = -9
var u uint = 9
var f float64 = 3.14
var c complex128 = complex(2, 3)
```

### Runes
- `rune` is alias for `int32`, represents Unicode code point.

```go
var ch rune = '₹'
```

### Strings

#### Raw String Literals
- Backticks, preserves formatting and escapes as-is.

```go
raw := `line1
line2`
```

#### Interpreted String Literals
- Double quotes, supports escapes.

```go
s := "line1\nline2"
```

### Type Conversion
```go
var x int = 42
var y float64 = float64(x)
```

---

## Composite Types

### Arrays
```go
arr := [3]int{1, 2, 3}
```
Fixed length, value type.

### Slices
```go
s := []int{1, 2, 3}
s = append(s, 4)
```

#### Capacity and Growth
- Slice has `len` and `cap`.
- Appending may allocate new backing array.

```go
fmt.Println(len(s), cap(s))
```

#### `make()`
```go
buf := make([]byte, 0, 1024)
```

#### Slice to Array Conversion
```go
sl := []int{1, 2, 3}
arr2 := [3]int(sl) // requires exact length
_ = arr2
```

#### Array to Slice Conversion
```go
arr3 := [4]int{1, 2, 3, 4}
sl2 := arr3[1:3]
_ = sl2
```

### Maps
```go
m := map[string]int{"go": 1}
m["ts"] = 2
```

#### Comma-Ok Idiom
```go
v, ok := m["go"]
fmt.Println(v, ok)
```

### Structs
```go
type User struct {
    ID   int
    Name string
}

u := User{ID: 1, Name: "Asha"}
```

#### Struct Tags and JSON
```go
type Payload struct {
    UserID int    `json:"user_id"`
    Name   string `json:"name"`
}
```

#### Embedding Structs
```go
type Address struct { City string }
type Employee struct {
    Name string
    Address
}
```

---

## Control Flow

### Conditionals

#### `if`
```go
if x > 0 {
    fmt.Println("positive")
}
```

#### `if-else`
```go
if x%2 == 0 {
    fmt.Println("even")
} else {
    fmt.Println("odd")
}
```

#### `switch`
```go
switch day {
case 1:
    fmt.Println("Mon")
default:
    fmt.Println("Other")
}
```

### Loops

#### `for`
```go
for i := 0; i < 3; i++ {
    fmt.Println(i)
}
```

#### `for range`
```go
nums := []int{10, 20}
for i, v := range nums {
    fmt.Println(i, v)
}
```

#### Iterating Maps
```go
for k, v := range m {
    fmt.Println(k, v)
}
```

#### Iterating Strings
```go
for i, r := range "Go₹" {
    fmt.Println(i, r)
}
```

### `break`
```go
for {
    break
}
```

### `continue`
```go
for i := 0; i < 3; i++ {
    if i == 1 { continue }
    fmt.Println(i)
}
```

### `goto` (discouraged)
```go
// avoid unless very specific low-level flow needs
```

---

## Functions

### Functions Basics
```go
func add(a, b int) int {
    return a + b
}
```

### Variadic Functions
```go
func sum(nums ...int) int {
    total := 0
    for _, n := range nums { total += n }
    return total
}
```

### Multiple Return Values
```go
func divmod(a, b int) (int, int) {
    return a / b, a % b
}
```

### Anonymous Functions
```go
fn := func(x int) int { return x * x }
```

### Closures
```go
func counter() func() int {
    n := 0
    return func() int {
        n++
        return n
    }
}
```

### Named Return Values
```go
func split(sum int) (x, y int) {
    x = sum * 4 / 9
    y = sum - x
    return
}
```

### Call by Value
- Go passes arguments by value.
- For shared mutation, pass pointers or reference-like types (slice/map/channel).

---

## Pointers

### Pointers Basics
```go
x := 10
p := &x
*p = 20
```

### Pointers with Structs
```go
type Point struct { X int }
pt := &Point{X: 1}
pt.X = 2 // shorthand for (*pt).X
```

### With Maps and Slices
- Maps and slices are descriptors pointing to runtime data.
- Mutations in function are visible to caller.

```go
func push(s []int) []int { return append(s, 1) }
```

---

## Memory Management

### Memory Management
- Stack for short-lived local values.
- Heap for values escaping function scope.

### Garbage Collection
- Go uses concurrent garbage collector.
- Reduce allocations in hot paths for performance.

---

## Methods and Interfaces

### Methods vs Functions
- Methods have receiver.
- Functions do not.

```go
type Rect struct{ W, H float64 }
func (r Rect) Area() float64 { return r.W * r.H }
```

### Pointer Receivers
- Use when mutating receiver or avoiding large copies.

```go
func (r *Rect) Scale(f float64) { r.W *= f; r.H *= f }
```

### Value Receivers
- Use for small immutable-like values.

### Interfaces
```go
type Stringer interface {
    String() string
}
```

### Empty Interfaces
- `any` is alias for `interface{}`.
- Can hold any value.

### Embedding Interfaces
```go
type Reader interface { Read([]byte) (int, error) }
type Closer interface { Close() error }
type ReadCloser interface {
    Reader
    Closer
}
```

### Type Assertions
```go
var v any = "go"
s, ok := v.(string)
fmt.Println(s, ok)
```

### Type Switch
```go
switch t := v.(type) {
case string:
    fmt.Println("string", t)
case int:
    fmt.Println("int", t)
default:
    fmt.Println("unknown")
}
```

### Interfaces Basics
- Satisfied implicitly (no `implements` keyword).
- Design small behavior-focused interfaces.

---

## Generics

### Why Generics?
- Reuse type-safe logic without duplication.

### Generic Functions
```go
func First[T any](items []T) T {
    return items[0]
}
```

### Generic Types and Interfaces
```go
type Stack[T any] struct { items []T }
func (s *Stack[T]) Push(v T) { s.items = append(s.items, v) }
```

### Type Constraints
```go
type Number interface {
    ~int | ~int64 | ~float64
}
func Add[T Number](a, b T) T { return a + b }
```

### Type Inference
```go
fmt.Println(Add(2, 3)) // compiler infers T as int
```

---

## Error Handling

### Error Handling Basics
- Functions return `error` as last return value.

```go
func mayFail(ok bool) error {
    if !ok { return errors.New("failed") }
    return nil
}
```

### `error` interface
```go
type error interface {
    Error() string
}
```

### `errors.New`
```go
err := errors.New("not found")
```

### `fmt.Errorf`
```go
err := fmt.Errorf("user %d: %w", 42, err)
```

### Wrapping and Unwrapping Errors
```go
if errors.Is(err, os.ErrNotExist) { /* ... */ }
var pathErr *os.PathError
if errors.As(err, &pathErr) { /* ... */ }
```

### Sentinel Errors
```go
var ErrClosed = errors.New("closed")
```

### `panic` and `recover`
```go
defer func() {
    if r := recover(); r != nil {
        fmt.Println("recovered:", r)
    }
}()
panic("boom")
```
Use sparingly for truly unrecoverable states.

### Stack Traces and Debugging
- `panic` shows stack trace.
- Use `%+v` with wrapped errors in compatible libs.

---

## Code Organization

### Modules and Dependencies

#### `go mod init`
```bash
go mod init example.com/myapp
```

#### `go mod tidy`
```bash
go mod tidy
```

#### `go mod vendor`
```bash
go mod vendor
```

### Packages
- One directory usually equals one package.
- Package name should be short and lowercase.

### Package Import Rules
- Exported identifiers start with uppercase letter.
- Unused imports cause compile error.

### Using 3rd Party Packages
```bash
go get github.com/google/uuid
```

### Publishing Modules
- Use semantic version tags (`v1.2.0`).
- Prefer stable API before `v1`.

---

## Concurrency

### Goroutines
```go
go func() {
    fmt.Println("running concurrently")
}()
```

### Channels
```go
ch := make(chan int)
go func() { ch <- 42 }()
fmt.Println(<-ch)
```

#### Buffered vs Unbuffered
```go
buf := make(chan int, 2)
buf <- 1
buf <- 2
```
- Unbuffered syncs sender/receiver directly.
- Buffered allows limited queueing.

### Select Statement
```go
select {
case msg := <-ch:
    fmt.Println(msg)
case <-time.After(time.Second):
    fmt.Println("timeout")
}
```

### Worker Pools
```go
jobs := make(chan int, 10)
results := make(chan int, 10)
for w := 0; w < 3; w++ {
    go func() {
        for j := range jobs { results <- j * 2 }
    }()
}
```

### `sync` Package

#### Mutexes
```go
var mu sync.Mutex
mu.Lock()
// critical section
mu.Unlock()
```

#### WaitGroups
```go
var wg sync.WaitGroup
wg.Add(1)
go func() {
    defer wg.Done()
}()
wg.Wait()
```

### `context` Package

#### Deadlines and Cancellations
```go
ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
defer cancel()
_ = ctx
```

### Common Usecases
- Request-scoped cancellation.
- Background job workers.
- Concurrent IO-bound tasks.

### Concurrency Patterns

#### fan-in
- Merge multiple channels into one.

#### fan-out
- Multiple workers consume from one jobs channel.

#### pipeline
- Chain stages: producer -> transformer -> consumer.

### Race Detection
```bash
go test -race ./...
go run -race main.go
```

---

## Standard Library

### I/O and File Handling
```go
data, err := os.ReadFile("a.txt")
if err != nil { /* handle */ }
_ = data
```

### `flag`
```go
name := flag.String("name", "guest", "username")
flag.Parse()
fmt.Println(*name)
```

### `time`
```go
now := time.Now()
fmt.Println(now.Format(time.RFC3339))
```

### `encoding/json`
```go
b, _ := json.Marshal(map[string]int{"a": 1})
```

### `os`
- Process args, env vars, files, exit handling.

### `bufio`
```go
scanner := bufio.NewScanner(os.Stdin)
for scanner.Scan() {
    fmt.Println(scanner.Text())
}
```

### `slog`
```go
logger := slog.Default()
logger.Info("app started")
```

### `regexp`
```go
re := regexp.MustCompile(`^go`)
fmt.Println(re.MatchString("golang"))
```

### `go:embed`
```go
//go:embed static/*
var assets embed.FS
```

---

## Testing and Benchmarking

### `testing` package basics
```go
func TestAdd(t *testing.T) {
    if add(1, 2) != 3 { t.Fatal("expected 3") }
}
```

### Table-driven Tests
```go
cases := []struct{ a, b, want int }{{1,2,3},{2,3,5}}
```

### Mocks and Stubs
- Prefer interface-based design for test doubles.

### `httptest` for HTTP Tests
```go
req := httptest.NewRequest(http.MethodGet, "/", nil)
rr := httptest.NewRecorder()
handler(rr, req)
```

### Benchmarks
```go
func BenchmarkAdd(b *testing.B) {
    for i := 0; i < b.N; i++ { _ = add(1, 2) }
}
```

### Coverage
```bash
go test -cover ./...
go test -coverprofile=cover.out ./...
go tool cover -html=cover.out
```

---

## Ecosystem and Libraries

### Building CLIs

#### Cobra
- Popular for production CLI apps.

#### urfave/cli
- Simpler CLI framework.

#### bubbletea
- TUI framework for terminal apps.

### Web Development

#### `net/http` (standard)
```go
http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("ok"))
})
```

#### Frameworks (gin, echo, fiber, beego)
- Choose based on ergonomics, middleware ecosystem, and performance tradeoffs.

### gRPC and Protocol Buffers
- Strongly typed high-performance service communication.

### ORMs and DB Access (pgx, GORM)
- `pgx` for performant Postgres access.
- `GORM` for ORM patterns.

### Logging (Zerolog, Zap)
- Structured logging with high performance.

### Realtime Communication (Melody, Centrifugo)
- WebSocket/pub-sub based realtime systems.

---

## Go Toolchain and Commands

```bash
go run main.go
go build ./...
go install ./...
go fmt ./...
go mod tidy
go test ./...
go clean -cache
go doc fmt.Println
go version
```

---

## Code Quality and Analysis

### `go vet`
```bash
go vet ./...
```

### `goimports`
- Formats code and fixes imports.

```bash
go install golang.org/x/tools/cmd/goimports@latest
goimports -w .
```

### Linters

#### revive
- Style and code smell checks.

#### staticcheck
- Advanced static analysis.

#### golangci-lint
- Unified linter runner.

```bash
golangci-lint run
```

---

## Security

### `govulncheck`
```bash
go install golang.org/x/vuln/cmd/govulncheck@latest
govulncheck ./...
```

---

## Performance and Debugging

### `pprof`
- CPU/heap profiling.

```bash
go test -cpuprofile=cpu.out -bench=.
go tool pprof cpu.out
```

### `trace`
```bash
go test -trace=trace.out ./...
go tool trace trace.out
```

### Race Detector
```bash
go test -race ./...
```

---

## Deployment and Tooling

### Cross-compilation
```bash
GOOS=linux GOARCH=amd64 go build -o app-linux
GOOS=windows GOARCH=amd64 go build -o app.exe
```

### Building Executables
```bash
go build -o myapp ./cmd/myapp
```

---

## Advanced Topics

### Memory Mgmt. in Depth
- Allocation patterns, object lifetimes, pooling (`sync.Pool`), avoiding excessive GC pressure.

### Escape Analysis
```bash
go build -gcflags="-m" ./...
```
- Shows what escapes to heap.

### Reflection
```go
t := reflect.TypeOf(42)
fmt.Println(t.Kind())
```

### Unsafe Package
- `unsafe` bypasses type safety; use only when absolutely necessary.

### Build Constraints and Tags
```go
//go:build linux
```

### CGO Basics
- Interoperate with C code (`import "C"`).
- Adds build complexity and portability considerations.

### Compiler and Linker Flags
```bash
go build -ldflags="-s -w" -trimpath
```

### Plugins and Dynamic Loading
- `plugin` package supports dynamic loading on selected platforms.

---

## DevOps Integration

### Docker
```dockerfile
FROM golang:1.24 AS build
WORKDIR /app
COPY . .
RUN go build -o app .

FROM gcr.io/distroless/base-debian12
COPY --from=build /app/app /app
ENTRYPOINT ["/app"]
```

### Kubernetes
- Package service in container and deploy with Deployment + Service.
- Add liveness/readiness probes for production reliability.

---

## Related Topics
- Backend Roadmap
- DevOps Roadmap
- System Design
- Software Design and Architecture

---

## India-Focused Notes
- Go is widely used in Indian startups and fintech for APIs, microservices, and backend infra.
- For local setup reliability in restricted networks, set GOPROXY if needed:

```bash
go env -w GOPROXY=https://proxy.golang.org,direct
```

- Use UTC in backend systems and convert to IST at display layer when required.
