# Go Cheatsheet

Comprehensive end-to-end reference for Go (Golang) from runtime internals, GMP scheduler, and memory allocator to concurrency primitives, channels, interfaces, generics, web backends (`net/http`), database pooling, testing, and production microservices.

---

## Table of Contents
- [High Priority Topics](#high-priority-topics)
- [1 Go Runtime, Memory Allocation, and Escape Analysis](#1-go-runtime-memory-allocation-and-escape-analysis)
- [2 Toolchain, Modules, and Workspace](#2-toolchain-modules-and-workspace)
- [3 Variables, Constants, and Zero Values](#3-variables-constants-and-zero-values)
- [4 Data Types and Memory Representation](#4-data-types-and-memory-representation)
- [5 Arrays, Slices, and Memory Growth](#5-arrays-slices-and-memory-growth)
- [6 Maps, Structs, and Custom Types](#6-maps-structs-and-custom-types)
- [7 Control Flow and Functions](#7-control-flow-and-functions)
- [8 Pointers, Value vs Pointer Semantics](#8-pointers-value-vs-pointer-semantics)
- [9 Methods, Interfaces, and Duck Typing](#9-methods-interfaces-and-duck-typing)
- [10 Generics (Go 1.18+)](#10-generics-go-118)
- [11 Error Handling and Custom Error Types](#11-error-handling-and-custom-error-types)
- [12 Concurrency: Goroutines and the GMP Scheduler](#12-concurrency-goroutines-and-the-gmp-scheduler)
- [13 Channels, Select, and Synchronization](#13-channels-select-and-synchronization)
- [14 Context Package (Cancellation & Timeouts)](#14-context-package-cancellation--timeouts)
- [15 Standard Library Powerhouses](#15-standard-library-powerhouses)
- [16 Production Web Services & Layered Architecture](#16-production-web-services--layered-architecture)
- [17 Database Access (database/sql and Pooling)](#17-database-access-databasesql-and-pooling)
- [18 Testing, Benchmarks, and Profiling](#18-testing-benchmarks-and-profiling)
- [19 High-Yield Interview Questions and Reality Check](#19-high-yield-interview-questions-and-reality-check)

---

## High Priority Topics

Most asked in Go interviews and backend cloud-native architecture:
1. **Goroutines vs OS Threads & The GMP Scheduler Model (`G`, `M`, `P`)**
2. **Channel Mechanics (Buffered vs Unbuffered, Channel State Matrix: send/recv/close)**
3. **Slice Internals: Header `(ptr, len, cap)`, Capacity Doubling & Sub-slice Memory Leaks**
4. **The `nil` Interface Trap: `iface` vs `eface` (`(*Type)(nil) != nil`)**
5. **Escape Analysis (`go build -gcflags="-m"`) & Stack vs Heap Allocation**
6. **Value Receivers vs Pointer Receivers on Methods**
7. **`sync` Primitives (`WaitGroup`, `Mutex`, `RWMutex`, `Once`, `sync.Pool`)**
8. **Error Wrapping (`fmt.Errorf("%w", err)`), `errors.Is()`, `errors.As()`**
9. **`context.Context` Propagation, Cancellation, and Timeout Trees**
10. **`database/sql` Connection Pool Tuning (`SetMaxOpenConns`, `SetMaxIdleConns`)**

---

## 1 Go Runtime, Memory Allocation, and Escape Analysis

### Go Runtime Architecture
Go binaries include a lightweight runtime embedded into the compiled machine code:
- **TCMalloc-based Allocator**: Allocates memory without centralized global lock bottlenecks using `mcache` (per-P local cache), `mcentral` (shared span lists), and `mheap` (page allocator).
- **Tri-Color Mark-and-Sweep Garbage Collector**:
  - **White**: Unvisited objects (candidates for collection).
  - **Grey**: Visited objects whose children have not yet been scanned.
  - **Black**: Visited objects and all reachable children (guaranteed alive).
  - Runs concurrently alongside application goroutines with Write Barriers, resulting in sub-millisecond Stop-The-World (STW) pauses.

### Escape Analysis
Escape analysis is a compile-time optimization that determines whether a variable can be safely placed on the **stack** (cheap, freed on function return) or must "escape" to the **heap** (managed by GC).

```bash
# Run compiler escape analysis diagnostics
go build -gcflags="-m -m" main.go
```

```go
package main

type User struct {
    ID   int
    Name string
}

// Escapes to Heap: Function returns pointer to locally created struct
func NewUserHeap(name string) *User {
    u := User{ID: 1, Name: name} // 'u' escapes to heap because address is returned
    return &u
}

// Stays on Stack: Value returned by copy
func NewUserStack(name string) User {
    u := User{ID: 1, Name: name} // 'u' stays on stack
    return u
}
```

---

## 2 Toolchain, Modules, and Workspace

### Essential Go CLI Commands
```bash
# Initialize and manage modules
go mod init github.com/user/my-app
go mod tidy           # Adds missing and removes unused modules
go mod download
go mod vendor         # Vendors dependencies locally into /vendor

# Build and Run
go run main.go
go build -v -o bin/server cmd/server/main.go

# Production cross-compilation (Zero C dependencies)
CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -ldflags="-s -w" -o bin/server-linux main.go

# Code verification
go vet ./...
go test -v -race ./... # Always run tests with race detector enabled!
```

### Multi-Module Workspaces (`go.work` - Go 1.18+)
```bash
go work init ./core ./api
go work use ./shared
```

---

## 3 Variables, Constants, and Zero Values

### Default Zero Values
| Type | Default Zero Value |
| :--- | :--- |
| `int`, `int64`, `float64`, `byte`, `rune` | `0` / `0.0` |
| `bool` | `false` |
| `string` | `""` (Empty string) |
| Pointers, Slices, Maps, Channels, Interfaces, Functions | **`nil`** |
| Structs | Struct with each field initialized to its zero value |

### Variable Declarations & `iota` Enums
```go
package main

import "fmt"

// Package-level constants with iota
type Status int

const (
    StatusPending Status = iota // 0
    StatusActive                // 1
    StatusSuspended             // 2
    StatusDeleted               // 3
)

// Bitmask flags with iota
const (
    ReadPermission  = 1 << iota // 1 (1 << 0)
    WritePermission             // 2 (1 << 1)
    ExecPermission              // 4 (1 << 2)
)

func main() {
    var a int = 10       // Explicit type
    var b = 20           // Type inferred
    c := 30              // Short declaration (Inside functions only)
    fmt.Println(a, b, c, StatusActive)
}
```

---

## 4 Data Types and Memory Representation

### Primitive Types
- **Integers**: `int8`, `int16`, `int32` (`rune`), `int64`, `int` (platform-dependent 32/64-bit).
- **Unsigned**: `uint8` (`byte`), `uint16`, `uint32`, `uint64`, `uintptr` (pointer-sized uint).
- **Floats & Complex**: `float32`, `float64`, `complex64`, `complex128`.
- **Strings**: Immutable sequence of arbitrary bytes (typically UTF-8).

### Memory Headers: `String` vs `Slice`
```
String Header (16 bytes on 64-bit):
[ Pointer to Byte Array (8B) | Length (8B) ]

Slice Header (24 bytes on 64-bit):
[ Pointer to Element Array (8B) | Length (8B) | Capacity (8B) ]
```

---

## 5 Arrays, Slices, and Memory Growth

### Arrays vs Slices
- **Array (`[N]T`)**: Fixed-size, value-typed (copying an array duplicates the entire underlying memory block).
- **Slice (`[]T`)**: Dynamic reference view pointing to an underlying array.

```go
package main

import "fmt"

func main() {
    // 1. Array (Fixed)
    var arr = [3]int{1, 2, 3}

    // 2. Slice creation with make(type, len, cap)
    s := make([]int, 2, 5) // len=2, cap=5 -> [0, 0]
    s[0] = 10
    s[1] = 20

    // 3. append: Automatically allocates new array when len exceeds cap
    s = append(s, 30, 40, 50) // len=5, cap=5
    s = append(s, 60)         // Capacity doubles! (cap=10)

    // 4. Sub-slicing: Shares the same backing array!
    sub := s[1:3]             // [20, 30], len=2, cap=9
    sub[0] = 999              // Mutates s[1] as well!
    fmt.Println("Original s[1]:", s[1]) // 999
}
```

> [!CAUTION]
> **Sub-slice Memory Leak**: Slicing a small portion of a massive array keeps the entire large array pinned in memory. Use `copy()` to detach the small slice.

---

## 6 Maps, Structs, and Custom Types

### Hash Maps (`map[K]V`)
- Unordered, hash-table based. Not safe for concurrent writes (causes fatal runtime panic).

```go
// Creation and Comma-OK Idiom
userAges := make(map[string]int)
userAges["Alice"] = 28

// Comma-ok pattern to distinguish zero-value from missing key
if age, exists := userAges["Bob"]; exists {
    fmt.Println("Bob's age:", age)
} else {
    fmt.Println("Bob does not exist in map")
}

delete(userAges, "Alice") // Safe delete (no-op if key missing)
```

### Structs, Embedding, and Tags
```go
type BaseEntity struct {
    ID        string `json:"id"`
    CreatedAt int64  `json:"created_at"`
}

// Composition via Struct Embedding (promotes fields and methods)
type Account struct {
    BaseEntity        // Embedded struct
    Username   string `json:"username"`
    Email      string `json:"email"`
    isActive   bool   // unexported private field
}

func main() {
    acc := Account{
        BaseEntity: BaseEntity{ID: "acc_1", CreatedAt: 1700000000},
        Username:   "navnath",
        Email:      "navnath@example.com",
    }
    // Access promoted field directly:
    fmt.Println(acc.ID, acc.Username)
}
```

---

## 7 Control Flow and Functions

### `if` with Short Statement & `switch`
```go
// Variable scoped only to the if/else block
if err := executeTask(); err != nil {
    fmt.Printf("Task failed: %v\n", err)
}

// Type Switch
func inspectType(val any) {
    switch v := val.(type) {
    case int:
        fmt.Println("Integer:", v)
    case string:
        fmt.Println("String:", v)
    default:
        fmt.Println("Other type")
    }
}
```

### `defer` LIFO Execution Order
`defer` pushes function calls onto a stack; executed in Last-In-First-Out order when surrounding function returns.

```go
func deferExample() (result int) {
    defer fmt.Println("1st defer")
    defer fmt.Println("2nd defer")

    // Defer can modify named return values!
    defer func() {
        result += 10
    }()

    return 5 // Returns 15!
}
```

---

## 8 Pointers, Value vs Pointer Semantics

### Receiver Types on Methods
```go
type Counter struct {
    count int
}

// 1. Value Receiver: Operates on a COPY (Original is NOT modified)
func (c Counter) ValueIncrement() {
    c.count++
}

// 2. Pointer Receiver: Operates on ORIGINAL memory address
func (c *Counter) PointerIncrement() {
    c.count++
}
```

### Guidelines: When to use Pointer vs Value Receivers
| Scenario | Receiver Choice |
| :--- | :--- |
| Method needs to mutate struct state | **Pointer Receiver (`*T`)** |
| Struct contains `sync.Mutex` or cannot be copied | **Pointer Receiver (`*T`)** |
| Large struct (avoiding expensive copy on each call) | **Pointer Receiver (`*T`)** |
| Small, immutable data structures (e.g. `time.Time`, 2D Point) | **Value Receiver (`T`)** |
| Consistency: If some methods require pointer, use pointer for all | **Pointer Receiver (`*T`)** |

---

## 9 Methods, Interfaces, and Duck Typing

### Implicit Interfaces
Types implement interfaces implicitly by implementing their method signatures—no `implements` keyword.

```go
type Reader interface {
    Read(p []byte) (n int, err error)
}

type Writer interface {
    Write(p []byte) (n int, err error)
}

// Interface Composition
type ReadWriter interface {
    Reader
    Writer
}
```

### The `nil` Interface Trap
An interface in Go is represented as a 2-word tuple: `(type, value)`. An interface is `nil` **ONLY if both `type` and `value` are nil**.

```go
type CustomError struct{}
func (c *CustomError) Error() string { return "fail" }

func getError(shouldFail bool) error {
    var err *CustomError = nil
    if shouldFail {
        err = &CustomError{}
    }
    return err // TRAP: Returns interface with type=*CustomError and value=nil!
}

func main() {
    err := getError(false)
    if err != nil {
        fmt.Println("BUG: err is NOT nil because type is populated!")
    }
}
```

---

## 10 Generics (Go 1.18+)

### Generic Functions & Type Constraints
```go
package main

import "fmt"

// 'comparable' allows == and != comparisons
func IndexOf[T comparable](slice []T, target T) int {
    for i, item := range slice {
        if item == target {
            return i
        }
    }
    return -1
}

// Custom Union Constraint with tilde (~) for underlying types
type Number interface {
    ~int | ~int64 | ~float32 | ~float64
}

func Sum[T Number](numbers []T) T {
    var total T
    for _, num := range numbers {
        total += num
    }
    return total
}
```

---

## 11 Error Handling and Custom Error Types

### Idiomatic Error Wrapping (`fmt.Errorf("%w")`)
```go
package main

import (
    "errors"
    "fmt"
)

var ErrNotFound = errors.New("record not found")

type DatabaseError struct {
    Query string
    Err   error
}

func (e *DatabaseError) Error() string {
    return fmt.Sprintf("db query failed: %s: %v", e.Query, e.Err)
}

func (e *DatabaseError) Unwrap() error {
    return e.Err
}

func findUser(id string) error {
    return fmt.Errorf("findUser failed: %w", ErrNotFound) // %w wraps error
}

func main() {
    err := findUser("u_10")

    // Check error chain with errors.Is
    if errors.Is(err, ErrNotFound) {
        fmt.Println("Handling missing user error specifically")
    }
}
```

---

## 12 Concurrency: Goroutines and the GMP Scheduler

### The GMP Scheduler Architecture
```
┌────────────────────────────────────────────────────────┐
│ G (Goroutine): Lightweight thread (2KB initial stack)  │
│ M (Machine): OS Kernel Thread                          │
│ P (Processor): Logical Context / Resource Manager      │
│                (Count matches GOMAXPROCS)              │
└────────────────────────────────────────────────────────┘

    [Global Run Queue]
           │
     ┌─────┴─────┐
     ▼           ▼
  [Local RQ]  [Local RQ]  <── (Work-Stealing Algorithm steals 50% from peers)
     │           │
   ┌─┴─┐       ┌─┴─┐
   │ P │       │ P │
   └─┬─┘       └─┬─┘
     │           │
   ┌─┴─┐       ┌─┴─┐
   │ M │       │ M │  <── (Bound to hardware CPU cores)
   └─┬─┘       └─┬─┘
     │           │
   ┌─┴─┐       ┌─┴─┐
   │ G │       │ G │  <── (Executing current task)
   └───┘       └───┘
```

---

## 13 Channels, Select, and Synchronization

### Channel Operations & State Behavior Matrix
| Channel State | Send (`ch <- v`) | Receive (`<-ch`) | Close (`close(ch)`) |
| :--- | :--- | :--- | :--- |
| **`nil`** | **Blocks forever** | **Blocks forever** | **PANIC** |
| **`open`** | Sends or blocks if full | Receives or blocks if empty | Closes channel |
| **`closed`** | **PANIC** | Yields remaining values, then **zero-value (`ok=false`)** | **PANIC** |

### Worker Pool Pattern
```go
package main

import (
    "fmt"
    "sync"
)

func worker(id int, jobs <-chan int, results chan<- int, wg *sync.WaitGroup) {
    defer wg.Done()
    for j := range jobs { // Automatically drains until channel is closed
        results <- j * 2
    }
}

func main() {
    const numJobs = 5
    jobs := make(chan int, numJobs)
    results := make(chan int, numJobs)
    var wg sync.WaitGroup

    // Spawn 3 workers
    for w := 1; w <= 3; w++ {
        wg.Add(1)
        go worker(w, jobs, results, &wg)
    }

    // Send jobs
    for j := 1; j <= numJobs; j++ {
        jobs <- j
    }
    close(jobs) // Signal workers no more jobs

    wg.Wait()
    close(results)

    for res := range results {
        fmt.Println("Result:", res)
    }
}
```

---

## 14 Context Package (Cancellation & Timeouts)

```go
package main

import (
    "context"
    "fmt"
    "time"
)

func fetchData(ctx context.Context) error {
    select {
    case <-time.After(500 * time.Millisecond): // Simulate slow work
        fmt.Println("Data fetched successfully")
        return nil
    case <-ctx.Done(): // Triggered if timeout or cancel happens first
        return ctx.Err()
    }
}

func main() {
    // Timeout tree: Cancels automatically after 200ms
    ctx, cancel := context.WithTimeout(context.Background(), 200*time.Millisecond)
    defer cancel() // Always call cancel to prevent context leaks!

    if err := fetchData(ctx); err != nil {
        fmt.Println("Fetch aborted:", err) // context deadline exceeded
    }
}
```

---

## 15 Standard Library Powerhouses

### Modern `net/http` Server (Go 1.22+ Path Parameters)
```go
package main

import (
    "encoding/json"
    "net/http"
)

type UserResponse struct {
    ID   string `json:"id"`
    Name string `json:"name"`
}

func main() {
    mux := http.NewServeMux()

    // Go 1.22+ enhanced routing with HTTP methods and path variables
    mux.HandleFunc("GET /api/users/{id}", func(w http.ResponseWriter, r *http.Request) {
        userID := r.PathValue("id")

        w.Header().Set("Content-Type", "application/json")
        w.WriteHeader(http.StatusOK)
        json.NewEncoder(w).Encode(UserResponse{
            ID:   userID,
            Name: "Navnath",
        })
    })

    server := &http.Server{
        Addr:    ":8080",
        Handler: mux,
    }
    server.ListenAndServe()
}
```

---

## 16 Production Web Services & Layered Architecture

### Standard Project Layout
```
my-go-service/
├── cmd/
│   └── api/
│       └── main.go       # App entry point
├── internal/             # Private application code (enforced by Go compiler)
│   ├── handler/          # HTTP transport layer
│   ├── service/          # Business logic layer
│   ├── repository/       # Database queries & storage
│   └── model/            # Domain structs
├── pkg/                  # Public library code safe for other projects to import
├── go.mod
└── go.sum
```

---

## 17 Database Access (database/sql and Pooling)

```go
package main

import (
    "context"
    "database/sql"
    "time"
)

func InitDB(driverName, dataSourceName string) (*sql.DB, error) {
    db, err := sql.Open(driverName, dataSourceName)
    if err != nil {
        return nil, err
    }

    // Critical Connection Pool Tuning for Production
    db.SetMaxOpenConns(25)                 // Maximum active connections
    db.SetMaxIdleConns(25)                 // Maximum idle connections in pool
    db.SetConnMaxLifetime(5 * time.Minute) // Maximum connection reuse duration
    db.SetConnMaxIdleTime(2 * time.Minute)

    ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
    defer cancel()

    if err := db.PingContext(ctx); err != nil {
        return nil, err
    }

    return db, nil
}
```

---

## 18 Testing, Benchmarks, and Profiling

### Table-Driven Tests & Benchmarks
```go
package mathutil

import "testing"

func Add(a, b int) int {
    return a + b
}

// Table-Driven Test
func TestAdd(t *testing.T) {
    tests := []struct {
        name     string
        a, b     int
        expected int
    }{
        {"positive", 2, 3, 5},
        {"negative", -1, -2, -3},
        {"zero", 0, 5, 5},
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            result := Add(tt.a, tt.b)
            if result != tt.expected {
                t.Errorf("Add(%d, %d) = %d; want %d", tt.a, tt.b, result, tt.expected)
            }
        })
    }
}

// Benchmark
func BenchmarkAdd(b *testing.B) {
    for i := 0; i < b.N; i++ {
        Add(100, 200)
    }
}
```

---

## 19 High-Yield Interview Questions and Reality Check

### 1. What happens during slice reallocation in Go?
> **Answer**: When `append()` exceeds a slice's `cap`, Go allocates a new larger backing array. In Go 1.18+, capacity doubles ($2\times$) for smaller slices (up to 256 elements), and transitions smoothly to a $1.25\times$ growth formula for larger slices. Existing elements are copied over, and the slice header pointer is updated to the new array.

### 2. Why is sending to a closed channel a panic, but receiving from a closed channel is not?
> **Answer**: Closing a channel indicates that **no more data will ever be sent**. Sending on a closed channel is a protocol violation and panics immediately. Receiving from a closed channel is safe because consumers need to drain any remaining buffered items; once empty, receives yield the type's zero value and `ok = false`.

### 3. Explain the GMP model in the Go runtime.
> **Answer**:
> - **G (Goroutine)**: Represents the goroutine with its stack and instruction pointer.
> - **M (Machine)**: Represents an OS kernel thread managed by the OS scheduler.
> - **P (Processor)**: Represents a logical context/processor required to execute Go code (`GOMAXPROCS`).
> A running `M` must hold a `P` to execute `G`s from its local run queue. If an `M` blocks in a syscall, `P` disassociates and migrates to another `M` (work-stealing scheduler).

### 4. What is the difference between `sync.Mutex` and `sync.RWMutex`?
> **Answer**: `sync.Mutex` provides exclusive mutual exclusion (only 1 thread can read or write at a time). `sync.RWMutex` allows **multiple concurrent readers (`RLock()`)** simultaneously as long as no writer holds the lock, but permits only **one exclusive writer (`Lock()`)**, drastically boosting performance for read-heavy shared state.

### 5. Why should you always run tests with the `-race` flag?
> **Answer**: The Go race detector instrumentates memory reads and writes during execution. It detects unsynchronized concurrent access to shared memory (data races) at runtime with minimal overhead ($2\times-10\times$), catching subtle concurrency bugs that are impossible to find via static analysis.

---

### Reality Check & Best Practices
- Never ignore returned `error` values with `_`.
- Always pass `context.Context` as the first parameter to functions performing I/O or network requests.
- Never use `time.Sleep()` for synchronization in tests; use `sync.WaitGroup`, channels, or polling.
- Always call `cancel()` on contexts created via `WithCancel` / `WithTimeout` in a `defer` block to prevent goroutine/timer leaks.
- Avoid global mutable state; inject dependencies via constructor functions (`NewService(...)`).
