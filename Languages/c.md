# C Cheatsheet

Comprehensive end-to-end reference for C (C99, C11, C17) from low-level memory layout, pointer mechanics, and the compilation pipeline (Makefile) to heap allocation, struct padding, bit manipulation, POSIX threads, and systems programming.

---

## Table of Contents
- [High Priority Topics](#high-priority-topics)
- [1 C Compilation Pipeline, Toolchains, and Makefile](#1-c-compilation-pipeline-toolchains-and-makefile)
- [2 Memory Layout of a C Program](#2-memory-layout-of-a-c-program)
- [3 Data Types, Sizes, and Representation](#3-data-types-sizes-and-representation)
- [4 Pointers Mastery, Pointer Arithmetic, and Void Pointers](#4-pointers-mastery-pointer-arithmetic-and-void-pointers)
- [5 Arrays, Strings, and Multidimensional Arrays](#5-arrays-strings-and-multidimensional-arrays)
- [6 Dynamic Memory Allocation (Heap Management)](#6-dynamic-memory-allocation-heap-management)
- [7 Structures, Unions, Bit-Fields, and Memory Alignment](#7-structures-unions-bit-fields-and-memory-alignment)
- [8 Functions, Recursion, and Function Pointers](#8-functions-recursion-and-function-pointers)
- [9 Storage Classes and Variable Scope](#9-storage-classes-and-variable-scope)
- [10 Type Qualifiers: const, volatile, and restrict](#10-type-qualifiers-const-volatile-and-restrict)
- [11 The C Preprocessor in Depth](#11-the-c-preprocessor-in-depth)
- [12 Bit Manipulation and Bitwise Operators](#12-bit-manipulation-and-bitwise-operators)
- [13 File Handling and Standard I/O](#13-file-handling-and-standard-io)
- [14 Error Handling, Signals, and System Calls](#14-error-handling-signals-and-system-calls)
- [15 Low-Level Concurrency: POSIX Threads (pthreads)](#15-low-level-concurrency-posix-threads-pthreads)
- [16 Debugging, Sanitizers, and Profiling](#16-debugging-sanitizers-and-profiling)
- [17 High-Yield Interview Questions and Reality Check](#17-high-yield-interview-questions-and-reality-check)

---

## High Priority Topics

Most asked in C interviews and low-level / embedded / OS engineering:
1. **Pointers, Double Pointers (`T**`), Pointer Arithmetic & `void*`**
2. **Memory Layout (Text, Data, BSS, Heap, Stack) & Stack Overflow**
3. **Heap Allocation (`malloc`, `calloc`, `realloc`, `free`), Memory Leaks & Dangling Pointers**
4. **Array-Pointer Decay & String Termination (`\0`)**
5. **Structure Padding, Alignment & `#pragma pack(1)`**
6. **Function Pointers & Callback Mechanisms (e.g. `qsort`)**
7. **Storage Classes (`static` local vs global, `extern`)**
8. **Type Qualifiers: `const`, `volatile`, and `restrict`**
9. **Bitwise Operations (Bitmasking, Set/Clear/Toggle, Endianness Check)**
10. **Preprocessor Macros (Parenthesis Safety, `#` Stringification, `##` Token Pasting)**

---

## 1 C Compilation Pipeline, Toolchains, and Makefile

### The 4 Compilation Stages
```
Source File (.c) + Header Files (.h)
       │
       ▼ [1. Preprocessor (gcc -E)]
Expands macros, includes headers, removes comments ──► Preprocessed Code (.i)
       │
       ▼ [2. Compiler (gcc -S)]
Translates C code into assembly instructions ──► Assembly File (.s)
       │
       ▼ [3. Assembler (gcc -c)]
Converts assembly into machine bytecode ──► Object File (.o)
       │
       ▼ [4. Linker (gcc / ld)]
Links object files with C Runtime (CRT) & libraries ──► Executable (a.out / app.exe)
```

### Production `Makefile`
```makefile
CC = gcc
CFLAGS = -Wall -Wextra -Wpedantic -O2 -std=c17
DEBUG_FLAGS = -g -fsanitize=address,undefined
TARGET = bin/app
SRCS = $(wildcard src/*.c)
OBJS = $(patsubst src/%.c, obj/%.o, $(SRCS))

# Default target
all: $(TARGET)

$(TARGET): $(OBJS) | bin
	$(CC) $(CFLAGS) $(OBJS) -o $@

obj/%.o: src/%.c | obj
	$(CC) $(CFLAGS) -Iinclude -c $< -o $@

bin obj:
	mkdir -p $@

clean:
	rm -rf bin obj

.PHONY: all clean
```

---

## 2 Memory Layout of a C Program

```
┌────────────────────────────────────────────────────────┐
│ High Memory Address (0xFFFFFFFF on 32-bit)             │
├────────────────────────────────────────────────────────┤
│ Command-Line Arguments & Environment Variables         │
├────────────────────────────────────────────────────────┤
│ Stack Frame (Local variables, return addresses) [Down] │
│                         │                              │
│                         ▼                              │
│                         ▲                              │
│                         │                              │
│ Heap (malloc/calloc allocations, dynamic memory) [Up]  │
├────────────────────────────────────────────────────────┤
│ BSS Segment (Uninitialized global / static variables)  │
│             (Initialized to 0 by OS at startup)        │
├────────────────────────────────────────────────────────┤
│ Data Segment (Initialized global / static variables)   │
├────────────────────────────────────────────────────────┤
│ Text Segment (Read-only machine instructions / Code)   │
├────────────────────────────────────────────────────────┤
│ Low Memory Address (0x00000000)                        │
└────────────────────────────────────────────────────────┘
```

---

## 3 Data Types, Sizes, and Representation

### Primitive Types (Standard 64-bit Architecture)
| Type | Size (Typical) | Range | Format Specifier |
| :--- | :--- | :--- | :--- |
| `char` | 1 byte (8-bit) | -128 to 127 (or 0 to 255) | `%c` |
| `short` | 2 bytes (16-bit) | -32,768 to 32,767 | `%hd` |
| `int` | 4 bytes (32-bit) | -2,147,483,648 to 2,147,483,647 | `%d` or `%i` |
| `long` | 4 or 8 bytes | Platform dependent | `%ld` |
| `long long` | 8 bytes (64-bit) | -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 | `%lld` |
| `unsigned int` | 4 bytes | 0 to 4,294,967,295 | `%u` |
| `float` | 4 bytes | 6-7 decimal digits precision | `%f` |
| `double` | 8 bytes | 15-17 decimal digits precision | `%lf` |
| `size_t` | 8 bytes | Unsigned size type | `%zu` |

### Fixed-Width Integer Types (`<stdint.h>`)
Use `<stdint.h>` for portable, explicit-width integers across all CPU architectures:
```c
#include <stdint.h>

int8_t   smallVal  = -128;
uint8_t  byteVal   = 255;
int32_t  normalVal = 100000;
uint64_t largeVal  = 18446744073709551615ULL;
uintptr_t ptrAsInt = (uintptr_t)&normalVal; // Integer capable of holding a pointer
```

---

## 4 Pointers Mastery, Pointer Arithmetic, and Void Pointers

### Pointer Fundamentals
```c
#include <stdio.h>

void pointerBasics(void) {
    int val = 42;
    int* ptr = &val;   // & = Address-of operator
    int** dptr = &ptr; // Double pointer (pointer to pointer)

    printf("Value: %d\n", *ptr);     // * = Dereference operator (42)
    printf("Via Double Pointer: %d\n", **dptr); // 42
}
```

### Pointer Arithmetic
Pointer arithmetic automatically scales offsets by `sizeof(Type)`:
```c
int arr[] = {10, 20, 30, 40};
int* p = arr;

printf("%d\n", *(p + 1)); // 20 (Advances by 1 * sizeof(int) = 4 bytes)
printf("%d\n", *(p + 3)); // 40
```

### Generic Raw Pointers (`void*`)
A `void*` holds memory addresses without type information (cannot be dereferenced directly without casting).
```c
void printGeneric(void* data, char type) {
    if (type == 'i') {
        printf("Integer: %d\n", *(int*)data);
    } else if (type == 'c') {
        printf("Char: %c\n", *(char*)data);
    }
}
```

### `const` Pointer Variations
```c
int x = 10, y = 20;

const int* p1 = &x;       // Pointer to const int (Cannot modify value: *p1 = 30 fails)
int* const p2 = &x;       // Const pointer to int (Cannot change address: p2 = &y fails)
const int* const p3 = &x; // Const pointer to const int (Neither can change)
```

---

## 5 Arrays, Strings, and Multidimensional Arrays

### Array-to-Pointer Decay
When passed to functions, arrays **decay** into a pointer to their first element.

```c
#include <stdio.h>

// 'arr' decays to 'int* arr'; sizeof(arr) returns pointer size (8 bytes), NOT array size!
void printArray(int arr[], size_t len) {
    for (size_t i = 0; i < len; i++) {
        printf("%d ", arr[i]); // Equiv to *(arr + i)
    }
    printf("\n");
}
```

### Strings and `<string.h>` Security
Strings in C are character arrays terminated with the null byte `\0`.

```c
#include <stdio.h>
#include <string.h>

void stringOperations(void) {
    char str[20] = "Hello";
    
    // String length (excluding \0)
    size_t len = strlen(str); // 5

    // Safe string formatting & concatenation (Prevents buffer overflow)
    snprintf(str, sizeof(str), "User ID: %d", 101);

    // Comparison
    if (strcmp(str, "User ID: 101") == 0) {
        printf("Strings match\n");
    }
}
```

---

## 6 Dynamic Memory Allocation (Heap Management)

### `malloc`, `calloc`, `realloc`, and `free`
```c
#include <stdio.h>
#include <stdlib.h>

void memoryDemo(void) {
    // 1. malloc: Allocates uninitialized memory (Contains garbage values)
    int* p1 = (int*)malloc(5 * sizeof(int));
    if (p1 == NULL) {
        perror("Allocation failed");
        return;
    }

    // 2. calloc: Allocates memory AND initializes all bytes to ZERO
    int* p2 = (int*)calloc(5, sizeof(int));

    // 3. realloc: Resizes memory block (may move to new address)
    int* temp = (int*)realloc(p1, 10 * sizeof(int));
    if (temp != NULL) {
        p1 = temp; // Reassign only on success to avoid leaking original p1
    }

    // 4. free: Returns memory to OS heap
    free(p1);
    free(p2);
    p1 = NULL; // Best Practice: Set to NULL to prevent Dangling Pointer bugs
    p2 = NULL;
}
```

---

## 7 Structures, Unions, Bit-Fields, and Memory Alignment

### Struct Padding & Byte Alignment
CPython/C compilers align struct fields to natural CPU word boundaries for performance, inserting hidden padding bytes.

```c
#include <stdio.h>

struct Unpadded {
    char a;    // 1 byte + 3 padding bytes
    int b;     // 4 bytes
    char c;    // 1 byte + 3 padding bytes
}; // Total: 12 bytes!

struct Optimized {
    int b;     // 4 bytes
    char a;    // 1 byte
    char c;    // 1 byte + 2 padding bytes
}; // Total: 8 bytes!

// Packed Struct (No padding - for network/hardware protocols)
#pragma pack(push, 1)
struct PackedStruct {
    char a;
    int b;
    char c;
}; // Total: 6 bytes!
#pragma pack(pop)
```

### Unions and Bit-Fields
```c
// Union: All members share the EXACT same memory location
union DataPacket {
    int intVal;
    float floatVal;
    char bytes[4];
};

// Bit-Fields: Explicit bit allocation for hardware registers
struct HardwareRegister {
    unsigned int enable : 1; // 1 bit
    unsigned int mode   : 3; // 3 bits (0-7)
    unsigned int ready  : 1; // 1 bit
    unsigned int unused : 3; // 3 bits padding
};
```

---

## 8 Functions, Recursion, and Function Pointers

### Function Pointers & Callback Architecture
Function pointers store the memory address of executable code in the Text segment.

```c
#include <stdio.h>
#include <stdlib.h>

int compareDesc(const void* a, const void* b) {
    int intA = *(const int*)a;
    int intB = *(const int*)b;
    return intB - intA; // Descending order
}

int main(void) {
    int nums[] = {50, 20, 80, 10, 30};
    size_t count = sizeof(nums) / sizeof(nums[0]);

    // qsort uses function pointer callback for comparison
    qsort(nums, count, sizeof(int), compareDesc);

    for (size_t i = 0; i < count; i++) {
        printf("%d ", nums[i]); // 80 50 30 20 10
    }
    printf("\n");
    return 0;
}
```

---

## 9 Storage Classes and Variable Scope

| Storage Class | Location | Scope | Lifetime | Default Initial Value |
| :--- | :--- | :--- | :--- | :--- |
| `auto` | Stack | Local block | Block execution | Garbage |
| `register` | CPU Register / Stack | Local block | Block execution | Garbage |
| `static` (local) | Data / BSS | Local block | **Entire Program** | `0` |
| `static` (global)| Data / BSS | **File Only** (Internal Linkage) | Entire Program | `0` |
| `extern` | Data / BSS | **Global** (External Linkage) | Entire Program | Defined elsewhere |

```c
void counterDemo(void) {
    static int callCount = 0; // Initialized ONCE at program startup
    callCount++;
    printf("Function called %d times\n", callCount);
}
```

---

## 10 Type Qualifiers: const, volatile, and restrict

### `volatile`
Tells the compiler **NOT to optimize reads/writes to memory**, because the value can be modified externally outside the program's control (e.g. Memory-Mapped Hardware Registers, Interrupt Service Routines, Multi-threaded shared flags).

```c
// Memory-mapped hardware status register
volatile uint32_t* const UART_STATUS = (uint32_t*)0x40001000;

void waitForData(void) {
    while ((*UART_STATUS & 0x01) == 0) {
        // Compiler will NOT optimize this into an infinite loop!
    }
}
```

### `restrict` (C99)
Informs the compiler that for the lifetime of the pointer, only that pointer (or values derived from it) will access the pointed-to memory. Enables aggressive SIMD vectorization.

```c
void vectorAdd(int* restrict a, int* restrict b, int* restrict result, size_t n) {
    for (size_t i = 0; i < n; i++) {
        result[i] = a[i] + b[i]; // Compiler knows a, b, and result DO NOT overlap in memory!
    }
}
```

---

## 11 The C Preprocessor in Depth

### Parenthesis Safety in Macros
Always wrap macro parameters and the full expression in parentheses to prevent operator precedence bugs.

```c
// BAD: SQUARE(2 + 3) expands to 2 + 3 * 2 + 3 = 11 (Wrong!)
#define SQUARE_BAD(x) x * x

// GOOD: SQUARE_GOOD(2 + 3) expands to ((2 + 3) * (2 + 3)) = 25
#define SQUARE_GOOD(x) ((x) * (x))
```

### Stringification (`#`) and Token Pasting (`##`)
```c
#include <stdio.h>

// # converts parameter to string literal
#define PRINT_INT(var) printf(#var " = %d\n", var)

// ## concatenates two tokens into a single identifier
#define MAKE_VAR(name, num) name##num

int main(void) {
    int MAKE_VAR(score, 1) = 100; // Declares 'int score1 = 100;'
    PRINT_INT(score1);            // Prints "score1 = 100"
    return 0;
}
```

---

## 12 Bit Manipulation and Bitwise Operators

### Common Bitwise Recipes
```c
#include <stdio.h>

#define SET_BIT(val, bit)    ((val) |= (1ULL << (bit)))
#define CLEAR_BIT(val, bit)  ((val) &= ~(1ULL << (bit)))
#define TOGGLE_BIT(val, bit) ((val) ^= (1ULL << (bit)))
#define CHECK_BIT(val, bit)  (((val) >> (bit)) & 1ULL)

// Check if integer is power of 2
int isPowerOfTwo(unsigned int n) {
    return (n > 0) && ((n & (n - 1)) == 0);
}

// Endianness Check at Runtime
int isLittleEndian(void) {
    unsigned int x = 1;
    char* c = (char*)&x;
    return (int)(*c); // Returns 1 if Little-Endian, 0 if Big-Endian
}
```

---

## 13 File Handling and Standard I/O

```c
#include <stdio.h>

void fileExample(void) {
    FILE* file = fopen("app.log", "w");
    if (file == NULL) {
        perror("Failed to open file");
        return;
    }

    fprintf(file, "STATUS: SUCCESS, CODE: %d\n", 200);
    fclose(file); // Flush and close

    // Reading with fgets (Buffer-safe)
    char buffer[128];
    FILE* readStream = fopen("app.log", "r");
    if (readStream != NULL) {
        while (fgets(buffer, sizeof(buffer), readStream) != NULL) {
            printf("Read: %s", buffer);
        }
        fclose(readStream);
    }
}
```

---

## 14 Error Handling, Signals, and System Calls

```c
#include <stdio.h>
#include <errno.h>
#include <string.h>
#include <signal.h>
#include <stdlib.h>

void signalHandler(int signum) {
    printf("\nCaught signal %d (SIGINT). Exiting safely...\n", signum);
    exit(0);
}

int main(void) {
    // Register interrupt signal (Ctrl+C)
    signal(SIGINT, signalHandler);

    FILE* f = fopen("non_existent_file.txt", "r");
    if (f == NULL) {
        // Inspect errno
        printf("Error code: %d\n", errno);
        printf("Error string: %s\n", strerror(errno));
        perror("Custom prefix");
    }

    return 0;
}
```

---

## 15 Low-Level Concurrency: POSIX Threads (pthreads)

```c
#include <stdio.h>
#include <pthread.h>

#define NUM_THREADS 4
#define ITERATIONS 100000

long g_counter = 0;
pthread_mutex_t g_lock;

void* threadWorker(void* arg) {
    (void)arg;
    for (int i = 0; i < ITERATIONS; i++) {
        pthread_mutex_lock(&g_lock);
        g_counter++;
        pthread_mutex_unlock(&g_lock);
    }
    return NULL;
}

int main(void) {
    pthread_t threads[NUM_THREADS];
    pthread_mutex_init(&g_lock, NULL);

    for (int i = 0; i < NUM_THREADS; i++) {
        pthread_create(&threads[i], NULL, threadWorker, NULL);
    }

    for (int i = 0; i < NUM_THREADS; i++) {
        pthread_join(threads[i], NULL);
    }

    pthread_mutex_destroy(&g_lock);
    printf("Final Counter: %ld (Expected: %d)\n", g_counter, NUM_THREADS * ITERATIONS);
    return 0;
}
```

---

## 16 Debugging, Sanitizers, and Profiling

### Compiler Sanitizer Flags
Compile with GCC/Clang sanitizers to catch memory bugs instantly at runtime:
```bash
gcc -fsanitize=address -fsanitize=undefined -g -O1 main.c -o app
./app
```

### Valgrind Memory Leak Checker
```bash
valgrind --leak-check=full --show-leak-kinds=all --track-origins=yes ./app
```

---

## 17 High-Yield Interview Questions and Reality Check

### 1. What is the difference between an Array and a Pointer in C?
> **Answer**: An array is a single contiguous block of memory with fixed size allocated at compile time; `sizeof(arr)` yields total byte size. A pointer is a variable that stores a memory address; `sizeof(ptr)` yields pointer size (8 bytes on 64-bit systems). In most expressions (and when passed as function arguments), arrays **decay** into a pointer to their first element (`&arr[0]`).

### 2. What is the difference between `malloc()` and `calloc()`?
> **Answer**: `malloc(size)` allocates `size` bytes of raw, uninitialized memory containing indeterminate garbage values. `calloc(num, size)` allocates `num * size` bytes and initializes **every single byte to zero**, which carries a small initialization overhead.

### 3. What is a Dangling Pointer, a Wild Pointer, and a Memory Leak?
> **Answer**:
> - **Dangling Pointer**: A pointer pointing to memory that has already been deallocated via `free()` or an out-of-scope stack address.
> - **Wild Pointer**: An uninitialized pointer pointing to an arbitrary random memory address.
> - **Memory Leak**: Heap memory allocated via `malloc`/`calloc` that is no longer reachable by any pointer and was never released with `free()`.

### 4. What is Structure Padding and why does it happen?
> **Answer**: Modern CPUs read data from memory in 4-byte or 8-byte word boundaries for speed. The compiler inserts padding bytes between struct members to align each data type to an address that is a multiple of its size (e.g. 4-byte `int` aligned to 4-byte boundary). This can be disabled using `#pragma pack(1)` at the cost of slower unaligned memory access.

### 5. Why should `volatile` be used in embedded / systems programming?
> **Answer**: `volatile` prevents the compiler from caching variable values in CPU registers or optimizing away repetitive reads/writes. It is required when interacting with Memory-Mapped I/O hardware registers, variables modified inside interrupt service routines (ISRs), or asynchronous signal handlers.

---

### Reality Check & Best Practices
- Always check if `malloc()` / `calloc()` returned `NULL` before dereferencing.
- Set pointers to `NULL` immediately after calling `free(ptr)` to prevent double-free and dangling pointer bugs.
- Always use `snprintf()` and `fgets()` instead of unsafe functions like `sprintf()`, `strcpy()`, and `gets()`.
- Use `<stdint.h>` types (`int32_t`, `uint64_t`) for binary protocols and hardware cross-compilation.
- Always compile with `-Wall -Wextra -Wpedantic` and test with AddressSanitizer (`-fsanitize=address`).
