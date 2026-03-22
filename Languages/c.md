# C Interview Topics (Core + Low-Level)

Focused interview reference for C language.
Each topic follows the same structure: What, Example, Logic, Input, Output.

## Quick Links
- [High Priority Topics](#high-priority-topics)
- [1 Basics](#1-basics)
- [2 Operators](#2-operators)
- [3 Control Flow](#3-control-flow)
- [4 Functions](#4-functions)
- [5 Pointers Most Important](#5-pointers-most-important)
- [6 Arrays](#6-arrays)
- [7 Strings](#7-strings)
- [8 Memory Management](#8-memory-management)
- [9 Structures Unions](#9-structures-unions)
- [10 File Handling](#10-file-handling)
- [11 Storage Classes](#11-storage-classes)
- [12 Preprocessor Very Important](#12-preprocessor-very-important)
- [13 Header Files](#13-header-files)
- [14 Type Casting](#14-type-casting)
- [15 Bit Manipulation](#15-bit-manipulation)
- [16 const Keyword](#16-const-keyword)
- [17 volatile Keyword](#17-volatile-keyword)
- [18 Memory Layout](#18-memory-layout)
- [19 Function Pointers](#19-function-pointers)
- [20 CLI Arguments](#20-cli-arguments)
- [21 File Extensions](#21-file-extensions)
- [Ultra-Short Priority](#ultra-short-priority)
- [Reality Check](#reality-check)

---

## High Priority Topics

Most asked in interviews:
- Pointers
- Memory management (malloc/free)
- Structures
- Preprocessor
- Array vs Pointer difference

---

## 1 Basics

### Data Types

#### What
Data types define size, range, and meaning of stored values.

#### Example
```c
int age = 25;
float pi = 3.14f;
char grade = 'A';
```

#### Logic
Compiler allocates memory based on declared type.

#### Input
age = 25, pi = 3.14, grade = A

#### Output
Typed variables available for calculations and logic

### Variables

#### What
Variables are named memory locations.

#### Example
```c
int a = 10;
int b = 20;
int sum = a + b;
```

#### Logic
Program reads values from memory and stores result in sum.

#### Input
a = 10, b = 20

#### Output
sum = 30

### Constants

#### What
Constants are values that should not change.

#### Example
```c
const int MAX_USERS = 100;
```

#### Logic
Compiler prevents accidental writes to const objects.

#### Input
MAX_USERS = 100

#### Output
Read-only value used safely

### Keywords

#### What
Keywords are reserved words with predefined meaning in C.

#### Example
```c
int main(void) {
    return 0;
}
```

#### Logic
Words like int, return, if, while cannot be used as variable names.

#### Input
Use reserved words correctly in program

#### Output
Valid compilation

---

## 2 Operators

### Arithmetic

#### What
Arithmetic operators perform math operations.

#### Example
```c
int a = 7, b = 3;
printf("%d", a + b);
```

#### Logic
+, -, *, /, % compute numeric results.

#### Input
a = 7, b = 3

#### Output
10

### Relational

#### What
Relational operators compare two values.

#### Example
```c
int x = 5, y = 8;
printf("%d", x < y);
```

#### Logic
Comparison returns 1 (true) or 0 (false).

#### Input
x = 5, y = 8

#### Output
1

### Logical

#### What
Logical operators combine conditions.

#### Example
```c
int age = 20;
printf("%d", age > 18 && age < 60);
```

#### Logic
&&, ||, ! evaluate boolean-like expressions.

#### Input
age = 20

#### Output
1

### Bitwise

#### What
Bitwise operators work at individual bit level.

#### Example
```c
int a = 5, b = 3;
printf("%d", a & b);
```

#### Logic
5 is 0101 and 3 is 0011, AND gives 0001.

#### Input
a = 5, b = 3

#### Output
1

### Assignment

#### What
Assignment operators store/update values.

#### Example
```c
int x = 10;
x += 5;
printf("%d", x);
```

#### Logic
x += 5 means x = x + 5.

#### Input
x starts 10

#### Output
15

### Unary

#### What
Unary operators act on one operand.

#### Example
```c
int x = 5;
printf("%d %d", ++x, -x);
```

#### Logic
++ increments; unary minus negates value.

#### Input
x = 5

#### Output
6 -6

---

## 3 Control Flow

### if else

#### What
Conditional branching for two or more paths.

#### Example
```c
int marks = 40;
if (marks >= 33) {
    printf("Pass");
} else {
    printf("Fail");
}
```

#### Logic
Condition decides which block executes.

#### Input
marks = 40

#### Output
Pass

### switch

#### What
Multi-way branch for constant integral cases.

#### Example
```c
int n = 2;
switch (n) {
    case 1: printf("One"); break;
    case 2: printf("Two"); break;
    default: printf("Other");
}
```

#### Logic
Matching case runs; break avoids fall-through.

#### Input
n = 2

#### Output
Two

### for loop

#### What
Loop with init, condition, update.

#### Example
```c
for (int i = 0; i < 3; i++) {
    printf("%d ", i);
}
```

#### Logic
Repeats while condition stays true.

#### Input
i from 0 to 2

#### Output
0 1 2

### while loop

#### What
Pre-condition loop.

#### Example
```c
int i = 0;
while (i < 2) {
    printf("%d ", i);
    i++;
}
```

#### Logic
Condition checked before each iteration.

#### Input
i starts 0

#### Output
0 1

### do while

#### What
Post-condition loop that runs at least once.

#### Example
```c
int i = 5;
do {
    printf("%d", i);
} while (i < 3);
```

#### Logic
Body executes first, then condition is checked.

#### Input
i = 5

#### Output
5

---

## 4 Functions

### Function declaration

#### What
Function prototype tells compiler signature.

#### Example
```c
int add(int a, int b);
```

#### Logic
Allows calling function before its definition appears.

#### Input
Function signature only

#### Output
Compiler knows return type and parameters

### Function definition

#### What
Function body with implementation.

#### Example
```c
int add(int a, int b) {
    return a + b;
}
```

#### Logic
Execution enters function, computes result, returns it.

#### Input
a = 4, b = 6

#### Output
10

### Recursion

#### What
Function calling itself with smaller subproblem.

#### Example
```c
int fact(int n) {
    if (n <= 1) return 1;
    return n * fact(n - 1);
}
```

#### Logic
Base case stops recursion; recursive case reduces n.

#### Input
n = 5

#### Output
120

### Call by value

#### What
C passes arguments by value (copies).

#### Example
```c
void inc(int x) {
    x++;
}

int a = 5;
inc(a);
printf("%d", a);
```

#### Logic
Only copied value changes inside function.

#### Input
a = 5

#### Output
5

---

## 5 Pointers Most Important

### Pointer basics

#### What
Pointer stores memory address of another variable.

#### Example
```c
int x = 10;
int *p = &x;
printf("%d", *p);
```

#### Logic
&p gives address; *p dereferences address to value.

#### Input
x = 10

#### Output
10

### Pointer arithmetic

#### What
Pointer can move through contiguous memory (arrays).

#### Example
```c
int arr[] = {10, 20, 30};
int *p = arr;
printf("%d ", *p);
p++;
printf("%d", *p);
```

#### Logic
Increment moves pointer by sizeof(type).

#### Input
arr = {10,20,30}

#### Output
10 20

### Pointer to pointer

#### What
Pointer holding address of another pointer.

#### Example
```c
int x = 7;
int *p = &x;
int **pp = &p;
printf("%d", **pp);
```

#### Logic
Double dereference reaches original value.

#### Input
x = 7

#### Output
7

### Void pointer

#### What
Generic pointer type that can hold any address.

#### Example
```c
int x = 42;
void *vp = &x;
printf("%d", *(int *)vp);
```

#### Logic
Must cast void pointer before dereference.

#### Input
vp points to int value 42

#### Output
42

### Null pointer

#### What
Pointer that intentionally points to no valid memory.

#### Example
```c
int *p = NULL;
if (p == NULL) {
    printf("Null");
}
```

#### Logic
Used as safe sentinel value.

#### Input
p initialized NULL

#### Output
Null

### Dangling pointer

#### What
Pointer pointing to freed or out-of-scope memory.

#### Example
```c
int *p = (int *)malloc(sizeof(int));
*p = 5;
free(p);
/* p is now dangling */
p = NULL;
```

#### Logic
After free, old address is invalid; reset pointer.

#### Input
Allocate then free

#### Output
Safe state after p = NULL

### Wild pointer

#### What
Uninitialized pointer with garbage address.

#### Example
```c
int *p;
/* wild pointer until initialized */
p = NULL;
```

#### Logic
Dereferencing uninitialized pointer causes undefined behavior.

#### Input
Pointer declared without assignment

#### Output
Undefined behavior if used before init

---

## 6 Arrays

### 1D arrays

#### What
Linear collection of same-type elements.

#### Example
```c
int a[4] = {1, 2, 3, 4};
printf("%d", a[2]);
```

#### Logic
Index-based access from 0.

#### Input
a = {1,2,3,4}

#### Output
3

### 2D arrays

#### What
Array of arrays, matrix-like storage.

#### Example
```c
int m[2][2] = {{1, 2}, {3, 4}};
printf("%d", m[1][0]);
```

#### Logic
Row-major memory layout in C.

#### Input
m = {{1,2},{3,4}}

#### Output
3

### Array vs pointer difference

#### What
Array is fixed block; pointer is variable storing address.

#### Example
```c
int arr[3] = {10, 20, 30};
int *p = arr;
printf("%zu %zu", sizeof(arr), sizeof(p));
```

#### Logic
sizeof(arr) gives full array size; sizeof(p) gives pointer size.

#### Input
int arr[3]

#### Output
Typically 12 and 8 (on 64-bit, int=4 bytes)

---

## 7 Strings

### Character arrays

#### What
C string is char array ending with null terminator '\0'.

#### Example
```c
char name[] = "Aman";
printf("%s", name);
```

#### Logic
String functions read until '\0'.

#### Input
"Aman"

#### Output
Aman

### String functions strlen strcpy etc

#### What
Library functions for common string operations.

#### Example
```c
#include <string.h>

char src[] = "abc";
char dst[10];
strcpy(dst, src);
printf("%zu %s", strlen(dst), dst);
```

#### Logic
strlen counts chars excluding '\0'; strcpy copies including '\0'.

#### Input
src = "abc"

#### Output
3 abc

---

## 8 Memory Management

### Stack vs Heap

#### What
Stack stores automatic variables; heap stores dynamic allocations.

#### Example
```c
int a = 10;                     /* stack */
int *p = (int *)malloc(sizeof(int)); /* heap */
*p = 20;
printf("%d %d", a, *p);
free(p);
```

#### Logic
Stack auto-cleans; heap must be manually freed.

#### Input
a = 10, heap value = 20

#### Output
10 20

### malloc

#### What
Allocates uninitialized memory block.

#### Example
```c
int *p = (int *)malloc(3 * sizeof(int));
```

#### Logic
Memory may contain garbage until assigned.

#### Input
Request memory for 3 ints

#### Output
Pointer to allocated block or NULL

### calloc

#### What
Allocates and zero-initializes memory.

#### Example
```c
int *p = (int *)calloc(3, sizeof(int));
printf("%d", p[0]);
free(p);
```

#### Logic
All bytes set to zero initially.

#### Input
3 elements of int

#### Output
0

### realloc

#### What
Resizes previously allocated block.

#### Example
```c
int *p = (int *)malloc(2 * sizeof(int));
p = (int *)realloc(p, 4 * sizeof(int));
free(p);
```

#### Logic
May move memory block and return new address.

#### Input
Grow from 2 ints to 4 ints

#### Output
Resized allocation pointer

### free

#### What
Releases dynamic memory back to allocator.

#### Example
```c
int *p = (int *)malloc(sizeof(int));
free(p);
p = NULL;
```

#### Logic
Avoid double free and use-after-free by resetting pointer.

#### Input
Allocated pointer p

#### Output
Memory released

### Memory leaks

#### What
Allocated memory not freed after use.

#### Example
```c
void bad(void) {
    int *p = (int *)malloc(sizeof(int));
    *p = 1;
    /* missing free(p); */
}
```

#### Logic
Losing pointer without free causes leak.

#### Input
Repeated bad() calls

#### Output
Growing memory usage

---

## 9 Structures Unions

### struct

#### What
User-defined type combining multiple fields.

#### Example
```c
struct Student {
    char name[20];
    int age;
};
```

#### Logic
Each member has independent storage.

#### Input
Student with name and age

#### Output
Grouped data in one object

### union

#### What
Members share same memory location.

#### Example
```c
union Data {
    int i;
    float f;
};

union Data d;
d.i = 10;
printf("%d", d.i);
```

#### Logic
Only one member is valid at a time.

#### Input
d.i = 10

#### Output
10

### Nested structures

#### What
Structure containing another structure.

#### Example
```c
struct Date {
    int d, m, y;
};

struct Employee {
    int id;
    struct Date doj;
};
```

#### Logic
Hierarchical modeling of related data.

#### Input
Employee id with embedded Date

#### Output
Single structured record with nested fields

### Structure padding

#### What
Compiler inserts unused bytes for alignment.

#### Example
```c
struct A {
    char c;
    int i;
};

printf("%zu", sizeof(struct A));
```

#### Logic
Alignment may increase struct size beyond sum of fields.

#### Input
char + int struct

#### Output
Often 8 on many systems

---

## 10 File Handling

### FILE pointer

#### What
FILE pointer represents stream handled by stdio library.

#### Example
```c
FILE *fp;
```

#### Logic
Used by file APIs like fopen, fprintf, fread.

#### Input
Declare FILE pointer

#### Output
Handle for file operations

### fopen fclose

#### What
Open and close files.

#### Example
```c
FILE *fp = fopen("data.txt", "w");
if (fp != NULL) {
    fputs("Hi", fp);
    fclose(fp);
}
```

#### Logic
fopen returns NULL on failure; fclose flushes and releases handle.

#### Input
Filename and mode

#### Output
File created/written then closed

### fread fwrite

#### What
Binary block read/write functions.

#### Example
```c
int nums[3] = {1, 2, 3};
FILE *fp = fopen("bin.dat", "wb");
fwrite(nums, sizeof(int), 3, fp);
fclose(fp);
```

#### Logic
Writes raw bytes, not formatted text.

#### Input
Array nums

#### Output
Binary data stored in file

### fprintf fscanf

#### What
Formatted text write/read for files.

#### Example
```c
FILE *fp = fopen("user.txt", "w");
fprintf(fp, "%s %d", "Ava", 21);
fclose(fp);
```

#### Logic
Works like printf/scanf but with FILE stream.

#### Input
Name and age

#### Output
Text line written to file

---

## 11 Storage Classes

### auto

#### What
default local storage class for block variables.

#### Example
```c
auto int x = 5;
printf("%d", x);
```

#### Logic
Automatic lifetime: created on block entry, destroyed on exit.

#### Input
x = 5

#### Output
5

### static

#### What
Preserves variable value across function calls or gives internal linkage at file scope.

#### Example
```c
void counter(void) {
    static int c = 0;
    c++;
    printf("%d ", c);
}
```

#### Logic
Initialized once, lifetime is full program.

#### Input
Call counter three times

#### Output
1 2 3

### extern

#### What
Declares global variable defined in another file.

#### Example
```c
/* file1.c */
int g = 10;

/* file2.c */
extern int g;
```

#### Logic
Enables cross-file access during linking.

#### Input
Global variable in separate translation unit

#### Output
Shared access to g

### register

#### What
Hint to store variable in CPU register for faster access.

#### Example
```c
register int i;
for (i = 0; i < 3; i++) {
    printf("%d ", i);
}
```

#### Logic
Compiler may ignore hint; address-of not allowed historically.

#### Input
Loop variable i

#### Output
0 1 2

---

## 12 Preprocessor Very Important

### include

#### What
Inserts contents of header file before compilation.

#### Example
```c
#include <stdio.h>
#include "my_utils.h"
```

#### Logic
Preprocessor copies file text into current translation unit.

#### Input
System and custom headers

#### Output
Declarations become available

### define

#### What
Defines macros (symbolic constants or function-like expansions).

#### Example
```c
#define PI 3.14159
#define SQUARE(x) ((x) * (x))
```

#### Logic
Pure text substitution before compilation.

#### Input
PI and SQUARE(4)

#### Output
3.14159 and 16

### Macros

#### What
Reusable compile-time text replacements.

#### Example
```c
#define MAX(a, b) ((a) > (b) ? (a) : (b))
printf("%d", MAX(3, 7));
```

#### Logic
Expands inline; careful parentheses prevent precedence bugs.

#### Input
3 and 7

#### Output
7

### Conditional compilation ifdef ifndef

#### What
Include/exclude code based on macro definitions.

#### Example
```c
#ifdef DEBUG
printf("Debug mode\n");
#endif

#ifndef VERSION
#define VERSION 1
#endif
```

#### Logic
Useful for platform-specific code and build flags.

#### Input
Whether DEBUG or VERSION is defined

#### Output
Different code paths compiled

---

## 13 Header Files

### h files

#### What
.h files typically contain declarations and shared interfaces.

#### Example
```c
/* math_utils.h */
int add(int a, int b);
```

#### Logic
Multiple .c files include same declaration contract.

#### Input
Header with function prototypes

#### Output
Type-safe cross-file calls

### Function declarations

#### What
Prototype declarations define function signature for callers.

#### Example
```c
int add(int a, int b);
```

#### Logic
Compiler checks argument and return type usage.

#### Input
Call add from another file

#### Output
Compilation succeeds with correct signature

### Include guards

#### What
Prevents multiple inclusion problems.

#### Example
```c
#ifndef MATH_UTILS_H
#define MATH_UTILS_H

int add(int a, int b);

#endif
```

#### Logic
Header content is processed once per translation unit.

#### Input
Header included indirectly many times

#### Output
No redefinition errors

---

## 14 Type Casting

### Implicit

#### What
Automatic conversion done by compiler.

#### Example
```c
int a = 5;
float f = a;
printf("%.1f", f);
```

#### Logic
Smaller/compatible type promoted silently.

#### Input
a = 5

#### Output
5.0

### Explicit

#### What
Manual type conversion by programmer.

#### Example
```c
int a = 5, b = 2;
float res = (float)a / b;
printf("%.2f", res);
```

#### Logic
Cast changes expression type before evaluation.

#### Input
a = 5, b = 2

#### Output
2.50

---

## 15 Bit Manipulation

### Bitwise operators

#### What
Manipulate binary representation directly.

#### Example
```c
int x = 6;      /* 0110 */
int y = 3;      /* 0011 */
printf("%d", x ^ y);
```

#### Logic
XOR sets bit when bits differ.

#### Input
x = 6, y = 3

#### Output
5

### Shifting << >>

#### What
Shift bits left or right.

#### Example
```c
int x = 4;
printf("%d %d", x << 1, x >> 1);
```

#### Logic
Left shift often multiplies by 2, right shift often divides by 2 (for non-negative ints).

#### Input
x = 4

#### Output
8 2

---

## 16 const Keyword

### const variables

#### What
Read-only variables after initialization.

#### Example
```c
const int limit = 50;
/* limit = 60; */
```

#### Logic
Protects values from accidental modification.

#### Input
limit = 50

#### Output
Compile-time error on write attempt

### const pointer vs pointer to const

#### What
Difference is whether pointer address or pointed data is constant.

#### Example
```c
int a = 10, b = 20;

const int *p1 = &a;  /* pointer to const int: data read-only through p1 */
p1 = &b;             /* allowed */

int *const p2 = &a;  /* const pointer: address fixed */
*p2 = 15;            /* allowed */
/* p2 = &b; */       /* not allowed */
```

#### Logic
Read declaration right-to-left to understand const binding.

#### Input
a = 10, b = 20

#### Output
p1 can re-point, p2 cannot re-point

---

## 17 volatile Keyword

### volatile usage

#### What
Tells compiler value may change unexpectedly outside normal code flow.

#### Example
```c
volatile int flag = 0;
while (flag == 0) {
    /* wait for hardware/ISR update */
}
```

#### Logic
Prevents aggressive optimization that assumes flag never changes.

#### Input
flag updated by interrupt/hardware/thread-like external actor

#### Output
Loop re-reads memory each iteration

---

## 18 Memory Layout

### Code segment

#### What
Holds executable instructions.

#### Example
```c
int add(int a, int b) { return a + b; }
```

#### Logic
Machine code for functions resides in text/code segment.

#### Input
Compiled function

#### Output
Instructions stored in code segment

### Data segment

#### What
Stores global/static initialized and uninitialized data.

#### Example
```c
int g_init = 5;   /* initialized data */
int g_uninit;     /* BSS */
```

#### Logic
Globals/statics have program lifetime.

#### Input
Global/static variables

#### Output
Placed in data/BSS areas

### Stack

#### What
Stores function frames and local automatic variables.

#### Example
```c
void f(void) {
    int x = 10;
    printf("%d", x);
}
```

#### Logic
Stack grows/shrinks with function calls and returns.

#### Input
Call f()

#### Output
Local x exists only during call

### Heap

#### What
Region for dynamic runtime allocation.

#### Example
```c
int *p = (int *)malloc(sizeof(int));
*p = 99;
free(p);
```

#### Logic
Program controls lifetime via malloc/free.

#### Input
Dynamic allocation request

#### Output
Memory available until freed

---

## 19 Function Pointers

### Pointer to function

#### What
Pointer that stores address of a function.

#### Example
```c
int add(int a, int b) { return a + b; }
int (*fp)(int, int) = add;
printf("%d", fp(2, 3));
```

#### Logic
Function pointer enables indirect invocation.

#### Input
2 and 3

#### Output
5

### Callback pattern

#### What
Pass function pointer to another function for custom behavior.

#### Example
```c
int op(int a, int b, int (*fn)(int, int)) {
    return fn(a, b);
}

int mul(int x, int y) { return x * y; }
printf("%d", op(3, 4, mul));
```

#### Logic
Caller injects behavior through callback function.

#### Input
3, 4, callback = mul

#### Output
12

---

## 20 CLI Arguments

### argc argv

#### What
Command-line arguments passed to main.

#### Example
```c
int main(int argc, char *argv[]) {
    printf("%d", argc);
    return 0;
}
```

#### Logic
argc is count, argv is array of argument strings.

#### Input
Run: ./app hello 123

#### Output
3

---

## 21 File Extensions

### c

#### What
.c file contains C source code definitions.

#### Example
```text
main.c
```

#### Logic
Compiled to object code then linked.

#### Input
C source file

#### Output
Object/executable after build

### h

#### What
.h file contains shared declarations.

#### Example
```text
utils.h
```

#### Logic
Included by .c files to share interfaces.

#### Input
Header declarations

#### Output
Cross-file compile-time visibility

---

## Ultra-Short Priority

Pointers
Memory (malloc/free)
Structures
Preprocessor
Array vs Pointer

---

## Reality Check

C interview usually gets decided by:
- Pointer understanding
- Memory understanding
- Array vs pointer difference

If these are strong, most core interview rounds become manageable.

---

## One-Line Revision
- Master pointers deeply: address, dereference, arithmetic, and safety issues.
- Learn dynamic memory APIs and failure patterns (leak, dangling, wild pointer).
- Be clear about struct/union behavior and memory layout basics.
- Understand preprocessor and header usage for modular C projects.
- Explain array vs pointer with sizeof and parameter-decay examples confidently.
