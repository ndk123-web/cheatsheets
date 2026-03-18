# C++ Cheatsheet (Detailed A to Z)

This file is a deep quick-reference for C++ with practical explanation blocks:
- What
- Why
- Logic/Concept
- Example
- Behavior of Example

---

## Table of Contents
- [1. Introduction to Language](#1-introduction-to-language)
- [2. Setting up your Environment](#2-setting-up-your-environment)
- [3. Basic Operations](#3-basic-operations)
- [4. Control Flow and Statements](#4-control-flow-and-statements)
- [5. Functions](#5-functions)
- [6. Data Types](#6-data-types)
- [7. Pointers and References](#7-pointers-and-references)
- [8. Structuring Codebase](#8-structuring-codebase)
- [9. Structures and Classes](#9-structures-and-classes)
- [10. Object Oriented Programming](#10-object-oriented-programming)
- [11. Exception Handling](#11-exception-handling)
- [12. Language Concepts](#12-language-concepts)
- [13. Standard Library and STL](#13-standard-library-and-stl)
- [14. Templates](#14-templates)
- [15. Idioms](#15-idioms)
- [16. Standards](#16-standards)
- [17. Debuggers and Language Tools](#17-debuggers-and-language-tools)
- [18. Compilers](#18-compilers)
- [19. Build Systems](#19-build-systems)
- [20. Package Managers](#20-package-managers)
- [21. Working with Libraries](#21-working-with-libraries)
- [22. Libraries](#22-libraries)
- [23. Frameworks and Testing](#23-frameworks-and-testing)

---

## 1. Introduction to Language

### What is C++
**What**
- C++ is a compiled, statically typed, multi-paradigm language.
- It supports procedural, object-oriented, and generic programming.

**Why**
- You get high performance and fine control over memory/resources.

**Logic/Concept**
- C++ compiles source code to native machine code for your platform.
- Strong typing catches many mistakes at compile time.

**Example**
```cpp
#include <iostream>

int main() {
    std::cout << "Hello from C++\n";
    return 0;
}
```

**Behavior of Example**
- Program prints `Hello from C++` and exits with success code `0`.

### Why use C++
**What**
- C++ is commonly used in systems, games, HFT, graphics, browsers, and embedded software.

**Why**
- Performance-sensitive projects need low overhead and predictable control.

**Logic/Concept**
- Zero-cost abstractions: high-level code without hidden runtime penalties (when used properly).

**Example**
```cpp
#include <vector>
#include <numeric>
#include <iostream>

int main() {
    std::vector<int> v(1'000'000, 1);
    std::cout << std::accumulate(v.begin(), v.end(), 0LL) << "\n";
}
```

**Behavior of Example**
- Computes a large sum efficiently with native performance.

### C vs C++
**What**
- C is procedural.
- C++ extends C with classes, templates, RAII, STL.

**Why**
- C++ gives safer and reusable abstractions for large codebases.

**Logic/Concept**
- C++ can write low-level code like C, but also supports higher-level patterns.

**Example**
```cpp
// C style resource handling: manual cleanup
// C++ style prefers RAII wrappers that auto-clean in destructors.
```

**Behavior of Example**
- With RAII, resources are released automatically on scope exit.

---

## 2. Setting up your Environment

### Installing C++
**What**
- Install compiler toolchain (MSVC, GCC, Clang).

**Why**
- Required to compile and run C++ source code.

**Logic/Concept**
- Compiler front-end parses C++.
- Back-end emits machine code.

**Example**
```bash
# Windows (MSVC via Developer Command Prompt)
cl

# Linux
g++ --version

# macOS
clang++ --version
```

**Behavior of Example**
- Shows compiler availability and version.

### Code Editors / IDEs
**What**
- Tools like VS Code, Visual Studio, CLion provide editing, build, debug support.

**Why**
- Faster development workflow with intellisense and debugging.

**Logic/Concept**
- Editor + language server + build system + debugger = productive setup.

**Example**
```txt
Recommended stack:
- Editor: VS Code
- Build: CMake + Ninja
- Compiler: Clang/GCC/MSVC
- Debugger: GDB/LLDB/Visual Studio debugger
```

**Behavior of Example**
- Gives a practical, maintainable default stack.

### Running your First Program
**What**
- Compile source and execute binary.

**Why**
- Verifies environment is set up correctly.

**Logic/Concept**
- Build command transforms .cpp into executable.

**Example**
```bash
g++ main.cpp -std=c++20 -Wall -Wextra -pedantic -o app
./app
```

**Behavior of Example**
- Produces executable `app` and runs it.

---

## 3. Basic Operations

### Arithmetic Operators
**What**
- Numeric operations: `+ - * / % ++ --`.

**Why**
- Core math logic in all programs.

**Logic/Concept**
- Integer division truncates.
- `%` works with integers.

**Example**
```cpp
int a = 7, b = 3;
int q = a / b; // 2
int r = a % b; // 1
```

**Behavior of Example**
- `q` becomes `2`, `r` becomes `1`.

### Logical Operators
**What**
- Boolean operators: `&&`, `||`, `!`.

**Why**
- Needed for decision making and conditions.

**Logic/Concept**
- Short-circuit rules:
- In `x && y`, if `x` is false, `y` is not evaluated.
- In `x || y`, if `x` is true, `y` is not evaluated.

**Example**
```cpp
bool ok = (5 > 1) && (2 < 1); // false
```

**Behavior of Example**
- `ok` is `false` because second expression is false.

### Bitwise Operators
**What**
- Bit-level operations: `& | ^ ~ << >>`.

**Why**
- Useful in flags, low-level programming, performance-sensitive paths.

**Logic/Concept**
- Operates on binary representation of integers.

**Example**
```cpp
unsigned int x = 0b1010;
unsigned int y = 0b1100;
auto z = x & y; // 0b1000
```

**Behavior of Example**
- `z` equals `8`.

---

## 4. Control Flow and Statements

### for / while / do while loops
**What**
- Repetition structures for iterative execution.

**Why**
- Avoid duplicated code and process sequences.

**Logic/Concept**
- `for`: best for counted loops.
- `while`: best when condition-driven.
- `do while`: runs body at least once.

**Example**
```cpp
for (int i = 0; i < 3; ++i) {}

int n = 0;
while (n < 3) ++n;

do {
    --n;
} while (n > 0);
```

**Behavior of Example**
- `for` iterates 3 times.
- `while` increases `n` until 3.
- `do while` executes at least once.

### if else / switch / goto
**What**
- Branching constructs.

**Why**
- Direct execution flow based on conditions/values.

**Logic/Concept**
- `switch` is good for fixed-value dispatch.
- `goto` exists but is discouraged due to maintainability.

**Example**
```cpp
int code = 2;
if (code == 1) {
    // ...
} else if (code == 2) {
    // ...
}

switch (code) {
    case 1: break;
    case 2: break;
    default: break;
}
```

**Behavior of Example**
- Branch for `code == 2` executes.

---

## 5. Functions

### Static Polymorphism
**What**
- Compile-time polymorphism via templates.

**Why**
- Generic code without virtual call overhead.

**Logic/Concept**
- Compiler generates type-specific versions.

**Example**
```cpp
template <typename T>
T add(T a, T b) { return a + b; }
```

**Behavior of Example**
- `add<int>` and `add<double>` are instantiated as needed.

### Function Overloading
**What**
- Multiple functions with same name, different parameter lists.

**Why**
- Cleaner APIs for related operations.

**Logic/Concept**
- Overload chosen at compile time by signature matching.

**Example**
```cpp
int area(int s) { return s * s; }
int area(int w, int h) { return w * h; }
```

**Behavior of Example**
- Compiler calls correct `area` based on arguments.

### Operator Overloading
**What**
- Custom meaning for operators on user-defined types.

**Why**
- Makes custom types feel natural to use.

**Logic/Concept**
- Keep semantics intuitive and non-surprising.

**Example**
```cpp
struct Point {
    int x{}, y{};
    Point operator+(const Point& o) const { return {x + o.x, y + o.y}; }
};
```

**Behavior of Example**
- `p1 + p2` returns new summed point.

### Lambdas
**What**
- Inline anonymous function objects.

**Why**
- Great for local callback logic.

**Logic/Concept**
- Capture list controls variable access from outer scope.

**Example**
```cpp
int factor = 3;
auto mul = [factor](int x) { return x * factor; };
```

**Behavior of Example**
- `mul(4)` returns `12` using captured `factor`.

---

## 6. Data Types

### Static Typing
**What**
- Variable type fixed at compile time.

**Why**
- Strong compile-time checking and optimized codegen.

**Logic/Concept**
- Type mismatch is compile error, not runtime surprise.

**Example**
```cpp
int n = 10;
// n = "abc"; // compile error
```

**Behavior of Example**
- Invalid assignment fails compilation.

### Dynamic Typing (Clarification)
**What**
- C++ is not dynamically typed.
- But you can do runtime polymorphism and type erasure.

**Why**
- Helps model heterogeneous behavior safely.

**Logic/Concept**
- Dynamic dispatch via virtual functions.
- Runtime checked casts via RTTI.

**Example**
```cpp
#include <memory>
#include <vector>

struct Shape { virtual ~Shape() = default; };
struct Circle : Shape {};

std::vector<std::unique_ptr<Shape>> shapes;
shapes.push_back(std::make_unique<Circle>());
```

**Behavior of Example**
- Container stores polymorphic objects via base pointer.

### RTTI
**What**
- Runtime Type Information (`typeid`, `dynamic_cast`).

**Why**
- Identify actual derived type safely at runtime.

**Logic/Concept**
- Works with polymorphic base classes (with virtual member).

**Example**
```cpp
#include <iostream>
#include <typeinfo>

struct Base { virtual ~Base() = default; };
struct Dog : Base {};

int main() {
    Base* b = new Dog();
    if (auto d = dynamic_cast<Dog*>(b)) {
        std::cout << "Dog\n";
    }
    delete b;
}
```

**Behavior of Example**
- Safe cast succeeds and prints `Dog`.

---

## 7. Pointers and References

### References
**What**
- Alias to an existing object.

**Why**
- Cleaner syntax for pass-by-reference APIs.

**Logic/Concept**
- Must be initialized and cannot be reseated.

**Example**
```cpp
int x = 10;
int& ref = x;
ref = 99;
```

**Behavior of Example**
- `x` becomes `99`.

### Memory Model
**What**
- Defines storage duration and object lifetime behavior.

**Why**
- Prevents dangling references and UB.

**Logic/Concept**
- Storage durations: automatic, static, thread_local, dynamic.

**Example**
```cpp
int global_var = 1;          // static storage duration

void f() {
    int local = 2;           // automatic
    static int once = 3;     // static
}
```

**Behavior of Example**
- `once` retains value across calls.

### Lifetime of Objects
**What**
- Period where object is valid for use.

**Why**
- Access after lifetime ends is UB.

**Logic/Concept**
- Constructor starts lifetime, destructor ends it.

**Example**
```cpp
int* bad_ptr() {
    int x = 5;
    return &x; // dangling pointer
}
```

**Behavior of Example**
- Returned pointer is invalid after function returns.

### Smart Pointers

#### unique_ptr
**What**
- Exclusive ownership pointer.

**Why**
- Prevent leaks and double deletes.

**Logic/Concept**
- Non-copyable, movable.

**Example**
```cpp
#include <memory>
auto p = std::make_unique<int>(42);
```

**Behavior of Example**
- `p` owns integer and deletes automatically.

#### shared_ptr
**What**
- Shared ownership pointer.

**Why**
- Multiple objects can co-own same resource.

**Logic/Concept**
- Reference count controls deletion timing.

**Example**
```cpp
#include <memory>
auto a = std::make_shared<int>(7);
auto b = a;
```

**Behavior of Example**
- Resource is deleted only when last owner goes away.

#### weak_ptr
**What**
- Non-owning observer to `shared_ptr` resource.

**Why**
- Break reference cycles.

**Logic/Concept**
- Use `lock()` to get temporary `shared_ptr`.

**Example**
```cpp
#include <memory>
std::shared_ptr<int> sp = std::make_shared<int>(5);
std::weak_ptr<int> wp = sp;
if (auto locked = wp.lock()) {
    // use *locked
}
```

**Behavior of Example**
- Access succeeds while `sp` is alive.

### Raw Pointers
**What**
- Plain memory address value.

**Why**
- Useful for non-owning views or low-level APIs.

**Logic/Concept**
- Raw pointer does not express ownership.

**Example**
```cpp
int x = 10;
int* p = &x;
```

**Behavior of Example**
- `p` points to `x`, no allocation involved.

### New/Delete Operators
**What**
- Manual dynamic allocation/deallocation.

**Why**
- Needed in rare low-level scenarios.

**Logic/Concept**
- Every `new` should have corresponding `delete` (or use smart pointer).

**Example**
```cpp
int* p = new int(3);
delete p;
p = nullptr;
```

**Behavior of Example**
- Memory released and pointer reset.

### Memory Leakage
**What**
- Allocated memory lost without deallocation.

**Why**
- Leads to growing memory usage and instability.

**Logic/Concept**
- Leak happens when no valid pointer remains to free allocation.

**Example**
```cpp
void leak() {
    int* p = new int(10);
    (void)p;
} // leak: no delete
```

**Behavior of Example**
- Each call leaks memory.

---

## 8. Structuring Codebase

### Scope
**What**
- Visibility region of names.

**Why**
- Controls access and avoids naming collisions.

**Logic/Concept**
- Inner scope can shadow outer names.

**Example**
```cpp
int x = 1;
{
    int x = 2;
}
```

**Behavior of Example**
- Inner `x` hides outer `x` inside block.

### Namespaces
**What**
- Logical grouping of symbols.

**Why**
- Prevent global name conflicts.

**Logic/Concept**
- Qualify names with `ns::name`.

**Example**
```cpp
namespace math {
    int add(int a, int b) { return a + b; }
}

int n = math::add(2, 3);
```

**Behavior of Example**
- Calls function from `math` namespace.

### Headers / CPP Files
**What**
- Header contains declarations, source contains definitions.

**Why**
- Improves organization and compile boundaries.

**Logic/Concept**
- Include guards or `#pragma once` avoid multiple inclusion.

**Example**
```cpp
// user.hpp
#pragma once
struct User { int id; };

// user.cpp
#include "user.hpp"
```

**Behavior of Example**
- Compiler sees declaration through header wherever included.

### Forward Declaration
**What**
- Declare type/function before full definition.

**Why**
- Reduce include coupling and compile time.

**Logic/Concept**
- Useful when only pointers/references are needed.

**Example**
```cpp
class Engine; // forward declaration

class Car {
    Engine* e{};
};
```

**Behavior of Example**
- `Car` compiles without including full `Engine` definition.

---

## 9. Structures and Classes

### Multiple Inheritance
**What**
- Derive from more than one base class.

**Why**
- Combine orthogonal behaviors.

**Logic/Concept**
- Can increase complexity; use carefully.

**Example**
```cpp
struct Flyer { void fly() {} };
struct Swimmer { void swim() {} };
struct Duck : Flyer, Swimmer {};
```

**Behavior of Example**
- `Duck` has both `fly` and `swim` capabilities.

### Diamond Inheritance
**What**
- Two base classes share a common base.

**Why**
- Without virtual inheritance, derived object can contain duplicate base subobjects.

**Logic/Concept**
- Use `virtual` inheritance to share one base instance.

**Example**
```cpp
struct Base { int x{}; };
struct L : virtual Base {};
struct R : virtual Base {};
struct D : L, R {};
```

**Behavior of Example**
- `D` has only one `Base` subobject.

### Rule of Zero, Five, Three
**What**
- Resource-management design rules for special member functions.

**Why**
- Prevent bugs with copy/move/destruction.

**Logic/Concept**
- Prefer Rule of Zero by using RAII members (string/vector/unique_ptr).

**Example**
```cpp
struct FileHandle {
    FileHandle() = default;
    ~FileHandle() = default;
    FileHandle(const FileHandle&) = delete;
    FileHandle& operator=(const FileHandle&) = delete;
    FileHandle(FileHandle&&) = default;
    FileHandle& operator=(FileHandle&&) = default;
};
```

**Behavior of Example**
- Copy is blocked, move is allowed.

---

## 10. Object Oriented Programming

### Virtual Methods
**What**
- Methods that can be overridden for runtime dispatch.

**Why**
- Enable polymorphic behavior via base pointers/references.

**Logic/Concept**
- Declare destructor virtual in polymorphic base classes.

**Example**
```cpp
#include <iostream>

struct Animal {
    virtual ~Animal() = default;
    virtual void speak() const { std::cout << "sound\n"; }
};

struct Dog : Animal {
    void speak() const override { std::cout << "bark\n"; }
};
```

**Behavior of Example**
- Calling `speak()` through `Animal*` to `Dog` prints `bark`.

### Virtual Tables
**What**
- Compiler-generated tables used to resolve virtual calls.

**Why**
- Implements dynamic dispatch.

**Logic/Concept**
- Each polymorphic type usually has a vtable pointer in object layout.

**Example**
```cpp
// Conceptual: not directly visible in source code.
```

**Behavior of Example**
- Virtual call resolves to most-derived override at runtime.

### Dynamic Polymorphism
**What**
- Runtime method selection based on actual object type.

**Why**
- Allows extensible design via interfaces/base classes.

**Logic/Concept**
- Requires virtual functions + base pointer/reference.

**Example**
```cpp
Animal* a = new Dog();
a->speak();
delete a;
```

**Behavior of Example**
- Prints `bark` due to dynamic dispatch.

---

## 11. Exception Handling

### Exit Codes
**What**
- Program return status to OS.

**Why**
- Signal success/failure in scripts and CI.

**Logic/Concept**
- `0` typically success, non-zero means error.

**Example**
```cpp
int main() {
    return 1;
}
```

**Behavior of Example**
- Process exits with failure status.

### Exceptions
**What**
- Structured error propagation with `throw`/`catch`.

**Why**
- Separate error paths from normal logic.

**Logic/Concept**
- Stack unwinding destroys local objects automatically.

**Example**
```cpp
#include <stdexcept>
#include <iostream>

int main() {
    try {
        throw std::runtime_error("bad input");
    } catch (const std::exception& e) {
        std::cout << e.what() << "\n";
    }
}
```

**Behavior of Example**
- Prints `bad input` and continues cleanly.

### Access Violations
**What**
- Illegal memory access (segfault/access violation).

**Why**
- Common in raw pointer misuse.

**Logic/Concept**
- Reading/writing invalid address is UB and often crashes.

**Example**
```cpp
int* p = nullptr;
// *p = 1; // crash/UB
```

**Behavior of Example**
- Dereferencing null pointer can crash program.

---

## 12. Language Concepts

### auto (Automatic Type Deduction)
**What**
- Compiler deduces variable type from initializer.

**Why**
- Reduces verbosity and prevents type mismatch in long type names.

**Logic/Concept**
- Deduced type can differ with references/const qualifiers.

**Example**
```cpp
auto n = 10;         // int
const int x = 5;
auto y = x;          // int (const removed for copy)
```

**Behavior of Example**
- `y` is modifiable int, not const int.

### Type Casting Overview
**What**
- Explicit type conversion.

**Why**
- Needed at API boundaries and polymorphic conversions.

**Logic/Concept**
- Prefer safest cast that matches intent.

**Example**
```cpp
double d = 4.9;
int i = static_cast<int>(d);
```

**Behavior of Example**
- `i` becomes `4`.

### static_cast
**What**
- Compile-time checked conversion for related types.

**Why**
- Safer than C-style casts.

**Logic/Concept**
- No runtime polymorphic safety checks.

**Example**
```cpp
float f = 3.14f;
int n = static_cast<int>(f);
```

**Behavior of Example**
- Truncates fractional part.

### const_cast
**What**
- Adds/removes const/volatile qualifiers.

**Why**
- Rare interoperability with legacy APIs.

**Logic/Concept**
- Modifying truly const object via cast is UB.

**Example**
```cpp
const int a = 10;
const int* p = &a;
int* q = const_cast<int*>(p);
// *q = 20; // UB if a is truly const
```

**Behavior of Example**
- Compiles, but write attempt may be UB.

### dynamic_cast
**What**
- Runtime-checked cast in inheritance hierarchies.

**Why**
- Safe downcasting from base to derived.

**Logic/Concept**
- Returns `nullptr` for failed pointer cast.

**Example**
```cpp
Base* b = new Base();
Dog* d = dynamic_cast<Dog*>(b);
```

**Behavior of Example**
- `d` becomes `nullptr` since object is not `Dog`.

### reinterpret_cast
**What**
- Low-level bit reinterpretation cast.

**Why**
- Very niche systems-level use cases.

**Logic/Concept**
- Dangerous and non-portable if misused.

**Example**
```cpp
std::uintptr_t bits = reinterpret_cast<std::uintptr_t>(b);
```

**Behavior of Example**
- Converts pointer to integer representation.

### Undefined Behavior (UB)
**What**
- Program behavior not defined by C++ standard.

**Why**
- UB can cause security bugs and unpredictable results.

**Logic/Concept**
- Compiler may optimize under assumption UB never occurs.

**Example**
```cpp
int arr[3] = {1,2,3};
// int x = arr[10]; // UB
```

**Behavior of Example**
- May crash, print garbage, or seem to work unpredictably.

### Argument Dependent Lookup (ADL)
**What**
- Function lookup includes namespaces associated with argument types.

**Why**
- Enables generic code calling unqualified functions (like `swap`).

**Logic/Concept**
- Common in template code with `using std::swap; swap(a,b);`.

**Example**
```cpp
namespace lib {
    struct X {};
    void print(const X&) {}
}

lib::X x;
print(x); // found via ADL
```

**Behavior of Example**
- `lib::print` is selected even without namespace qualifier.

### Name Mangling
**What**
- Compiler encoding of function/type signatures into symbol names.

**Why**
- Supports overloading and linkage.

**Logic/Concept**
- `extern "C"` disables mangling for C ABI compatibility.

**Example**
```cpp
extern "C" void c_api();
```

**Behavior of Example**
- Exported symbol has C-compatible linkage name.

### Macros
**What**
- Preprocessor text substitution.

**Why**
- Conditional compilation and compile-time constants (legacy patterns).

**Logic/Concept**
- No type safety, no scope awareness.

**Example**
```cpp
#define MAX(a,b) ((a) > (b) ? (a) : (b))
```

**Behavior of Example**
- Expanded before compilation; side-effects can be problematic.

---

## 13. Standard Library and STL

### Iterators
**What**
- Objects that navigate containers.

**Why**
- Unified algorithm-container interaction.

**Logic/Concept**
- Different iterator categories enable different operations.

**Example**
```cpp
#include <vector>
std::vector<int> v = {1,2,3};
for (auto it = v.begin(); it != v.end(); ++it) {
    // use *it
}
```

**Behavior of Example**
- Iterates all vector elements.

### iostream
**What**
- Stream-based console/file I/O library.

**Why**
- Type-safe input/output.

**Logic/Concept**
- Uses stream operators `<<` and `>>`.

**Example**
```cpp
#include <iostream>
std::cout << "value=" << 42 << "\n";
```

**Behavior of Example**
- Writes formatted output to stdout.

### Algorithms
**What**
- STL algorithm utilities (`sort`, `find`, `transform`, etc.).

**Why**
- Reusable, optimized generic operations.

**Logic/Concept**
- Works on iterator ranges.

**Example**
```cpp
#include <algorithm>
#include <vector>
std::vector<int> v = {3,1,2};
std::sort(v.begin(), v.end());
```

**Behavior of Example**
- `v` becomes `{1,2,3}`.

### Date / Time
**What**
- `std::chrono` time points, durations, clocks.

**Why**
- Type-safe time handling.

**Logic/Concept**
- Duration types prevent unit confusion.

**Example**
```cpp
#include <chrono>
auto start = std::chrono::steady_clock::now();
```

**Behavior of Example**
- Captures monotonic start time for elapsed measurement.

### Multithreading
**What**
- Native thread support in standard library.

**Why**
- Parallelism and responsive background work.

**Logic/Concept**
- Shared data requires synchronization primitives.

**Example**
```cpp
#include <thread>
#include <iostream>

int main() {
    std::thread t([] { std::cout << "worker\n"; });
    t.join();
}
```

**Behavior of Example**
- Worker thread runs and main waits via `join`.

### Containers
**What**
- Data structure implementations in STL.

**Why**
- Standardized performant structures for most use cases.

**Logic/Concept**
- Pick container based on access/update complexity.

**Example**
```cpp
#include <unordered_map>
std::unordered_map<std::string, int> freq;
freq["cpp"]++;
```

**Behavior of Example**
- Inserts key if missing and increments count.

---

## 14. Templates

### Variadic Templates
**What**
- Templates that accept variable number of type/value arguments.

**Why**
- Flexible generic APIs like logging/forwarding.

**Logic/Concept**
- Parameter packs expanded at compile time.

**Example**
```cpp
#include <iostream>

template <typename... Ts>
void print_all(const Ts&... args) {
    ((std::cout << args << ' '), ...);
}
```

**Behavior of Example**
- Prints all arguments in one call.

### Template Specialization
**What**
- Custom template behavior for specific types.

**Why**
- Optimize or alter semantics for known types.

**Logic/Concept**
- Compiler chooses specialized version when it matches.

**Example**
```cpp
template <typename T>
struct IsPointer { static constexpr bool value = false; };

template <typename T>
struct IsPointer<T*> { static constexpr bool value = true; };
```

**Behavior of Example**
- `IsPointer<int>::value` false, `IsPointer<int*>::value` true.

### Full Template Specialization
**What**
- Specialization for one exact type.

**Why**
- Exact custom logic.

**Logic/Concept**
- Most specific match wins.

**Example**
```cpp
template <typename T>
struct Name { static constexpr const char* value = "generic"; };

template <>
struct Name<int> { static constexpr const char* value = "int"; };
```

**Behavior of Example**
- `Name<int>::value` returns `int`.

### Partial Template Specialization
**What**
- Specialization for a family/pattern of types.

**Why**
- Reuse for grouped type shapes.

**Logic/Concept**
- Example pattern: pointer types, container wrappers.

**Example**
```cpp
template <typename T>
struct Kind { static constexpr const char* v = "value"; };

template <typename T>
struct Kind<T*> { static constexpr const char* v = "pointer"; };
```

**Behavior of Example**
- `Kind<double*>::v` gives `pointer`.

### Type Traits
**What**
- Compile-time type property queries.

**Why**
- Enable conditional generic code.

**Logic/Concept**
- Many traits in `<type_traits>`.

**Example**
```cpp
#include <type_traits>
static_assert(std::is_integral_v<int>);
```

**Behavior of Example**
- Compile succeeds if condition true.

### SFINAE
**What**
- Substitution failure during template matching is not hard error.

**Why**
- Conditionally enable template overloads.

**Logic/Concept**
- Legacy technique often replaced by C++20 concepts.

**Example**
```cpp
#include <type_traits>

template <typename T, typename = std::enable_if_t<std::is_integral_v<T>>>
T twice(T v) { return v + v; }
```

**Behavior of Example**
- `twice(5)` works, non-integral types may fail overload resolution.

---

## 15. Idioms

### Non-Copyable / Non-Moveable
**What**
- Delete copy/move operations intentionally.

**Why**
- Enforce ownership/lifetime semantics.

**Logic/Concept**
- Prevent accidental copies of resource owners.

**Example**
```cpp
struct Handle {
    Handle() = default;
    Handle(const Handle&) = delete;
    Handle& operator=(const Handle&) = delete;
    Handle(Handle&&) = delete;
    Handle& operator=(Handle&&) = delete;
};
```

**Behavior of Example**
- Object cannot be copied or moved.

### Erase-Remove
**What**
- Standard way to remove matching values from sequence containers.

**Why**
- `std::remove` alone only reorders and returns new logical end.

**Logic/Concept**
- Need container `erase` call to actually shrink.

**Example**
```cpp
#include <algorithm>
#include <vector>

std::vector<int> v = {1,0,2,0,3};
v.erase(std::remove(v.begin(), v.end(), 0), v.end());
```

**Behavior of Example**
- Vector becomes `{1,2,3}`.

### Copy and Swap
**What**
- Assignment implemented via copying temp and swapping.

**Why**
- Strong exception safety.

**Logic/Concept**
- If copy fails, original object unchanged.

**Example**
```cpp
// Pattern concept (simplified):
// T& operator=(T other) { swap(*this, other); return *this; }
```

**Behavior of Example**
- Assignment is safe and concise when swap is noexcept.

### Copy on Write
**What**
- Share data until mutation requires copy.

**Why**
- Reduce copies for read-heavy workloads.

**Logic/Concept**
- Less common in modern C++ core types due to threading/complexity.

**Example**
```cpp
// Conceptual idiom; not default behavior of std::string in modern implementations.
```

**Behavior of Example**
- Data copied only when write occurs.

### RAII
**What**
- Resource Acquisition Is Initialization.

**Why**
- Automatic deterministic cleanup, even on exceptions.

**Logic/Concept**
- Constructor acquires, destructor releases.

**Example**
```cpp
#include <mutex>

std::mutex m;
void f() {
    std::lock_guard<std::mutex> lock(m);
    // critical section
}
```

**Behavior of Example**
- Mutex always unlocked when scope ends.

### Pimpl
**What**
- Pointer-to-implementation idiom.

**Why**
- Hides private details and reduces rebuild dependencies.

**Logic/Concept**
- Public header stores opaque pointer; implementation in cpp.

**Example**
```cpp
// Widget.hpp
class Widget {
    struct Impl;
    Impl* p;
};
```

**Behavior of Example**
- Header remains stable while internals can change.

### CRTP
**What**
- Curiously Recurring Template Pattern.

**Why**
- Static polymorphism without virtual overhead.

**Logic/Concept**
- Base template takes derived type as parameter.

**Example**
```cpp
template <typename Derived>
struct Base {
    void call() { static_cast<Derived*>(this)->impl(); }
};

struct Child : Base<Child> {
    void impl() {}
};
```

**Behavior of Example**
- `call()` resolves `impl()` at compile time.

---

## 16. Standards

### C++ 11 / 14
**What**
- Major modernization: move semantics, lambdas, smart pointers, `auto`.

**Why**
- Better safety and expressiveness.

**Logic/Concept**
- C++14 refined C++11 with more constexpr and generic lambdas.

**Example**
```cpp
auto x = 5;
auto f = [](auto v) { return v + v; }; // C++14 generic lambda
```

**Behavior of Example**
- Lambda works with multiple types.

### C++ 17
**What**
- Added `if constexpr`, structured bindings, `optional`, `variant`.

**Why**
- Cleaner generic and value-oriented code.

**Logic/Concept**
- Reduced boilerplate with modern language utilities.

**Example**
```cpp
std::pair<int,int> p{1,2};
auto [a,b] = p;
```

**Behavior of Example**
- `a` and `b` hold decomposed pair values.

### C++ 20
**What**
- Concepts, ranges, coroutines, improved constexpr.

**Why**
- Better constraints and safer template APIs.

**Logic/Concept**
- Concepts improve diagnostics and readability.

**Example**
```cpp
#include <concepts>

template <std::integral T>
T add_one(T x) { return x + 1; }
```

**Behavior of Example**
- Only integral types accepted by compiler.

### Newest
**What**
- C++23 now available on modern toolchains; C++26 in progress.

**Why**
- Continue improving productivity and safety.

**Logic/Concept**
- Toolchain support varies; verify compiler version.

**Example**
```txt
Check support matrix before adopting new features in production.
```

**Behavior of Example**
- Prevents portability surprises.

### C++ 0x
**What**
- Historical pre-release nickname for C++11.

**Why**
- Seen in legacy docs and talks.

**Logic/Concept**
- Refers to the long design phase before final standard year.

**Example**
```txt
"C++0x" in old article usually means early C++11 features.
```

**Behavior of Example**
- Clarifies older terminology.

---

## 17. Debuggers and Language Tools

### Understanding Debugger Messages
**What**
- Reading call stacks, watch variables, and stop reasons.

**Why**
- Faster root-cause analysis.

**Logic/Concept**
- Focus on first failing frame and invalid state transition.

**Example**
```txt
Look at:
1) Exception type
2) Stack trace frame where state became invalid
3) Variable values in that frame
```

**Behavior of Example**
- You identify source of bug, not just crash location.

### Debugging Symbols
**What**
- Mapping from binary machine code to source lines/types.

**Why**
- Needed for meaningful breakpoints and stack traces.

**Logic/Concept**
- Build debug config with symbols (`-g` or `/Zi`).

**Example**
```bash
g++ main.cpp -std=c++20 -g -O0 -o app
```

**Behavior of Example**
- Debugger can show source-level details.

### WinDbg
**What**
- Advanced Windows debugger.

**Why**
- Useful for crash dumps, native internals.

**Logic/Concept**
- Analyze dump files and exception records.

**Example**
```txt
Open .dmp -> !analyze -v -> inspect stack/modules
```

**Behavior of Example**
- Produces crash diagnostics with likely faulting module/stack.

### GDB
**What**
- GNU debugger for Linux and other platforms.

**Why**
- Interactive native debugging with breakpoints.

**Logic/Concept**
- Step through execution and inspect memory/state.

**Example**
```bash
gdb ./app
# (gdb) break main
# (gdb) run
# (gdb) next
# (gdb) bt
```

**Behavior of Example**
- Stops at `main`, steps lines, and prints stack backtrace.

---

## 18. Compilers

### Compiler Stages
**What**
- Preprocess, compile, assemble, link.

**Why**
- Helps diagnose build errors accurately.

**Logic/Concept**
- Include/macro issues are preprocessing.
- Undefined symbols are linking errors.

**Example**
```txt
source.cpp -> preprocessed.cpp -> object.o -> executable
```

**Behavior of Example**
- Clear mental model for where failures happen.

### Compilers and Features
**What**
- Different compilers support different standards/features.

**Why**
- Portability depends on compiler/version and flags.

**Logic/Concept**
- Always set explicit language standard flag.

**Example**
```bash
clang++ main.cpp -std=c++20
```

**Behavior of Example**
- Ensures expected language feature set.

### Clang++ / LLVM
**What**
- Compiler front-end with LLVM backend.

**Why**
- Great diagnostics and tooling integration.

**Logic/Concept**
- Often used with sanitizers and static tools.

**Example**
```bash
clang++ main.cpp -std=c++20 -fsanitize=address,undefined -g
```

**Behavior of Example**
- Runtime checks detect memory/UB issues.

### Intel C++
**What**
- Intel-focused compiler optimizations.

**Why**
- HPC/performance tuning on Intel architectures.

**Logic/Concept**
- Can generate hardware-specific optimized code paths.

**Example**
```txt
Use Intel compiler in performance-critical scientific workloads.
```

**Behavior of Example**
- Potential speed improvements on target hardware.

### MSVC C++
**What**
- Microsoft compiler for Windows.

**Why**
- Best integration with Visual Studio and Windows ecosystem.

**Logic/Concept**
- Uses `/std:c++20`, `/EHsc`, `/W4` flags.

**Example**
```bash
cl /std:c++20 /W4 /EHsc main.cpp
```

**Behavior of Example**
- Builds executable with strict warnings.

### GCC
**What**
- GNU Compiler Collection C++ front-end (`g++`).

**Why**
- Stable and widely used cross-platform.

**Logic/Concept**
- Strong optimization and mature ecosystem.

**Example**
```bash
g++ main.cpp -std=c++20 -O2 -Wall -Wextra
```

**Behavior of Example**
- Optimized production build with common warnings.

### MinGW
**What**
- GCC toolchain for Windows.

**Why**
- Lightweight alternative to MSVC for GCC-like workflow.

**Logic/Concept**
- Can integrate with VS Code tasks and CMake.

**Example**
```bash
g++ main.cpp -std=c++20 -o app.exe
```

**Behavior of Example**
- Produces Windows executable using GCC-style toolchain.

---

## 19. Build Systems

### CMake
**What**
- Cross-platform meta build generator.

**Why**
- Standard way to manage medium/large C++ builds.

**Logic/Concept**
- Generates Ninja/Make/Visual Studio projects from one config.

**Example**
```cmake
cmake_minimum_required(VERSION 3.20)
project(MyApp LANGUAGES CXX)
set(CMAKE_CXX_STANDARD 20)
add_executable(myapp main.cpp)
```

**Behavior of Example**
- Configures a C++20 executable target.

### Makefile
**What**
- Rule-based build scripting.

**Why**
- Good for small or custom workflows.

**Logic/Concept**
- Targets and dependencies control incremental builds.

**Example**
```make
app: main.cpp
	g++ main.cpp -std=c++20 -o app
```

**Behavior of Example**
- Running `make` builds `app` if source changed.

### Ninja
**What**
- Fast low-level build executor.

**Why**
- Very fast incremental rebuilds.

**Logic/Concept**
- Usually generated by CMake (`-G Ninja`).

**Example**
```bash
cmake -S . -B build -G Ninja
cmake --build build
```

**Behavior of Example**
- Generates and builds project quickly.

---

## 20. Package Managers

### vcpkg
**What**
- C/C++ dependency manager by Microsoft.

**Why**
- Easy package integration with CMake toolchain.

**Logic/Concept**
- Installs prebuilt/source ports and exposes CMake configs.

**Example**
```bash
vcpkg install fmt
```

**Behavior of Example**
- `fmt` package becomes available for project linking.

### Conan
**What**
- Cross-platform package manager for C/C++.

**Why**
- Reproducible dependency graphs and profiles.

**Logic/Concept**
- Lockfiles and profiles help deterministic builds.

**Example**
```bash
conan install . --build=missing
```

**Behavior of Example**
- Resolves and downloads/builds missing dependencies.

### NuGet
**What**
- Package manager common in .NET and Visual Studio workflows.

**Why**
- Some C++ projects in MSVC ecosystem use it.

**Logic/Concept**
- Integrates with solution/project files.

**Example**
```txt
Install package via Visual Studio NuGet manager for C++ project.
```

**Behavior of Example**
- Package references added to project metadata.

### Spack
**What**
- Package manager focused on HPC/scientific stacks.

**Why**
- Handles many compiler/MPI/variant combinations.

**Logic/Concept**
- Build matrix flexibility for supercomputing environments.

**Example**
```bash
spack install openmpi
```

**Behavior of Example**
- Installs selected package variant in Spack environment.

---

## 21. Working with Libraries

### Library Inclusion
**What**
- Linking external code: header-only, static, or shared libraries.

**Why**
- Reuse proven components instead of reinventing.

**Logic/Concept**
- Include directories for headers, linker flags for binaries.

**Example**
```bash
g++ main.cpp -I/path/include -L/path/lib -lmylib
```

**Behavior of Example**
- Compiler finds headers and linker resolves library symbols.

### Licensing
**What**
- Legal terms for using/distributing dependencies.

**Why**
- Avoid compliance and legal risks.

**Logic/Concept**
- Verify license compatibility before shipping binaries.

**Example**
```txt
Track each dependency: name, version, license, notice requirements.
```

**Behavior of Example**
- Clear audit trail for release compliance.

---

## 22. Libraries

### Boost
**What**
- Large set of peer-reviewed C++ libraries.

**Why**
- Adds robust utilities beyond STL.

**Logic/Concept**
- Many Boost ideas later became standard C++ features.

**Example**
```cpp
#include <boost/algorithm/string.hpp>
```

**Behavior of Example**
- Access rich string/utility APIs.

### OpenCV
**What**
- Computer vision and image processing library.

**Why**
- Efficient CV pipelines and camera/image workflows.

**Logic/Concept**
- Matrix-first image representation (`cv::Mat`).

**Example**
```cpp
#include <opencv2/opencv.hpp>
```

**Behavior of Example**
- Enables OpenCV APIs for image operations.

### POCO
**What**
- C++ class libraries for networking, IO, data.

**Why**
- Enterprise-style backend utilities.

**Logic/Concept**
- Cross-platform abstractions around system tasks.

**Example**
```cpp
#include <Poco/Net/HTTPClientSession.h>
```

**Behavior of Example**
- Enables HTTP client APIs.

### protobuf
**What**
- Binary serialization format and code generator.

**Why**
- Compact, fast, schema-based message exchange.

**Logic/Concept**
- `.proto` schema generates strongly typed code.

**Example**
```txt
protoc --cpp_out=. message.proto
```

**Behavior of Example**
- Generates C++ classes for serialization/deserialization.

### gRPC
**What**
- RPC framework often used with protobuf.

**Why**
- Typed service contracts and efficient inter-service communication.

**Logic/Concept**
- Client/server stubs generated from proto definitions.

**Example**
```txt
protoc --grpc_out=. --plugin=protoc-gen-grpc=... service.proto
```

**Behavior of Example**
- Generates service interfaces and stubs.

### TensorFlow
**What**
- ML framework with C++ APIs.

**Why**
- Integrate ML inference/training into native systems.

**Logic/Concept**
- Graph/runtime optimized for tensor operations.

**Example**
```cpp
// TensorFlow C++ integration is usually build-heavy and project-specific.
```

**Behavior of Example**
- Enables native ML pipelines when configured.

### pybind11
**What**
- Lightweight C++ bindings for Python.

**Why**
- Expose C++ performance code to Python users.

**Logic/Concept**
- Header-only wrappers map C++ types/functions to Python objects.

**Example**
```cpp
#include <pybind11/pybind11.h>

int add(int a, int b) { return a + b; }

PYBIND11_MODULE(example, m) {
    m.def("add", &add);
}
```

**Behavior of Example**
- Python can import module and call `add`.

### spdlog
**What**
- Fast structured/unstructured logging library.

**Why**
- Better logging performance and flexibility than plain iostream.

**Logic/Concept**
- Supports async logging and sinks.

**Example**
```cpp
#include <spdlog/spdlog.h>
spdlog::info("Service started on port {}", 8080);
```

**Behavior of Example**
- Prints formatted log line.

### fmt
**What**
- Type-safe formatting library.

**Why**
- Cleaner and safer formatting than `printf`.

**Logic/Concept**
- Format string checked with strong typing.

**Example**
```cpp
#include <fmt/core.h>
auto s = fmt::format("User {}", 42);
```

**Behavior of Example**
- Produces string `User 42`.

### opencl
**What**
- API for heterogeneous compute on CPU/GPU.

**Why**
- Accelerate parallel numeric workloads.

**Logic/Concept**
- Kernel code executes on compute devices.

**Example**
```cpp
// Setup includes OpenCL context, command queue, kernel compilation.
```

**Behavior of Example**
- Offloads selected computation to device.

### ranges_v3
**What**
- Ranges library precursor for C++20 ranges.

**Why**
- Composable lazy sequence transformations.

**Logic/Concept**
- View pipelines avoid eager intermediate containers.

**Example**
```cpp
// Conceptual: vec | views::filter(...) | views::transform(...)
```

**Behavior of Example**
- Applies transformation pipeline lazily.

---

## 23. Frameworks and Testing

### gtest / gmock
**What**
- Google Test and Mock framework.

**Why**
- Unit tests and mock-based behavior testing.

**Logic/Concept**
- Arrange, Act, Assert style with rich assertions.

**Example**
```cpp
#include <gtest/gtest.h>

TEST(MathTest, Add) {
    EXPECT_EQ(2 + 3, 5);
}
```

**Behavior of Example**
- Test passes if expression is true.

### Qt
**What**
- Cross-platform C++ UI/application framework.

**Why**
- Build desktop apps with widgets/QML.

**Logic/Concept**
- Signal-slot mechanism for event-driven programming.

**Example**
```cpp
// Qt app setup typically uses qmake/CMake + moc-generated code.
```

**Behavior of Example**
- Provides full application framework for GUI and beyond.

### Catch2
**What**
- Lightweight modern C++ test framework.

**Why**
- Easy setup and expressive test syntax.

**Logic/Concept**
- Single-header-friendly architecture (historically) and modern package support.

**Example**
```cpp
#include <catch2/catch_test_macros.hpp>

TEST_CASE("sum") {
    REQUIRE(1 + 1 == 2);
}
```

**Behavior of Example**
- Test framework reports pass/fail in runner output.

### Orbit Profiler
**What**
- Sampling profiler (commonly for game/perf diagnosis).

**Why**
- Understand CPU hotspots and frame-time bottlenecks.

**Logic/Concept**
- Captures sampled stack traces and timeline events.

**Example**
```txt
Attach profiler to running process and inspect hot functions.
```

**Behavior of Example**
- Identifies expensive functions for optimization.

### PyTorch C++
**What**
- LibTorch C++ frontend for PyTorch.

**Why**
- Deploy ML inference in C++ applications.

**Logic/Concept**
- Uses tensors/modules similarly to Python API.

**Example**
```cpp
#include <torch/torch.h>

auto t = torch::rand({2, 3});
```

**Behavior of Example**
- Creates random tensor in native C++ runtime.

---

## Practical Build and Debug Commands

```bash
# GCC/Clang debug build
g++ main.cpp -std=c++20 -g -O0 -Wall -Wextra -pedantic -o app

# Address + UB sanitizer (Clang/GCC)
clang++ main.cpp -std=c++20 -g -O1 -fsanitize=address,undefined -fno-omit-frame-pointer -o app

# Run and inspect crash stack in gdb
gdb ./app

# MSVC
cl /std:c++20 /EHsc /Zi /W4 main.cpp
```

---

## Recommended Defaults
- Use C++20 minimum for new code.
- Prefer RAII and standard containers over raw memory management.
- Prefer `unique_ptr` for ownership; use `shared_ptr` only when shared ownership is real.
- Enable warnings and treat as errors in CI.
- Use sanitizer builds in tests.
- Use CMake + Ninja for scalable builds.
- Write tests from day one (gtest/Catch2).
