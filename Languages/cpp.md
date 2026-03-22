# C++ Interview Topics (Core + File System + Modules)

Focused interview reference.
Each topic uses the same structure: What, Example, Logic, Input, Output.

## Quick Links
- [High Priority Topics](#high-priority-topics)
- [1 Basics](#1-basics)
- [2 Control Flow](#2-control-flow)
- [3 Functions](#3-functions)
- [4 Pointers](#4-pointers)
- [5 References](#5-references)
- [6 Arrays Strings](#6-arrays-strings)
- [7 Memory Management](#7-memory-management)
- [8 Structures Unions](#8-structures-unions)
- [9 OOP](#9-oop)
- [10 Advanced OOP](#10-advanced-oop)
- [11 STL](#11-stl)
- [12 Algorithms STL](#12-algorithms-stl)
- [13 Templates](#13-templates)
- [14 Exception Handling](#14-exception-handling)
- [15 File Handling Imports](#15-file-handling-imports-important)
- [16 Namespaces](#16-namespaces)
- [17 Modular Code Structure](#17-modular-code-structure)
- [18 Compilation Process](#18-compilation-process)
- [19 File Extensions](#19-file-extensions)
- [Ultra-Short Priority](#ultra-short-priority)

---

## High Priority Topics

Most asked in interviews:
- Pointers
- OOP
- Memory Management
- STL
- File structure: include, header/source split, namespaces

---

## 1 Basics

### Data Types

#### What
Data types define what kind of value a variable can store.

#### Example
```cpp
int age = 22;
double score = 95.5;
char grade = 'A';
bool passed = true;
```

#### Logic
Compiler allocates memory based on type and enforces operations accordingly.

#### Input
Assign integer, floating value, character, and boolean.

#### Output
Variables store type-safe values for later operations.

### Variables

#### What
Variables are named memory locations used to store data.

#### Example
```cpp
int x = 10;
int y = 20;
int sum = x + y;
```

#### Logic
Values are read from x and y, then computed and stored in sum.

#### Input
x = 10, y = 20

#### Output
sum = 30

### Operators

#### What
Operators perform actions like arithmetic, comparison, and logic.

#### Example
```cpp
int a = 8, b = 3;
int add = a + b;
bool greater = a > b;
```

#### Logic
Arithmetic operator returns a numeric result; comparison returns true or false.

#### Input
a = 8, b = 3

#### Output
add = 11, greater = true

---

## 2 Control Flow

### if else

#### What
if else runs code blocks based on condition.

#### Example
```cpp
int marks = 70;
if (marks >= 50) {
    cout << "Pass";
} else {
    cout << "Fail";
}
```

#### Logic
Condition is evaluated first. Only one matching block executes.

#### Input
marks = 70

#### Output
Pass

### switch

#### What
switch selects one case among multiple constant options.

#### Example
```cpp
int day = 2;
switch (day) {
    case 1: cout << "Mon"; break;
    case 2: cout << "Tue"; break;
    default: cout << "Unknown";
}
```

#### Logic
Expression is matched with case labels. break prevents fall-through.

#### Input
day = 2

#### Output
Tue

### for loop

#### What
for loop repeats code with init, condition, and update.

#### Example
```cpp
for (int i = 0; i < 3; i++) {
    cout << i << " ";
}
```

#### Logic
Loop runs while condition is true and increments i each iteration.

#### Input
i from 0 to less than 3

#### Output
0 1 2

### while loop

#### What
while loop repeats as long as condition remains true.

#### Example
```cpp
int i = 0;
while (i < 2) {
    cout << i << " ";
    i++;
}
```

#### Logic
Condition checked before each iteration.

#### Input
i starts at 0

#### Output
0 1

---

## 3 Functions

### Function declaration

#### What
Function declaration tells compiler function name, return type, and parameters.

#### Example
```cpp
int add(int, int);
```

#### Logic
Declaration enables function calls before full definition appears.

#### Input
Function signature only

#### Output
Compiler knows how to type-check add calls.

### Function definition

#### What
Function definition provides actual implementation.

#### Example
```cpp
int add(int a, int b) {
    return a + b;
}
```

#### Logic
When called, parameters receive values and function returns computed result.

#### Input
a = 4, b = 5

#### Output
9

### Pass by value

#### What
Function receives a copy of argument.

#### Example
```cpp
void inc(int x) {
    x++;
}

int a = 5;
inc(a);
cout << a;
```

#### Logic
Only local copy changes, original variable remains unchanged.

#### Input
a = 5

#### Output
5

### Pass by reference

#### What
Function receives alias of original variable.

#### Example
```cpp
void inc(int& x) {
    x++;
}

int a = 5;
inc(a);
cout << a;
```

#### Logic
Reference binds to original variable, so updates affect caller data.

#### Input
a = 5

#### Output
6

### Inline functions

#### What
inline suggests compiler expand function body at call site.

#### Example
```cpp
inline int square(int x) {
    return x * x;
}

cout << square(4);
```

#### Logic
May reduce function-call overhead for tiny functions.

#### Input
x = 4

#### Output
16

---

## 4 Pointers

### Pointer basics

#### What
Pointer stores address of another variable.

#### Example
```cpp
int x = 10;
int* p = &x;
cout << *p;
```

#### Logic
p holds address of x; dereference *p reads value at that address.

#### Input
x = 10

#### Output
10

### Pointer arithmetic

#### What
Pointer can move through contiguous memory.

#### Example
```cpp
int arr[3] = {10, 20, 30};
int* p = arr;
cout << *p << " ";
p++;
cout << *p;
```

#### Logic
Incrementing int pointer moves by sizeof(int).

#### Input
Array {10, 20, 30}

#### Output
10 20

### Pointer to pointer

#### What
Pointer that stores address of another pointer.

#### Example
```cpp
int x = 7;
int* p = &x;
int** pp = &p;
cout << **pp;
```

#### Logic
First dereference gets p, second gets x value.

#### Input
x = 7

#### Output
7

### Null pointer

#### What
Pointer that points to no valid object.

#### Example
```cpp
int* p = nullptr;
if (p == nullptr) {
    cout << "Null";
}
```

#### Logic
Always check before dereferencing.

#### Input
p initialized to nullptr

#### Output
Null

### Dangling pointer

#### What
Pointer referencing memory that is already freed or out of scope.

#### Example
```cpp
int* p = new int(5);
delete p;
// p is now dangling
p = nullptr;
```

#### Logic
After delete, old address is invalid. Reset pointer to nullptr.

#### Input
Allocate then delete

#### Output
Safe state after setting p to nullptr

---

## 5 References

### Reference variables

#### What
Reference is an alias for an existing variable.

#### Example
```cpp
int a = 10;
int& ref = a;
ref = 15;
cout << a;
```

#### Logic
Changing ref changes original variable because both refer to same object.

#### Input
a = 10, ref = alias of a

#### Output
15

### Pointer vs Reference

#### What
Pointer can be reassigned and null; reference must bind once and cannot be null by design.

#### Example
```cpp
int x = 1, y = 2;
int* p = &x;
p = &y;

int& r = x;
r = 5;
cout << x << " " << y << " " << *p;
```

#### Logic
Pointer target changes. Reference remains alias to original object.

#### Input
x = 1, y = 2

#### Output
5 2 2

---

## 6 Arrays Strings

### Arrays

#### What
Fixed-size collection of same type elements.

#### Example
```cpp
int arr[3] = {1, 2, 3};
cout << arr[0] << " " << arr[2];
```

#### Logic
Indexing starts from 0.

#### Input
arr = {1,2,3}

#### Output
1 3

### Character arrays

#### What
C-style string represented as char array ending with null character.

#### Example
```cpp
char name[] = "Aman";
cout << name;
```

#### Logic
Compiler appends '\0' at end for string termination.

#### Input
"Aman"

#### Output
Aman

### std string

#### What
std::string is dynamic and safer string type from STL.

#### Example
```cpp
string s = "Hello";
s += " C++";
cout << s;
```

#### Logic
String manages memory internally and supports rich operations.

#### Input
"Hello"

#### Output
Hello C++

---

## 7 Memory Management

### Stack vs Heap

#### What
Stack stores local automatic variables. Heap stores dynamically allocated objects.

#### Example
```cpp
int a = 10;            // stack
int* p = new int(20);  // heap
cout << a << " " << *p;
delete p;
```

#### Logic
Stack auto-cleans on scope exit. Heap must be manually freed.

#### Input
a = 10, heap int = 20

#### Output
10 20

### new delete

#### What
new allocates memory on heap; delete releases it.

#### Example
```cpp
int* p = new int;
*p = 99;
cout << *p;
delete p;
```

#### Logic
Not deleting causes leak.

#### Input
Allocate one int and assign 99

#### Output
99

### Memory leaks

#### What
Leak happens when heap memory is allocated but never released.

#### Example
```cpp
void bad() {
    int* p = new int(5);
    // missing delete p;
}
```

#### Logic
Pointer lost after function ends; memory remains allocated.

#### Input
Call bad repeatedly

#### Output
Memory usage grows unnecessarily

---

## 8 Structures Unions

### struct

#### What
struct groups related variables under one type.

#### Example
```cpp
struct Student {
    string name;
    int age;
};

Student s{"Riya", 21};
cout << s.name << " " << s.age;
```

#### Logic
Each member has separate memory.

#### Input
name = Riya, age = 21

#### Output
Riya 21

### union

#### What
union stores different members in same memory location.

#### Example
```cpp
union Data {
    int i;
    float f;
};

Data d;
d.i = 10;
cout << d.i;
```

#### Logic
Only one member value is valid at a time.

#### Input
d.i = 10

#### Output
10

---

## 9 OOP

### Class

#### What
Class is blueprint containing data and methods.

#### Example
```cpp
class User {
public:
    string name;
    void show() { cout << name; }
};
```

#### Logic
Class defines structure; object uses it.

#### Input
Define class User

#### Output
Reusable custom type

### Object

#### What
Object is instance of a class.

#### Example
```cpp
User u;
u.name = "Dev";
u.show();
```

#### Logic
Object gets memory for class members.

#### Input
u.name = Dev

#### Output
Dev

### Constructor

#### What
Special function called automatically when object is created.

#### Example
```cpp
class A {
public:
    A() { cout << "Constructed"; }
};

A obj;
```

#### Logic
Initializes object state.

#### Input
Create A obj

#### Output
Constructed

### Destructor

#### What
Special function called automatically when object is destroyed.

#### Example
```cpp
class A {
public:
    ~A() { cout << "Destroyed"; }
};

{
    A obj;
}
```

#### Logic
Used for cleanup (files, memory, locks).

#### Input
Object goes out of scope

#### Output
Destroyed

### Copy constructor

#### What
Constructor that initializes object from another object of same class.

#### Example
```cpp
class Box {
public:
    int w;
    Box(int x) : w(x) {}
    Box(const Box& b) : w(b.w) {}
};

Box b1(10);
Box b2 = b1;
cout << b2.w;
```

#### Logic
Defines how copy should happen, especially for owned resources.

#### Input
b1.w = 10

#### Output
10

### Encapsulation

#### What
Bundling data and methods with controlled access.

#### Example
```cpp
class Account {
private:
    int balance;
public:
    Account() : balance(0) {}
    void deposit(int x) { balance += x; }
    int getBalance() const { return balance; }
};

Account a;
a.deposit(100);
cout << a.getBalance();
```

#### Logic
Private members protect internal state.

#### Input
Deposit 100

#### Output
100

### Abstraction

#### What
Show essential interface, hide internal implementation.

#### Example
```cpp
class Car {
public:
    void start() { engineOn(); }
private:
    void engineOn() { cout << "Engine started"; }
};

Car c;
c.start();
```

#### Logic
User calls public method without needing low-level details.

#### Input
Call c.start()

#### Output
Engine started

### Inheritance

#### What
Derived class acquires properties and behavior of base class.

#### Example
```cpp
class Animal {
public:
    void eat() { cout << "eat"; }
};

class Dog : public Animal {};

Dog d;
d.eat();
```

#### Logic
Code reuse through parent-child relation.

#### Input
Dog object

#### Output
eat

### Polymorphism

#### What
Same interface, different behavior.

#### Example
```cpp
class Base {
public:
    virtual void show() { cout << "Base"; }
};

class Derived : public Base {
public:
    void show() override { cout << "Derived"; }
};

Base* b = new Derived();
b->show();
delete b;
```

#### Logic
Virtual dispatch picks Derived implementation at runtime.

#### Input
Base pointer to Derived object

#### Output
Derived

---

## 10 Advanced OOP

### Operator Overloading

#### What
Define custom behavior for operators on user-defined types.

#### Example
```cpp
class Point {
public:
    int x;
    Point(int x) : x(x) {}
    Point operator+(const Point& other) {
        return Point(x + other.x);
    }
};

Point a(2), b(3);
Point c = a + b;
cout << c.x;
```

#### Logic
operator+ function is called for objects.

#### Input
2 and 3

#### Output
5

### Function Overloading

#### What
Multiple functions with same name but different parameter lists.

#### Example
```cpp
int add(int a, int b) { return a + b; }
double add(double a, double b) { return a + b; }

cout << add(2, 3) << " " << add(2.5, 3.5);
```

#### Logic
Compiler selects best-matching signature at compile time.

#### Input
int and double arguments

#### Output
5 6

### Function Overriding

#### What
Derived class redefines base class virtual method.

#### Example
```cpp
class A {
public:
    virtual void show() { cout << "A"; }
};

class B : public A {
public:
    void show() override { cout << "B"; }
};
```

#### Logic
Runtime call through base pointer uses derived override.

#### Input
Call show via base reference to B

#### Output
B

### Virtual functions

#### What
Enable runtime polymorphism.

#### Example
```cpp
A* ptr = new B();
ptr->show();
delete ptr;
```

#### Logic
Compiler emits virtual dispatch mechanism.

#### Input
Base pointer to derived object

#### Output
B

### vtable

#### What
Internal table of virtual function addresses used for dynamic dispatch.

#### Example
```cpp
// Conceptual behavior: ptr->show() resolves through vtable.
```

#### Logic
Object with virtual methods stores hidden vptr; call resolves at runtime.

#### Input
Virtual call on base pointer

#### Output
Correct overridden function executes

---

## 11 STL

### Vector

#### What
Dynamic array container.

#### Example
```cpp
vector<int> v = {1, 2, 3};
v.push_back(4);
cout << v.size();
```

#### Logic
Automatically resizes when capacity is exceeded.

#### Input
Push 4 into vector

#### Output
4

### Pair

#### What
Stores two heterogeneous values.

#### Example
```cpp
pair<string, int> p = {"A", 10};
cout << p.first << " " << p.second;
```

#### Logic
Useful for key-value and returning two values.

#### Input
"A", 10

#### Output
A 10

### Map

#### What
Sorted key-value associative container.

#### Example
```cpp
map<string, int> m;
m["apple"] = 3;
cout << m["apple"];
```

#### Logic
Keys are unique and ordered.

#### Input
Insert apple -> 3

#### Output
3

### Set

#### What
Stores unique sorted values.

#### Example
```cpp
set<int> s = {3, 1, 3, 2};
cout << s.size();
```

#### Logic
Duplicates are removed automatically.

#### Input
3,1,3,2

#### Output
3

### Stack

#### What
LIFO container adapter.

#### Example
```cpp
stack<int> st;
st.push(10);
st.push(20);
cout << st.top();
```

#### Logic
Last pushed element is accessed first.

#### Input
Push 10 then 20

#### Output
20

### Queue

#### What
FIFO container adapter.

#### Example
```cpp
queue<int> q;
q.push(10);
q.push(20);
cout << q.front();
```

#### Logic
First pushed element is removed/accessed first.

#### Input
Push 10 then 20

#### Output
10

### Priority Queue

#### What
Heap-based container where top is highest priority element.

#### Example
```cpp
priority_queue<int> pq;
pq.push(5);
pq.push(1);
pq.push(10);
cout << pq.top();
```

#### Logic
By default, max-heap behavior.

#### Input
5,1,10

#### Output
10

---

## 12 Algorithms STL

### sort

#### What
Sorts range in ascending order by default.

#### Example
```cpp
vector<int> v = {4, 1, 3, 2};
sort(v.begin(), v.end());
```

#### Logic
Reorders elements based on comparator.

#### Input
4,1,3,2

#### Output
1,2,3,4

### binary_search

#### What
Checks if value exists in sorted range.

#### Example
```cpp
vector<int> v = {1, 2, 3, 4, 5};
cout << binary_search(v.begin(), v.end(), 3);
```

#### Logic
Uses divide-and-conquer on sorted data.

#### Input
Search 3 in sorted vector

#### Output
1

### lower_bound

#### What
Returns iterator to first element not less than target.

#### Example
```cpp
vector<int> v = {1, 2, 2, 4};
auto it = lower_bound(v.begin(), v.end(), 2);
cout << (it - v.begin());
```

#### Logic
Finds first valid insertion position preserving order.

#### Input
Target 2

#### Output
1

### upper_bound

#### What
Returns iterator to first element greater than target.

#### Example
```cpp
vector<int> v = {1, 2, 2, 4};
auto it = upper_bound(v.begin(), v.end(), 2);
cout << (it - v.begin());
```

#### Logic
Skips all equal elements and returns next position.

#### Input
Target 2

#### Output
3

---

## 13 Templates

### Function templates

#### What
Generic function that works with multiple data types.

#### Example
```cpp
template <typename T>
T add(T a, T b) {
    return a + b;
}

cout << add<int>(2, 3);
```

#### Logic
Compiler generates concrete function for used type.

#### Input
2 and 3 as int

#### Output
5

### Class templates

#### What
Generic class parameterized by type.

#### Example
```cpp
template <typename T>
class Box {
public:
    T value;
    Box(T v) : value(v) {}
};

Box<int> b(10);
cout << b.value;
```

#### Logic
Compiler creates class version for T = int.

#### Input
T = int, value = 10

#### Output
10

---

## 14 Exception Handling

### try catch throw

#### What
Mechanism to handle runtime errors safely.

#### Example
```cpp
try {
    int a = 10, b = 0;
    if (b == 0) throw runtime_error("divide by zero");
    cout << a / b;
} catch (const exception& e) {
    cout << e.what();
}
```

#### Logic
throw transfers control to matching catch block.

#### Input
b = 0

#### Output
divide by zero

---

## 15 File Handling Imports IMPORTANT

### include

#### What
#include inserts header content before compilation.

#### Example
```cpp
#include <iostream>
#include "math_utils.h"
```

#### Logic
Preprocessor copies referenced file text into translation unit.

#### Input
System and local headers

#### Output
Compiler sees declarations from both headers

### Header files h

#### What
Header files contain declarations and interfaces.

#### Example
```cpp
// math_utils.h
int add(int a, int b);
```

#### Logic
Expose public API to multiple source files.

#### Input
Function declarations in header

#### Output
Any source including header can call declared functions

### Implementation files cpp

#### What
Source files contain function/method definitions.

#### Example
```cpp
// math_utils.cpp
#include "math_utils.h"

int add(int a, int b) {
    return a + b;
}
```

#### Logic
Actual executable code is compiled from source files.

#### Input
Definition of add

#### Output
Linker resolves add symbol for callers

### Include guards

#### What
Prevents multiple inclusion of same header.

#### Example
```cpp
#ifndef MATH_UTILS_H
#define MATH_UTILS_H

int add(int a, int b);

#endif
```

#### Logic
Header body is included only once per translation unit.

#### Input
Repeated include of same header

#### Output
No redefinition errors

### pragma once

#### What
Non-macro directive to include header once.

#### Example
```cpp
#pragma once

int add(int a, int b);
```

#### Logic
Compiler skips repeated includes of file.

#### Input
Header included multiple times

#### Output
Single effective inclusion

---

## 16 Namespaces

### namespace keyword

#### What
namespace groups identifiers to avoid naming conflicts.

#### Example
```cpp
namespace Math {
    int add(int a, int b) { return a + b; }
}

cout << Math::add(2, 3);
```

#### Logic
Qualified name selects symbol from specific namespace.

#### Input
2 and 3

#### Output
5

### using namespace std

#### What
Brings names from std namespace into current scope.

#### Example
```cpp
using namespace std;
cout << "Hello";
```

#### Logic
Removes need to write std:: repeatedly, but can cause name collisions.

#### Input
Print string

#### Output
Hello

### Custom namespaces

#### What
User-defined namespaces organize project code.

#### Example
```cpp
namespace App {
    void run() { cout << "Run"; }
}

App::run();
```

#### Logic
Improves modularity and avoids global-name pollution.

#### Input
Call App::run()

#### Output
Run

---

## 17 Modular Code Structure

### Header declaration

#### What
Declare interfaces in header files.

#### Example
```cpp
// user_service.h
class UserService {
public:
    void createUser(const string& name);
};
```

#### Logic
Other files can depend on declarations without seeing implementation details.

#### Input
Include header in multiple source files

#### Output
Shared interface access

### Source file implementation

#### What
Implement declared behavior in .cpp files.

#### Example
```cpp
// user_service.cpp
#include "user_service.h"

void UserService::createUser(const string& name) {
    cout << "Created: " << name;
}
```

#### Logic
Keeps implementation centralized and maintainable.

#### Input
Call createUser("Ava")

#### Output
Created: Ava

### Separation of concerns

#### What
Split code by responsibility (API, business logic, utilities).

#### Example
```text
include/
  math_utils.h
src/
  math_utils.cpp
  main.cpp
```

#### Logic
Cleaner architecture improves testing, readability, and scaling.

#### Input
Project with multiple responsibilities

#### Output
Modular and easier-to-maintain codebase

---

## 18 Compilation Process

### Preprocessing

#### What
Handles directives like include, define, conditional compilation.

#### Example
```cpp
#define PI 3.14
#include <iostream>
```

#### Logic
Preprocessor transforms source before actual compilation.

#### Input
Source with preprocessor directives

#### Output
Expanded intermediate source

### Compilation

#### What
Translates preprocessed source to object code.

#### Example
```text
g++ -c main.cpp
```

#### Logic
Each source file becomes separate object file.

#### Input
main.cpp

#### Output
main.o

### Linking

#### What
Combines object files and libraries into executable.

#### Example
```text
g++ main.o math_utils.o -o app
```

#### Logic
Resolves function and variable references across files.

#### Input
Object files and libraries

#### Output
Executable binary app

---

## 19 File Extensions

### cpp

#### What
Common extension for C++ source implementation files.

#### Example
```text
main.cpp
```

#### Logic
Compiled into object files.

#### Input
Source code in main.cpp

#### Output
Object code after compilation

### h

#### What
Traditional C/C++ header extension for declarations.

#### Example
```text
math_utils.h
```

#### Logic
Included by source files for shared interfaces.

#### Input
Declarations in header

#### Output
Compilation units can use declared symbols

### hpp

#### What
Alternative C++ header extension, often used for C++-specific headers/templates.

#### Example
```text
vector_utils.hpp
```

#### Logic
Convention choice; behavior same as other header files.

#### Input
Header contents in .hpp

#### Output
Included interfaces available to source files

---

## Ultra-Short Priority

Pointers
OOP
Memory
STL
File structure (#include, namespace)

---

## One-Line Revision
- Be very strong in pointers, references, and dynamic memory.
- Master class design, constructors, destructors, copy behavior, and virtual polymorphism.
- Practice STL containers and algorithms daily.
- Understand file split: header declarations + source implementations + include safety.
- Explain preprocessing, compilation, and linking clearly in interviews.
