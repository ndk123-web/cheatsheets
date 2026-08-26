# Rust Cheatsheet

Comprehensive reference for Rust from core language foundations to advanced systems, async runtimes, web backends (Axum), databases (SQLx), and production architecture.

---

## Table of Contents
- [High Priority Topics](#high-priority-topics)
- [1 Setup and Cargo Toolchain](#1-setup-and-cargo-toolchain)
- [2 Language Basics and Variables](#2-language-basics-and-variables)
- [3 Data Types and Memory Representation](#3-data-types-and-memory-representation)
- [4 Strings and Slices](#4-strings-and-slices)
- [5 Control Flow and Pattern Matching](#5-control-flow-and-pattern-matching)
- [6 Functions and Closures](#6-functions-and-closures)
- [7 Ownership, Borrowing, and Lifetimes](#7-ownership-borrowing-and-lifetimes)
- [8 Structs, Enums, and Methods](#8-structs-enums-and-methods)
- [9 Traits and Generics](#9-traits-and-generics)
- [10 Collections and Iterators](#10-collections-and-iterators)
- [11 Error Handling (Option, Result, Custom Errors)](#11-error-handling-option-result-custom-errors)
- [12 Smart Pointers and Interior Mutability](#12-smart-pointers-and-interior-mutability)
- [13 Modules, Packages, and Modern Imports](#13-modules-packages-and-modern-imports)
- [14 Attributes and Serialization (Serde)](#14-attributes-and-serialization-serde)
- [15 Async Rust and Tokio Runtime](#15-async-rust-and-tokio-runtime)
- [16 Backend Web Development with Axum](#16-backend-web-development-with-axum)
- [17 Database Access with SQLx](#17-database-access-with-sqlx)
- [18 CLI Applications and File I/O](#18-cli-applications-and-file-io)
- [19 Multithreading and Concurrency Primitives](#19-multithreading-and-concurrency-primitives)
- [20 Testing, Benchmarking, and Tooling](#20-testing-benchmarking-and-tooling)
- [21 Unsafe Rust and FFI](#21-unsafe-rust-and-ffi)
- [22 High-Yield Interview Questions and Reality Check](#22-high-yield-interview-questions-and-reality-check)

---

## High Priority Topics

Most asked in Rust interviews and core engineering:
1. **Ownership, Move Semantics, and Copy vs Move**
2. **Borrow Checker Rules (Aliasing XOR Mutability: `&T` vs `&mut T`)**
3. **Lifetimes (`'a`, lifetime elision, and struct lifetimes)**
4. **`String` vs `&str` and Slices (`&[T]`)**
5. **Option `<T>` and Result `<T, E>` with the `?` Operator**
6. **Traits, Trait Bounds, and Static Dispatch (`impl Trait`) vs Dynamic Dispatch (`dyn Trait`)**
7. **Smart Pointers (`Box<T>`, `Rc<T>`, `Arc<T>`, `RefCell<T>`, `Mutex<T>`)**
8. **Async Runtime Mechanics (Tokio, Futures, async channels `mpsc`)**
9. **Axum Web Framework Layered Architecture & State Extraction**
10. **SQLx Connection Pools, Migrations & Async CRUD**

---

## 1 Setup and Cargo Toolchain

### Toolchain Management
Rust uses `rustup` for toolchains, `rustc` as compiler, and `cargo` as package manager and build system.

```bash
# Update toolchain
rustup update

# Check versions
rustc --version
cargo --version

# Add compilation targets or components
rustup target add x86_64-unknown-linux-musl
rustup component add clippy rustfmt
```

### Essential Cargo Commands
```bash
# Initialize a new binary or library project
cargo new my_app --bin
cargo new my_lib --lib

# Check code without generating binary (much faster)
cargo check

# Build and run
cargo build              # Debug mode (target/debug/)
cargo build --release    # Optimized production build (target/release/)
cargo run
cargo run --release

# Testing & Code quality
cargo test
cargo fmt --all
cargo clippy -- -D warnings

# Dependency management
cargo add tokio --features full
cargo add serde --features derive
cargo add serde_json
cargo add axum
cargo add sqlx --features "runtime-tokio-native-tls sqlite"
```

### `Cargo.toml` Structure
```toml
[package]
name = "my_app"
version = "0.1.0"
edition = "2021"

[dependencies]
tokio = { version = "1.0", features = ["full"] }
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0"
axum = "0.7"
sqlx = { version = "0.7", features = ["runtime-tokio-native-tls", "sqlite", "macros"] }
tracing = "0.1"
tracing-subscriber = "0.3"
thiserror = "1.0"
anyhow = "1.0"

[profile.release]
opt-level = 3
lto = true
codegen-units = 1
panic = "abort"
```

---

## 2 Language Basics and Variables

### Immutability by Default
In Rust, variables are immutable by default. To allow changes, declare with `mut`.

```rust
fn main() {
    let x = 5;       // Immutable: cannot reassign x = 6
    let mut y = 10;  // Mutable
    y += 5;

    println!("x = {x}, y = {y}");
}
```

### Constants vs Statics vs Let
| Feature | `let` / `let mut` | `const` | `static` / `static mut` |
| :--- | :--- | :--- | :--- |
| **Scope** | Block-scoped | Block or global | Global |
| **Type Annotation** | Optional (inferred) | **Mandatory** | **Mandatory** |
| **Evaluation** | Runtime | Compile-time constant | Fixed memory address |
| **Inlining** | Local stack/register | Inlined everywhere used | Single address in data segment |

```rust
const MAX_USERS: u32 = 100_000;
static SERVER_NAME: &str = "AXUM_PROD";
```

### Variable Shadowing
Shadowing allows redeclaring a variable with `let` in the same or inner scope, allowing type and mutability changes.

```rust
let spaces = "   ";          // type: &str
let spaces = spaces.len();    // type: usize (shadowed)
```

---

## 3 Data Types and Memory Representation

### Primitive Scalar Types
- **Integers**:
  - Signed: `i8`, `i16`, `i32` (default), `i64`, `i128`, `isize` (pointer-sized)
  - Unsigned: `u8` (byte), `u16`, `u32`, `u64`, `u128`, `usize` (indices/sizes)
- **Floating-point**: `f32`, `f64` (default IEEE-754)
- **Boolean**: `bool` (`true`, `false`) - 1 byte
- **Character**: `char` - **4 bytes** (represents any Unicode Scalar Value)

### Primitive Compound Types
```rust
// Tuples: fixed size, mixed types
let tuple: (i32, f64, u8) = (500, 6.4, 1);
let (x, y, z) = tuple;       // Destructuring
let first = tuple.0;          // Indexing

// Arrays: fixed size, homogeneous, allocated on STACK
let arr: [i32; 5] = [1, 2, 3, 4, 5];
let zeroes = [0; 100];        // 100 elements initialized to 0
let len = arr.len();
```

---

## 4 Strings and Slices

### `String` vs `&str`
| Property | `String` | `&str` (String Slice) |
| :--- | :--- | :--- |
| **Ownership** | Owns its heap buffer | Borrowed reference / view |
| **Memory** | Heap buffer (ptr + len + capacity on stack) | Pointer + length (fat pointer on stack) |
| **Mutability** | Growable / mutable via `mut` | Read-only view (or `&mut str` in-place) |
| **Allocation** | Dynamically allocated | Points to stack, heap, or `'static` binary data |

```rust
fn main() {
    // String (Heap allocated, growable)
    let mut s = String::from("Hello");
    s.push_str(", Rust!");

    // &str (String slice reference)
    let slice: &str = &s[0..5]; // "Hello"

    // String literal has type &'static str
    let literal: &'static str = "Static string in binary";
}
```

### Array Slices (`&[T]`)
A slice is a 2-word fat pointer `(pointer, length)` into contiguous data.

```rust
let nums = [10, 20, 30, 40, 50];
let slice: &[i32] = &nums[1..4]; // [20, 30, 40]
assert_eq!(slice.len(), 3);
```

---

## 5 Control Flow and Pattern Matching

### `if`/`else` as Expressions
In Rust, `if` is an expression that yields a value. Branches must evaluate to the same type.

```rust
let condition = true;
let number = if condition { 42 } else { 0 };
```

### Loops: `loop`, `while`, and `for`
```rust
// loop with break returning value
let mut counter = 0;
let result = loop {
    counter += 1;
    if counter == 10 {
        break counter * 2; // Returns 20
    }
};

// while loop
while counter > 0 {
    counter -= 1;
}

// for in loop (preferred, bounds-checked at compile time)
for item in [10, 20, 30].iter() {
    println!("{item}");
}

for i in 0..5 { // Range 0 to 4 (exclusive)
    println!("{i}");
}
```

### Pattern Matching (`match`)
Matches must be **exhaustive**.

```rust
enum Status {
    Ok,
    Pending(u32),
    Err { code: u16, msg: String },
}

fn handle_status(s: Status) {
    match s {
        Status::Ok => println!("All good"),
        Status::Pending(sec) if sec > 60 => println!("Long pending: {sec}s"),
        Status::Pending(sec) => println!("Pending: {sec}s"),
        Status::Err { code: 404, .. } => println!("Resource Not Found"),
        Status::Err { code, msg } => println!("Error {code}: {msg}"),
    }
}
```

### `if let`, `while let`, and `let-else`
```rust
// if let (concise handling for a single variant)
let config_val: Option<i32> = Some(100);
if let Some(val) = config_val {
    println!("Got value: {val}");
}

// let-else (Rust 1.65+ for early return guard clauses)
fn process(opt: Option<String>) {
    let Some(name) = opt else {
        println!("Missing name, returning");
        return;
    };
    println!("Processing: {name}");
}
```

---

## 6 Functions and Closures

### Functions
```rust
// Last expression without semicolon is the return value
fn add(a: i32, b: i32) -> i32 {
    a + b
}
```

### Closures
Anonymous functions that can capture variables from their enclosing scope.

```rust
let x = 10;

// Syntax: |params| -> ReturnType { body }
let add_x = |val| val + x;
println!("{}", add_x(5)); // 15
```

### Closure Traits and Capture Priority
Rust infers how to capture variables automatically:
1. `Fn`: Captures by **immutable reference** (`&T`). Can be called repeatedly concurrently.
2. `FnMut`: Captures by **mutable reference** (`&mut T`). Can modify captured values and be called multiple times.
3. `FnOnce`: Captures by **value (ownership move)** (`T`). Consumes captured values and can be called only once.

```rust
// move keyword forces closure to take ownership of captured variables
let text = String::from("Rust");
let consume = move || {
    println!("Consumed: {text}");
    drop(text); // text dropped here
};
consume();
// consume(); // Error: use of moved value
```

---

## 7 Ownership, Borrowing, and Lifetimes

### The 3 Core Ownership Rules
1. Each value in Rust has an **owner**.
2. There can only be **one owner at a time**.
3. When the owner goes out of scope, the value is **dropped** (`Drop::drop`).

```rust
let s1 = String::from("hello"); // Heap allocation
let s2 = s1;                   // MOVE: s1 is invalidated, s2 is now the owner
// println!("{}", s1);         // COMPILE ERROR: value borrowed here after move
```

### Copy Types vs Move Types
- Types that implement `Copy` live on the stack and duplicate bitwise on assignment (`i32`, `f64`, `bool`, `char`, `[T; N]` where `T: Copy`, tuples of Copy types).
- Types that manage heap resources (`String`, `Vec<T>`, `Box<T>`) implement `Clone` (explicit deep copy) and default to `Move`.

### Borrowing Rules (Aliasing XOR Mutability)
At any given time, you can have:
- **Any number of immutable references (`&T`)**, OR
- **Exactly one mutable reference (`&mut T`)**,
- **NEVER both simultaneously.**

```rust
let mut data = vec![1, 2, 3];

let r1 = &data;     // Immutable borrow
let r2 = &data;     // OK: multiple immutable borrows
println!("{r1:?}, {r2:?}"); // Non-Lexical Lifetimes (NLL): r1, r2 end here

let r3 = &mut data; // OK: previous immutable borrows are no longer active
r3.push(4);
```

### Lifetimes (`'a`)
Lifetimes ensure that references never outlive the data they point to (preventing dangling pointers).

```rust
// 'a specifies that the returned reference lives as long as the shorter of x and y
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() { x } else { y }
}

// Struct holding a reference must specify lifetime
struct Highlight<'a> {
    part: &'a str,
}
```

### Lifetime Elision Rules (Compiler automatically infers):
1. Each input reference parameter gets its own lifetime parameter.
2. If there is exactly one input lifetime, that lifetime is assigned to all output references.
3. If there are multiple input lifetimes and one is `&self` or `&mut self`, the lifetime of `self` is assigned to all output references.

---

## 8 Structs, Enums, and Methods

### Struct Definitions
```rust
// 1. Classic Struct
#[derive(Debug, Clone)]
pub struct User {
    pub id: u64,
    pub username: String,
    pub active: bool,
}

// 2. Tuple Struct
struct Color(u8, u8, u8);

// 3. Unit-like Struct (useful for markers and traits)
struct EmptyMarker;
```

### Method Implementations (`impl`)
```rust
impl User {
    // Associated function / Constructor (no self)
    pub fn new(id: u64, username: String) -> Self {
        Self { id, username, active: true }
    }

    // Immutable borrow method
    pub fn display_name(&self) -> &str {
        &self.username
    }

    // Mutable borrow method
    pub fn deactivate(&mut self) {
        self.active = false;
    }

    // Consuming method (takes ownership)
    pub fn destroy(self) {
        println!("User {} destroyed", self.username);
    }
}
```

### Enums with Data Payloads
```rust
#[derive(Debug, PartialEq)]
pub enum WebEvent {
    PageLoad,
    KeyPress(char),
    Click { x: i64, y: i64 },
    Paste(String),
}
```

---

## 9 Traits and Generics

### Defining and Implementing Traits
Traits define shared behavior (interfaces in other languages).

```rust
pub trait Summary {
    fn summarize(&self) -> String;

    // Default implementation
    fn format_header(&self) -> String {
        format!("--- {} ---", self.summarize())
    }
}

pub struct Article {
    pub headline: String,
    pub author: String,
}

impl Summary for Article {
    fn summarize(&self) -> String {
        format!("{} by {}", self.headline, self.author)
    }
}
```

### Static Dispatch (`impl Trait` / Generics) vs Dynamic Dispatch (`dyn Trait`)
| Feature | Static Dispatch (`impl Trait`, `T: Trait`) | Dynamic Dispatch (`Box<dyn Trait>`, `&dyn Trait`) |
| :--- | :--- | :--- |
| **Mechanism** | Monomorphization at compile time | Fat pointer with vtable lookup at runtime |
| **Performance** | Zero runtime cost, inlined | Minor pointer indirection overhead |
| **Binary Size** | Larger (code generated per concrete type) | Smaller |
| **Heterogeneous Collections** | No (single concrete type per invocation) | Yes (`Vec<Box<dyn Trait>>`) |

```rust
// Static dispatch
fn print_summary<T: Summary>(item: &T) {
    println!("{}", item.summarize());
}

// Dynamic dispatch (heterogeneous list)
fn print_all(items: &[Box<dyn Summary>]) {
    for item in items {
        println!("{}", item.summarize());
    }
}
```

### Essential Standard Traits
- `Debug` (`{:?}`) & `Display` (`{}`)
- `Clone` (explicit deep copy) & `Copy` (implicit bitwise copy)
- `Default` (`Default::default()`)
- `PartialEq` (`==`), `Eq`, `PartialOrd`, `Ord`
- `From<T>` & `Into<U>` (lossless type conversions)
- `Deref` & `DerefMut` (smart pointer dereferencing `*x`)
- `Drop` (destructor cleanup on scope exit)
- `Send` (safe to transfer across thread boundaries) & `Sync` (safe to share references between threads)

---

## 10 Collections and Iterators

### Core Collections
```rust
use std::collections::{HashMap, HashSet, VecDeque};

// 1. Vector (growable heap array)
let mut vec = vec![1, 2, 3];
vec.push(4);
vec.pop(); // Returns Option<T>

// 2. HashMap (key-value store)
let mut map: HashMap<String, u32> = HashMap::new();
map.insert("alice".to_string(), 100);

// Entry API (insert default if absent)
map.entry("bob".to_string()).or_insert(50);

// 3. HashSet (unique set)
let mut set = HashSet::new();
set.insert(42);
```

### Iterators and Functional Adapters
Rust iterators are **lazy** and compile down to zero-cost assembly.

- `iter()`: yields immutable references `&T`
- `iter_mut()`: yields mutable references `&mut T`
- `into_iter()`: yields owned values `T` (consumes the collection)

```rust
let numbers = vec![1, 2, 3, 4, 5, 6];

// Functional iterator pipeline
let sum_even_squares: i32 = numbers
    .iter()
    .filter(|&&x| x % 2 == 0)
    .map(|&x| x * x)
    .sum();

println!("Sum: {sum_even_squares}"); // 4 + 16 + 36 = 56

// Collect into a new Vector
let doubled: Vec<i32> = numbers.iter().map(|x| x * 2).collect();
```

---

## 11 Error Handling (Option, Result, Custom Errors)

### `Option<T>` (Absence of Value)
```rust
enum Option<T> {
    Some(T),
    None,
}

let name: Option<&str> = Some("Rustacean");
let len = name.map(|s| s.len()).unwrap_or(0);
```

### `Result<T, E>` and the `?` Operator
```rust
use std::fs::File;
use std::io::{self, Read};

fn read_username_from_file(path: &str) -> Result<String, io::Error> {
    let mut file = File::open(path)?; // Returns Err early if file doesn't exist
    let mut s = String::new();
    file.read_to_string(&mut s)?;
    Ok(s)
}
```

### Idiomatic Custom Errors with `thiserror` and `anyhow`
```rust
// Domain/Library Errors with thiserror
use thiserror::Error;

#[derive(Error, Debug)]
pub enum AppError {
    #[error("Database error: {0}")]
    Database(#[from] sqlx::Error),

    #[error("User not found with id: {0}")]
    NotFound(u64),

    #[error("Authentication failed: {0}")]
    Unauthorized(String),
}

// Application/Top-level Errors with anyhow
fn run_app() -> anyhow::Result<()> {
    // Allows returning any error with context
    std::fs::read_to_string("config.toml")
        .map_err(|e| anyhow::anyhow!("Failed reading config: {e}"))?;
    Ok(())
}
```

---

## 12 Smart Pointers and Interior Mutability

### Smart Pointer Reference Guide
| Smart Pointer | Heap Allocation | Reference Count | Mutability | Thread Safe | Common Use Case |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `Box<T>` | Yes | 1 (Single Owner) | Standard (`mut`) | Yes (`Send`/`Sync`) | Recursive types, large stack offloading, trait objects (`dyn Trait`) |
| `Rc<T>` | Yes | Multiple (Non-atomic) | Immutable | **No** (Single-thread only) | Shared ownership in single-threaded graph/tree structures |
| `Arc<T>` | Yes | Multiple (Atomic) | Immutable | **Yes** (`Send` + `Sync`) | Shared ownership across multiple async tasks / threads |
| `RefCell<T>` | No | 1 (Borrow Checked at Runtime) | Interior Mutability | **No** | Mutating data behind immutable references in single thread |
| `Mutex<T>` | No | 1 (Thread Locking) | Interior Mutability | **Yes** | Mutual exclusion access to shared state across threads |
| `RwLock<T>` | No | 1 (Multiple Readers / 1 Writer) | Interior Mutability | **Yes** | High-read, low-write concurrent state |

### Practical Patterns

#### 1. Recursive Data Structure with `Box<T>` (Linked List)
```rust
#[derive(Debug)]
pub enum List<T> {
    Cons(T, Box<List<T>>),
    Nil,
}

let list = List::Cons(1, Box::new(List::Cons(2, Box::new(List::Nil))));
```

#### 2. Shared Thread-Safe Mutable State (`Arc<Mutex<T>>`)
```rust
use std::sync::{Arc, Mutex};
use std::thread;

let counter = Arc::new(Mutex::new(0));
let mut handles = vec![];

for _ in 0..5 {
    let counter_clone = Arc::clone(&counter);
    let handle = thread::spawn(move || {
        let mut num = counter_clone.lock().unwrap();
        *num += 1;
    });
    handles.push(handle);
}

for h in handles {
    h.join().unwrap();
}

println!("Result: {}", *counter.lock().unwrap()); // 5
```

---

## 13 Modules, Packages, and Modern Imports

### Modern Rust Module Hierarchy (Rust 2018 / 2021 Edition)
Avoid old `mod.rs` patterns; use direct file/folder naming.

```
src/
├── main.rs          # Binary root (or lib.rs for library)
├── configs.rs       # Module definition
├── configs/
│   └── config.rs    # Submodule: configs::config
├── db.rs
├── db/
│   └── connection.rs
├── handlers/
│   ├── auth.rs
│   └── user.rs
└── handlers.rs      # Declares: pub mod auth; pub mod user;
```

### Module Declarations and Visibility
```rust
// src/handlers.rs
pub mod auth;
pub mod user;

// src/handlers/auth.rs
pub async fn login() {
    println!("Auth login");
}

// src/main.rs
mod configs;
mod db;
mod handlers;

use handlers::auth::login; // Bringing into scope
```

### Paths & Re-exports
- `crate::`: Root of current crate
- `super::`: Parent module
- `self::`: Current module
- `pub use`: Re-exporting items for a clean public API surface

```rust
// In src/db.rs
pub mod connection;
pub use connection::create_pool; // Callers use db::create_pool directly
```

---

## 14 Attributes and Serialization (Serde)

### Common Built-in Attributes
```rust
#[derive(Debug, Clone, PartialEq, Eq, Hash)] // Automatic trait implementations
#[inline]                                     // Hint to inline function
#[allow(dead_code)]                           // Silence compiler warnings
#[cfg(target_os = "windows")]                 // Conditional compilation
```

### Serde JSON, YAML & TOML
```rust
use serde::{Deserialize, Serialize};

#[derive(Serialize, Deserialize, Debug)]
#[serde(rename_all = "camelCase")] // Converts field names to camelCase for API JSON
pub struct UserDto {
    pub id: u64,
    #[serde(rename = "user_name")]
    pub username: String,
    #[serde(default)]              // Fallback to Default if omitted in payload
    pub is_admin: bool,
    #[serde(skip_serializing_if = "Option::is_none")]
    pub bio: Option<String>,
}

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let user = UserDto {
        id: 1,
        username: "ndk".to_string(),
        is_admin: false,
        bio: None,
    };

    // Serialize to JSON String
    let json_str = serde_json::to_string_pretty(&user)?;
    println!("{json_str}");

    // Deserialize back
    let parsed: UserDto = serde_json::from_str(&json_str)?;
    println!("{:?}", parsed);

    Ok(())
}
```

---

## 15 Async Rust and Tokio Runtime

### Async Fundamentals
- Rust futures are **pull-based (lazy)**: they do no work until polled via `.await`.
- Async functions return a type implementing `std::future::Future`.

```rust
#[tokio::main]
async fn main() {
    println!("Starting task");
    let result = fetch_data().await;
    println!("Received: {result}");
}

async fn fetch_data() -> String {
    tokio::time::sleep(tokio::time::Duration::from_millis(500)).await;
    "Data payload".to_string()
}
```

### Tokio Tasks & Spawning
```rust
let handle = tokio::spawn(async {
    // Runs on Tokio thread pool worker
    42
});

let result = handle.await.unwrap();
```

### Tokio MPSC Channels (Message Passing)
```rust
use tokio::sync::mpsc;

#[tokio::main]
async fn main() {
    // Multi-producer, single-consumer channel
    let (tx, mut rx) = mpsc::channel::<String>(32);

    for i in 1..=3 {
        let tx_clone = tx.clone();
        tokio::spawn(async move {
            tx_clone.send(format!("Message from task {i}")).await.unwrap();
        });
    }
    drop(tx); // Drop original tx so rx knows when all senders are done

    while let Some(msg) = rx.recv().await {
        println!("Got: {msg}");
    }
}
```

---

## 16 Backend Web Development with Axum

### Axum Production Layered Architecture
Axum is an ergonomic, fast web framework built on top of `tokio`, `tower`, and `hyper`.

```
src/
├── main.rs            # Server init & route binding
├── state.rs           # Shared AppState (Arc<AppState>)
├── routes/            # Route groupings (auth, users, items)
├── handlers/          # HTTP request handlers & extractors
├── services/          # Business logic layer
├── repositories/      # Database queries
├── models/            # Domain entities
├── dto/               # Request/Response schemas (Serde)
└── middlewares/       # Auth guards, logging, CORS
```

### Complete Axum API Example
```rust
use axum::{
    extract::{Path, State},
    http::StatusCode,
    response::IntoResponse,
    routing::{get, post},
    Json, Router,
};
use serde::{Deserialize, Serialize};
use std::sync::Arc;
use tokio::net::TcpListener;

#[derive(Clone)]
pub struct AppState {
    pub db_pool: sqlx::SqlitePool,
}

#[derive(Serialize, Deserialize)]
pub struct CreateUserPayload {
    pub username: String,
    pub email: String,
}

#[derive(Serialize)]
pub struct ApiResponse<T: Serialize> {
    pub success: bool,
    pub data: T,
}

// Handler with State and JSON Extractor
async fn create_user(
    State(state): State<Arc<AppState>>,
    Json(payload): Json<CreateUserPayload>,
) -> impl IntoResponse {
    // Save to database via state.db_pool ...
    (
        StatusCode::CREATED,
        Json(ApiResponse {
            success: true,
            data: format!("Created user {}", payload.username),
        }),
    )
}

// Handler with Path Param Extractor
async fn get_user(
    Path(user_id): Path<u64>,
    State(_state): State<Arc<AppState>>,
) -> impl IntoResponse {
    (
        StatusCode::OK,
        Json(ApiResponse {
            success: true,
            data: format!("Details for user {user_id}"),
        }),
    )
}

#[tokio::main]
async fn main() {
    let pool = sqlx::sqlite::SqlitePoolOptions::new()
        .connect("sqlite::memory:")
        .await
        .unwrap();

    let shared_state = Arc::new(AppState { db_pool: pool });

    let app = Router::new()
        .route("/users", post(create_user))
        .route("/users/:id", get(get_user))
        .with_state(shared_state);

    let listener = TcpListener::bind("127.0.0.1:3000").await.unwrap();
    println!("Server running on http://127.0.0.1:3000");
    axum::serve(listener, app).await.unwrap();
}
```

---

## 17 Database Access with SQLx

### Connection Pool Setup (SQLite / Postgres)
SQLx provides async, pure-Rust, compile-time verified SQL queries.

```rust
use sqlx::sqlite::SqlitePoolOptions;
use sqlx::SqlitePool;

pub async fn init_db(database_url: &str) -> Result<SqlitePool, sqlx::Error> {
    let pool = SqlitePoolOptions::new()
        .max_connections(10)
        .connect(database_url)
        .await?;

    // Run pending migrations
    sqlx::migrate!("./migrations").run(&pool).await?;

    Ok(pool)
}
```

### Async CRUD Operations
```rust
use sqlx::FromRow;

#[derive(Debug, FromRow, serde::Serialize)]
pub struct Todo {
    pub id: i64,
    pub title: String,
    pub completed: bool,
}

// 1. CREATE
pub async fn insert_todo(pool: &SqlitePool, title: &str) -> Result<i64, sqlx::Error> {
    let id = sqlx::query!(
        "INSERT INTO todos (title, completed) VALUES (?, false)",
        title
    )
    .execute(pool)
    .await?
    .last_insert_rowid();

    Ok(id)
}

// 2. READ ALL (Query As)
pub async fn get_todos(pool: &SqlitePool) -> Result<Vec<Todo>, sqlx::Error> {
    let todos = sqlx::query_as!(
        Todo,
        "SELECT id, title, completed FROM todos ORDER BY id DESC"
    )
    .fetch_all(pool)
    .await?;

    Ok(todos)
}

// 3. UPDATE
pub async fn mark_completed(pool: &SqlitePool, id: i64) -> Result<bool, sqlx::Error> {
    let rows_affected = sqlx::query!(
        "UPDATE todos SET completed = true WHERE id = ?",
        id
    )
    .execute(pool)
    .await?
    .rows_affected();

    Ok(rows_affected > 0)
}

// 4. TRANSACTION
pub async fn transfer_todos(pool: &SqlitePool) -> Result<(), sqlx::Error> {
    let mut tx = pool.begin().await?;

    sqlx::query("UPDATE todos SET completed = true WHERE id = 1")
        .execute(&mut *tx)
        .await?;

    sqlx::query("UPDATE todos SET completed = true WHERE id = 2")
        .execute(&mut *tx)
        .await?;

    tx.commit().await?; // Commit or rolls back automatically on drop
    Ok(())
}
```

---

## 18 CLI Applications and File I/O

### Reading Arguments and Standard Input
```rust
use std::env;
use std::io::{self, Write};

fn main() {
    // CLI Arguments
    let args: Vec<String> = env::args().collect();
    if args.len() > 1 {
        println!("Received argument: {}", args[1]);
    }

    // Standard Input
    print!("Enter your name: ");
    io::stdout().flush().unwrap();

    let mut input = String::new();
    io::stdin().read_line(&mut input).expect("Failed to read");
    println!("Hello, {}", input.trim());
}
```

### Buffered File Read & Write
```rust
use std::fs::{File, OpenOptions};
use std::io::{BufRead, BufReader, BufWriter, Write};

fn save_todos(path: &str, items: &[String]) -> std::io::Result<()> {
    let file = OpenOptions::new()
        .create(true)
        .write(true)
        .truncate(true)
        .open(path)?;

    let mut writer = BufWriter::new(file);
    for item in items {
        writeln!(writer, "{}", item)?;
    }
    writer.flush()?;
    Ok(())
}

fn load_todos(path: &str) -> std::io::Result<Vec<String>> {
    let file = File::open(path)?;
    let reader = BufReader::new(file);
    let mut lines = Vec::new();

    for line in reader.lines() {
        lines.push(line?);
    }
    Ok(lines)
}
```

---

## 19 Multithreading and Concurrency Primitives

### Standard Threads (`std::thread`)
```rust
use std::thread;
use std::time::Duration;

let handle = thread::spawn(|| {
    for i in 1..=5 {
        println!("Spawned thread: {i}");
        thread::sleep(Duration::from_millis(50));
    }
});

handle.join().unwrap(); // Wait for thread to finish
```

### Atomic Primitives (`std::sync::atomic`)
Lock-free shared state for primitives.

```rust
use std::sync::atomic::{AtomicUsize, Ordering};
use std::sync::Arc;

let counter = Arc::new(AtomicUsize::new(0));
let c = Arc::clone(&counter);

thread::spawn(move || {
    c.fetch_add(1, Ordering::SeqCst);
}).join().unwrap();

println!("Atomic Counter: {}", counter.load(Ordering::SeqCst));
```

---

## 20 Testing, Benchmarking, and Tooling

### Unit and Integration Tests
```rust
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_add() {
        assert_eq!(add(2, 2), 4);
        assert_ne!(add(2, 2), 5);
    }

    #[test]
    #[should_panic(expected = "divide by zero")]
    fn test_panic() {
        panic!("divide by zero");
    }
}
```

---

## 21 Unsafe Rust and FFI

### When is `unsafe` used?
Unsafe grants 5 superpowers:
1. Dereferencing raw pointers (`*const T`, `*mut T`).
2. Calling unsafe functions or foreign function interfaces (FFI).
3. Implementing unsafe traits (`Send`, `Sync`).
4. Mutating mutable static variables.
5. Accessing fields of `union`s.

```rust
let mut num = 42;
let p1 = &num as *const i32;
let p2 = &mut num as *mut i32;

unsafe {
    *p2 = 100;
    println!("p1 points to: {}", *p1);
}
```

---

## 22 High-Yield Interview Questions and Reality Check

### 1. What makes Rust memory-safe without a Garbage Collector?
> **Answer**: Rust enforces memory safety at **compile time** using its ownership model, affine type system (values move by default and cannot be used after move), and the borrow checker. The borrow checker guarantees at compile time that you never have data races, use-after-free, double-free, or dangling pointers.

### 2. Explain the difference between `&str` and `String`.
> **Answer**: `String` is an owned, growable, heap-allocated buffer consisting of a pointer, length, and capacity on the stack. `&str` is a string slice: an immutable fat pointer `(ptr, len)` borrowing a sequence of UTF-8 characters located on the heap, stack, or binary rodata.

### 3. What is the difference between `Rc<T>` and `Arc<T>`? Why not use `Arc<T>` everywhere?
> **Answer**: `Rc<T>` uses non-atomic reference counts and is strictly single-threaded (does not implement `Send` or `Sync`). `Arc<T>` uses atomic operations (`fetch_add`, `fetch_sub`) to safely synchronize reference counts across threads. `Arc<T>` incurs atomic instruction overhead and memory fences, so `Rc<T>` is preferred in single-threaded workloads for maximum speed.

### 4. What is the difference between `RefCell<T>` and `Mutex<T>`?
> **Answer**: Both provide **interior mutability** (modifying data through an immutable reference). However, `RefCell<T>` enforces the borrow rules at runtime in a single thread (panicking on invalid borrows), whereas `Mutex<T>` blocks/locks threads across concurrent execution.

### 5. What are Lifetime Elision Rules and why do they exist?
> **Answer**: Rust's lifetime elision rules are deterministic heuristics programmed into the compiler that automatically infer lifetimes for common function signatures, eliminating the need to annotate explicit `'a` on every reference.

### 6. What is Monomorphization in Rust Generics?
> **Answer**: When generic code (`fn process<T>(item: T)`) is compiled, the Rust compiler generates a distinct, duplicated copy of the machine code specialized for each concrete type used (e.g., `process_i32`, `process_string`). This enables full compiler optimizations and inlining with **zero runtime dispatch penalty**.

---

### Reality Check & Best Practices
- Prefer borrowing `&str` and `&[T]` in function arguments instead of `&String` or `&Vec<T>` for maximum flexibility.
- Use `clippy` (`cargo clippy -- -D warnings`) on every commit.
- In async code, avoid holding `std::sync::MutexGuard` across `.await` points (use `tokio::sync::Mutex` or restructure to release the lock before awaiting).
- Use `#[derive(Debug, Clone)]` liberally on domain structs and DTOs.
- Isolate IO and network errors with `thiserror` in business logic and bubble them to endpoints with `anyhow` or custom `IntoResponse` handlers.
