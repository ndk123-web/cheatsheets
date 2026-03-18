# JavaScript Cheatsheet (A to Z Quick Reference)

## What is JavaScript?
JavaScript is a high-level, dynamic language used to create interactive web apps and server-side services.
It runs in browsers and in runtimes like Node.js.
You use it for UI behavior, API communication, backend services, tooling, and automation.

---

## Table of Contents
- [Introduction](#introduction)
- [Variables](#variables)
- [Data Types](#data-types)
- [Type Casting](#type-casting)
- [Data Structures](#data-structures)
- [Equality Comparisons](#equality-comparisons)
- [Loops and Iterations](#loops-and-iterations)
- [Control Flow](#control-flow)
- [Expressions and Operators](#expressions-and-operators)
- [Functions](#functions)
- [Strict Mode](#strict-mode)
- [this Keyword](#this-keyword)
- [Function Borrowing and Binding](#function-borrowing-and-binding)
- [Asynchronous JavaScript](#asynchronous-javascript)
- [Working with APIs](#working-with-apis)
- [Modules](#modules)
- [Iterators and Generators](#iterators-and-generators)
- [Classes](#classes)
- [Memory Management](#memory-management)
- [DOM and Browser](#dom-and-browser)
- [Debugging](#debugging)
- [Related Ecosystem](#related-ecosystem)

---

## Introduction

### What
- JavaScript is the programming language of the web.
- It evolved from basic browser scripting to full-stack development language.

### Why
- Needed to make pages dynamic and interactive.
- Standardized versions (ECMAScript) keep language modern and consistent.

### How
- Run in browser console or script tag.
- Run in Node.js using terminal command `node file.js`.

### Logic/Concept
- Browser engine parses and executes JavaScript.
- JavaScript is single-threaded with asynchronous event loop behavior.

### Example
```js
console.log("JavaScript is running");
```

### Behavior from Example
- The line prints text to console immediately.
- Confirms runtime and script execution are working.

---

## Variables

### Variable Declarations (`var`, `let`, `const`)

#### What
- Variables store values in memory.
- JavaScript provides three declaration keywords.

#### Why
- Different declaration types support different mutability and scope needs.

#### How
- `var` is function-scoped.
- `let` and `const` are block-scoped.
- `const` cannot be reassigned.

#### Logic/Concept
- Scope determines where variable is visible.
- Mutability determines whether variable binding changes.

#### Example
```js
var a = 10;
let b = 20;
const c = 30;

b = 25;
// c = 35; // Error: Assignment to constant variable
```

#### Behavior from Example
- `a` and `b` store numbers.
- `b` changes successfully.
- Reassigning `c` throws runtime error.

### Hoisting

#### What
- Hoisting is JavaScript moving declarations to top of scope during compilation.

#### Why
- Explains why some variables/functions are accessible before declaration lines.

#### How
- `var` is hoisted and initialized as `undefined`.
- `let`/`const` are hoisted but in Temporal Dead Zone until declaration line.

#### Logic/Concept
- Initialization timing differs by keyword.

#### Example
```js
console.log(x); // undefined
var x = 5;

// console.log(y); // ReferenceError
let y = 10;
```

#### Behavior from Example
- `x` can be read before assignment but gives `undefined`.
- `y` access before declaration throws ReferenceError.

### Variable Naming Rules

#### What
- Rules define valid identifier names.

#### Why
- Prevent syntax errors and improve readability.

#### How
- Start with letter, underscore `_`, or dollar `$`.
- Cannot start with number.
- Cannot use reserved keywords.

#### Logic/Concept
- Parser validates names during lexical analysis.

#### Example
```js
let userName = "A";
let _count = 0;
let $price = 99;
// let 2name = "B"; // Invalid
```

#### Behavior from Example
- Valid identifiers compile and run.
- Invalid identifier causes syntax error.

### Variable Scopes (Global, Function, Block)

#### What
- Scope defines visibility lifetime of variables.

#### Why
- Scope control prevents accidental overwrite and naming conflicts.

#### How
- Global: available across file/runtime.
- Function: inside function only.
- Block: inside `{}` only for `let`/`const`.

#### Logic/Concept
- JavaScript uses lexical scoping (scope decided by code structure, not call location).

#### Example
```js
let globalVar = "global";

function demo() {
  var functionVar = "function";
  if (true) {
    let blockVar = "block";
    console.log(globalVar, functionVar, blockVar);
  }
  // console.log(blockVar); // Error
}

demo();
```

#### Behavior from Example
- `globalVar` and `functionVar` accessible inside block.
- `blockVar` only inside `if` block.

### All About Variables (Quick Rules)

#### What
- Practical best-practice summary for variables.

#### Why
- Reduces bugs and improves maintainability.

#### How
- Prefer `const` by default.
- Use `let` only when reassignment is needed.
- Avoid `var` in modern code.

#### Logic/Concept
- Immutable bindings make code predictable.

#### Example
```js
const apiUrl = "https://example.com";
let retryCount = 0;
retryCount += 1;
```

#### Behavior from Example
- `apiUrl` remains stable.
- `retryCount` tracks changing state safely.

---

## Data Types

### Primitive Types

#### What
- Immutable value types: `string`, `number`, `bigint`, `boolean`, `undefined`, `null`, `symbol`.

#### Why
- Primitive behavior affects copying, comparison, and memory usage.

#### How
- Stored by value, not by reference.

#### Logic/Concept
- Assigning primitive creates independent copy.

#### Example
```js
let a = 10;
let b = a;
b = 20;
console.log(a, b);
```

#### Behavior from Example
- Output is `10 20`.
- Changing `b` does not affect `a`.

### `string`

#### What
- Text data type.

#### Why
- Used for names, messages, URLs, and display content.

#### How
- Create with single quote, double quote, or template literals.

#### Logic/Concept
- Strings are immutable.

#### Example
```js
const name = "Riya";
const msg = `Hello ${name}`;
console.log(msg);
```

#### Behavior from Example
- Template literal interpolates variable and prints `Hello Riya`.

### `number`

#### What
- Numeric type for integers and floating points.

#### Why
- Used for arithmetic, counters, measurements.

#### How
- Supports operators `+ - * / % **`.

#### Logic/Concept
- Stored as IEEE 754 double-precision floating-point.

#### Example
```js
const total = 0.1 + 0.2;
console.log(total);
```

#### Behavior from Example
- Output may be `0.30000000000000004` due to floating-point precision.

### `bigint`

#### What
- Numeric type for very large integers.

#### Why
- Number loses precision above `2^53 - 1`.

#### How
- Add `n` suffix or use `BigInt()`.

#### Logic/Concept
- BigInt operations must use BigInt operands only.

#### Example
```js
const big = 9007199254740993n;
console.log(big + 2n);
```

#### Behavior from Example
- Large integer arithmetic is exact.

### `boolean`

#### What
- Logical true/false type.

#### Why
- Used in conditions and control flow.

#### How
- Result of comparisons and logical expressions.

#### Logic/Concept
- Supports short-circuit evaluation in logical operators.

#### Example
```js
const isAdmin = true;
if (isAdmin) console.log("Allowed");
```

#### Behavior from Example
- Condition passes and prints `Allowed`.

### `undefined`

#### What
- Value for declared but uninitialized variables.

#### Why
- Represents "not assigned yet" state.

#### How
- Returned by functions with no `return` value.

#### Logic/Concept
- Distinct from `null` (intentional emptiness).

#### Example
```js
let x;
console.log(x);
```

#### Behavior from Example
- Prints `undefined`.

### `null`

#### What
- Intentional empty or missing object value.

#### Why
- Expresses explicit "no value".

#### How
- Assign directly when resetting references.

#### Logic/Concept
- Historical quirk: `typeof null === "object"`.

#### Example
```js
let selectedUser = null;
console.log(selectedUser);
```

#### Behavior from Example
- Variable intentionally holds empty state.

### `symbol`

#### What
- Unique and immutable identifier primitive.

#### Why
- Avoid property name collisions in objects.

#### How
- Use `Symbol("description")`.

#### Logic/Concept
- Every Symbol call creates unique value.

#### Example
```js
const id1 = Symbol("id");
const id2 = Symbol("id");
console.log(id1 === id2);
```

#### Behavior from Example
- Prints `false` because symbols are unique.

### Object

#### What
- Reference type storing key-value pairs.

#### Why
- Models structured, grouped data.

#### How
- Create with `{}` or constructors.

#### Logic/Concept
- Assigned and passed by reference.

#### Example
```js
const user = { name: "Dev" };
const ref = user;
ref.name = "Sam";
console.log(user.name);
```

#### Behavior from Example
- Prints `Sam` because both variables reference same object.

### Object Prototype

#### What
- Hidden link from object to parent object for property lookup.

#### Why
- Enables method sharing and inheritance.

#### How
- Access via `Object.getPrototypeOf(obj)`.

#### Logic/Concept
- If property missing on object, JS searches prototype chain.

#### Example
```js
const animal = { eat() { return "eating"; } };
const dog = Object.create(animal);
console.log(dog.eat());
```

#### Behavior from Example
- `dog` does not own `eat`, but finds it in prototype.

### Prototypal Inheritance

#### What
- Objects inherit directly from other objects.

#### Why
- Reuse behavior without class-only model.

#### How
- Link objects with `Object.create(parent)`.

#### Logic/Concept
- Prototype chain resolves methods at runtime.

#### Example
```js
const parent = { role: "parent" };
const child = Object.create(parent);
console.log(child.role);
```

#### Behavior from Example
- Prints `parent` through inherited property lookup.

### `typeof` Operator

#### What
- Returns string describing operand type.

#### Why
- Quick runtime type checks.

#### How
- Use `typeof value`.

#### Logic/Concept
- Some edge cases exist (`null`, arrays as object).

#### Example
```js
console.log(typeof "x");
console.log(typeof 1);
console.log(typeof null);
```

#### Behavior from Example
- Outputs `string`, `number`, `object` (null quirk).

### Built-in Objects

#### What
- Standard global utilities: `Math`, `Date`, `Array`, `JSON`, `Promise`, etc.

#### Why
- Solve common tasks without external libraries.

#### How
- Use methods from these objects directly.

#### Logic/Concept
- Built-ins are optimized and standardized.

#### Example
```js
console.log(Math.max(3, 9, 2));
console.log(new Date().getFullYear());
```

#### Behavior from Example
- Returns maximum number and current year.

---

## Type Casting

### Type Conversion vs Coercion

#### What
- Conversion: explicit type change.
- Coercion: automatic type change by JavaScript.

#### Why
- Avoid unexpected outputs in math and comparisons.

#### How
- Use explicit conversion for clarity.
- Understand coercion rules for `+`, `==`, and template literals.

#### Logic/Concept
- Engine attempts compatible type before operation.

#### Example
```js
console.log("5" + 1);
console.log("5" - 1);
```

#### Behavior from Example
- First output `51` (string concatenation).
- Second output `4` (numeric coercion).

### Explicit Type Casting

#### What
- Manual conversion using constructors/functions.

#### Why
- Predictable and readable behavior.

#### How
- `Number()`, `String()`, `Boolean()`, `BigInt()`.

#### Logic/Concept
- You control final type before operation.

#### Example
```js
const n = Number("123");
const s = String(123);
const b = Boolean(1);
console.log(n, s, b);
```

#### Behavior from Example
- Outputs `123`, `"123"`, `true` with expected types.

### Implicit Type Casting

#### What
- JavaScript converts operands automatically.

#### Why
- Convenient but risky when misunderstood.

#### How
- Happens in arithmetic and loose equality.

#### Logic/Concept
- Conversion direction depends on operator.

#### Example
```js
console.log(0 == false);
console.log(null == undefined);
```

#### Behavior from Example
- Both print `true` due to loose equality coercion rules.

---

## Data Structures

### Keyed Collections (`Map`, `WeakMap`, `Set`, `WeakSet`)

#### What
- Specialized collections for keys and unique values.

#### Why
- Better semantics and performance for many use-cases than plain objects/arrays.

#### How
- `Map`: key-value with any key type.
- `Set`: unique values.
- `WeakMap`/`WeakSet`: weak references for objects only.

#### Logic/Concept
- Weak collections do not prevent garbage collection.

#### Example
```js
const m = new Map();
m.set("name", "Lee");

const s = new Set([1, 1, 2]);
console.log(m.get("name"), s.size);
```

#### Behavior from Example
- `Map` retrieves value by key.
- `Set` removes duplicates, size becomes `2`.

### Indexed Collections (`Arrays`, Typed Arrays)

#### What
- Ordered collections by index.

#### Why
- Efficient sequential data processing.

#### How
- Arrays for generic items.
- Typed arrays (`Uint8Array`, etc.) for binary numeric data.

#### Logic/Concept
- Typed arrays have fixed numeric type and contiguous memory layout.

#### Example
```js
const arr = [10, 20, 30];
const bytes = new Uint8Array([65, 66, 67]);
console.log(arr[1], bytes[0]);
```

#### Behavior from Example
- `arr[1]` gives `20`.
- `bytes[0]` gives `65`.

### Structured Data (`JSON`)

#### What
- Text format for structured key-value data exchange.

#### Why
- Standard API data format.

#### How
- `JSON.stringify(object)` to serialize.
- `JSON.parse(string)` to deserialize.

#### Logic/Concept
- Converts runtime objects to transport-safe text and back.

#### Example
```js
const user = { id: 1, name: "Ana" };
const text = JSON.stringify(user);
const obj = JSON.parse(text);
console.log(text, obj.name);
```

#### Behavior from Example
- Object becomes JSON string and reconstructs correctly.

---

## Equality Comparisons

### `==` (Loose Equality)

#### What
- Compares values after type coercion.

#### Why
- Legacy convenience but often causes confusion.

#### How
- Avoid unless coercion behavior is fully intended.

#### Logic/Concept
- Follows abstract equality algorithm.

#### Example
```js
console.log(5 == "5");
```

#### Behavior from Example
- Prints `true` because string coerces to number.

### `===` (Strict Equality)

#### What
- Compares type and value without coercion.

#### Why
- Safer and predictable in production code.

#### How
- Use as default comparison operator.

#### Logic/Concept
- Follows strict equality algorithm.

#### Example
```js
console.log(5 === "5");
```

#### Behavior from Example
- Prints `false` because types differ.

### `Object.is`

#### What
- Compares values with SameValue semantics.

#### Why
- Handles `NaN` and signed zero edge cases.

#### How
- Call `Object.is(a, b)`.

#### Logic/Concept
- `NaN` equals itself; `+0` and `-0` are different.

#### Example
```js
console.log(Object.is(NaN, NaN));
console.log(Object.is(+0, -0));
```

#### Behavior from Example
- Prints `true` then `false`.

### Value Comparison Operators

#### What
- `<`, `>`, `<=`, `>=` compare order.

#### Why
- Needed for sorting, filtering, ranges.

#### How
- Works on numbers and lexicographic strings.

#### Logic/Concept
- String comparison is Unicode code-point based.

#### Example
```js
console.log(10 > 2);
console.log("b" > "a");
```

#### Behavior from Example
- Both expressions evaluate to `true`.

### Equality Algorithms (Concept Names)

#### What
- Internal concepts: `isLooselyEqual`, `isStrictlyEqual`, `SameValue`, `SameValueZero`.

#### Why
- Explains behavior differences across APIs.

#### How
- `==` uses loose algorithm.
- `===` uses strict.
- `Object.is` uses SameValue.
- `Array.prototype.includes` uses SameValueZero.

#### Logic/Concept
- `SameValueZero` treats `NaN` as equal and `+0/-0` as equal.

#### Example
```js
console.log([NaN].includes(NaN));
console.log([+0].includes(-0));
```

#### Behavior from Example
- Both print `true` due to SameValueZero.

---

## Loops and Iterations

### `for`

#### What
- Count-controlled loop.

#### Why
- Best when iteration count/index is known.

#### How
- `for (init; condition; update) {}`.

#### Logic/Concept
- Runs: init once, then condition -> body -> update.

#### Example
```js
for (let i = 0; i < 3; i++) {
  console.log(i);
}
```

#### Behavior from Example
- Prints `0`, `1`, `2`.

### `while`

#### What
- Condition-controlled loop.

#### Why
- Use when number of iterations is not known in advance.

#### How
- Runs while condition is true.

#### Logic/Concept
- Condition checked before each iteration.

#### Example
```js
let i = 0;
while (i < 2) {
  console.log(i);
  i++;
}
```

#### Behavior from Example
- Prints `0`, `1` then stops.

### `do...while`

#### What
- Loop that executes body at least once.

#### Why
- Useful when first run is mandatory.

#### How
- Body runs before condition check.

#### Logic/Concept
- Post-condition loop.

#### Example
```js
let i = 5;
do {
  console.log(i);
  i++;
} while (i < 5);
```

#### Behavior from Example
- Prints `5` once despite false condition.

### `for...in`

#### What
- Iterates enumerable object keys.

#### Why
- Useful for object property traversal.

#### How
- `for (const key in obj)`.

#### Logic/Concept
- Returns keys, not values.

#### Example
```js
const obj = { a: 1, b: 2 };
for (const k in obj) console.log(k, obj[k]);
```

#### Behavior from Example
- Prints `a 1` and `b 2`.

### `for...of`

#### What
- Iterates iterable values.

#### Why
- Clean loop for arrays, strings, maps, sets.

#### How
- `for (const value of iterable)`.

#### Logic/Concept
- Uses iterable protocol and iterator.

#### Example
```js
for (const n of [10, 20, 30]) {
  console.log(n);
}
```

#### Behavior from Example
- Prints each array value in order.

### `break` and `continue`

#### What
- `break` exits loop.
- `continue` skips current iteration.

#### Why
- Improves control for conditional loop behavior.

#### How
- Place inside loop with condition checks.

#### Logic/Concept
- Control statements alter normal iteration flow.

#### Example
```js
for (let i = 0; i < 5; i++) {
  if (i === 2) continue;
  if (i === 4) break;
  console.log(i);
}
```

#### Behavior from Example
- Prints `0`, `1`, `3`.
- Skips `2`, stops before printing `4`.

---

## Control Flow

### `if...else`

#### What
- Conditional branching based on boolean condition.

#### Why
- Executes different logic for different states.

#### How
- `if (condition) {}` else alternate block.

#### Logic/Concept
- First true condition block executes.

#### Example
```js
const score = 75;
if (score >= 50) {
  console.log("Pass");
} else {
  console.log("Fail");
}
```

#### Behavior from Example
- Prints `Pass`.

### `switch`

#### What
- Multi-branch comparison against one expression.

#### Why
- Cleaner than many `else if` for fixed values.

#### How
- Use `case` blocks with `break`.

#### Logic/Concept
- Matches by strict equality.

#### Example
```js
const day = 2;
switch (day) {
  case 1: console.log("Mon"); break;
  case 2: console.log("Tue"); break;
  default: console.log("Unknown");
}
```

#### Behavior from Example
- Prints `Tue`.

### Exceptional Handling (`throw`, `try/catch/finally`, Error Objects)

#### What
- Mechanism to handle runtime failures safely.

#### Why
- Prevent crashes and provide meaningful error handling.

#### How
- `throw new Error(message)`.
- Catch with `catch (err)`.
- Always-run cleanup in `finally`.

#### Logic/Concept
- Errors propagate up call stack until handled.

#### Example
```js
function divide(a, b) {
  if (b === 0) throw new Error("Divide by zero");
  return a / b;
}

try {
  console.log(divide(10, 0));
} catch (err) {
  console.log("Handled:", err.message);
} finally {
  console.log("Done");
}
```

#### Behavior from Example
- Throws Error for invalid input.
- Catch block handles and prints message.
- `finally` prints `Done` always.

---

## Expressions and Operators

### Assignment Operators

#### What
- Assign/update variable values.

#### Why
- Needed for state updates.

#### How
- `=`, `+=`, `-=`, `*=`, `/=`, `%=`.

#### Logic/Concept
- Right side evaluated first, then assigned.

#### Example
```js
let x = 10;
x += 5;
console.log(x);
```

#### Behavior from Example
- `x` becomes `15`.

### Comparison Operators

#### What
- Compare values and return boolean.

#### Why
- Required for decision logic.

#### How
- `>`, `<`, `>=`, `<=`, `==`, `===`, `!=`, `!==`.

#### Logic/Concept
- Output is always boolean.

#### Example
```js
console.log(10 >= 5, 10 === "10");
```

#### Behavior from Example
- Prints `true false`.

### Arithmetic Operators

#### What
- Perform numeric calculations.

#### Why
- Core math in applications.

#### How
- `+`, `-`, `*`, `/`, `%`, `**`, `++`, `--`.

#### Logic/Concept
- Operator precedence affects result.

#### Example
```js
console.log(2 + 3 * 4);
```

#### Behavior from Example
- Prints `14` because multiplication runs first.

### Bitwise Operators

#### What
- Operate on 32-bit binary integers.

#### Why
- Useful in low-level flags and optimization scenarios.

#### How
- `&`, `|`, `^`, `~`, `<<`, `>>`, `>>>`.

#### Logic/Concept
- Converts numbers to binary form for operation.

#### Example
```js
console.log(5 & 1);
```

#### Behavior from Example
- Prints `1` because `0101 & 0001 = 0001`.

### Logical Operators

#### What
- Boolean logic and short-circuit evaluation.

#### Why
- Control flow and default value patterns.

#### How
- `&&`, `||`, `!`, `??`.

#### Logic/Concept
- `&&` returns first falsy or last truthy.
- `||` returns first truthy.

#### Example
```js
console.log(true && "ok");
console.log(0 || 100);
console.log(null ?? "fallback");
```

#### Behavior from Example
- Outputs `ok`, `100`, `fallback`.

### BigInt Operators

#### What
- Arithmetic/comparison for `bigint` values.

#### Why
- Safe large integer computation.

#### How
- Use BigInt with BigInt only.

#### Logic/Concept
- Mixing Number and BigInt directly throws error.

#### Example
```js
const a = 10n;
const b = 3n;
console.log(a / b);
```

#### Behavior from Example
- Prints `3n` (integer division for BigInt).

### String Operators

#### What
- Operations for combining and checking text.

#### Why
- Build messages and labels.

#### How
- `+` for concatenation.

#### Logic/Concept
- If one operand is string, `+` coerces other to string.

#### Example
```js
console.log("Hello " + "World");
```

#### Behavior from Example
- Prints `Hello World`.

### Conditional Operator (Ternary)

#### What
- Short inline conditional expression.

#### Why
- Compact alternative to simple `if...else` assignment.

#### How
- `condition ? valueIfTrue : valueIfFalse`.

#### Logic/Concept
- Returns one of two expressions.

#### Example
```js
const age = 18;
const type = age >= 18 ? "adult" : "minor";
console.log(type);
```

#### Behavior from Example
- Prints `adult`.

### Comma Operator

#### What
- Evaluates multiple expressions and returns last one.

#### Why
- Rarely used in compact loops/expressions.

#### How
- `(expr1, expr2, expr3)`.

#### Logic/Concept
- Earlier expressions run for side effects only.

#### Example
```js
let a = 1;
const result = (a += 2, a *= 3, a);
console.log(result);
```

#### Behavior from Example
- `a` changes through each step; final output is `9`.

### Unary Operators

#### What
- Single-operand operators.

#### Why
- Type conversion, sign change, boolean inversion, type checks.

#### How
- `typeof`, `+`, `-`, `!`, `delete`, `void`.

#### Logic/Concept
- Unary `+` converts to number.

#### Example
```js
console.log(typeof "x", +"42", !0);
```

#### Behavior from Example
- Prints `string 42 true`.

---

## Functions

### Function Parameters

#### What
- Inputs passed to function.

#### Why
- Make functions reusable with variable data.

#### How
- Define in function signature.

#### Logic/Concept
- Arguments map to parameters by position.

#### Example
```js
function greet(name) {
  return `Hello ${name}`;
}
console.log(greet("Dev"));
```

#### Behavior from Example
- Returns greeting using supplied argument.

### Arrow Functions

#### What
- Short function syntax using `=>`.

#### Why
- Cleaner syntax, lexical `this` behavior.

#### How
- `const fn = (a, b) => a + b`.

#### Logic/Concept
- No own `this`, `arguments`, or `prototype`.

#### Example
```js
const add = (a, b) => a + b;
console.log(add(2, 3));
```

#### Behavior from Example
- Prints `5`.

### IIFEs

#### What
- Immediately Invoked Function Expressions.

#### Why
- Execute once and create isolated scope.

#### How
- Wrap function in parentheses and call immediately.

#### Logic/Concept
- Useful to avoid polluting global scope.

#### Example
```js
(function () {
  console.log("IIFE executed");
})();
```

#### Behavior from Example
- Prints once immediately.

### `arguments` Object

#### What
- Array-like object containing passed arguments in normal functions.

#### Why
- Useful in legacy functions with unknown parameter count.

#### How
- Access via `arguments[index]`.

#### Logic/Concept
- Not available in arrow functions.

#### Example
```js
function sum() {
  let total = 0;
  for (let i = 0; i < arguments.length; i++) total += arguments[i];
  return total;
}
console.log(sum(1, 2, 3));
```

#### Behavior from Example
- Adds all passed values and prints `6`.

### Scope and Function Stack

#### What
- Function calls create execution contexts and stack frames.

#### Why
- Important for recursion and debugging call flow.

#### How
- Each call pushes stack frame; return pops frame.

#### Logic/Concept
- Deep recursion can cause stack overflow.

#### Example
```js
function a() { b(); }
function b() { console.log("stack frame b"); }
a();
```

#### Behavior from Example
- `a` calls `b`; stack grows then unwinds.

### Built-in Functions

#### What
- Global helpers like `parseInt`, `parseFloat`, `isNaN`, `setTimeout`.

#### Why
- Common tasks without custom implementations.

#### How
- Directly call based on utility need.

#### Logic/Concept
- Use correct parser/checker for reliable conversion.

#### Example
```js
console.log(parseInt("42px", 10));
console.log(isNaN("abc"));
```

#### Behavior from Example
- Prints `42` and `true`.

### Default Params

#### What
- Parameter fallback values.

#### Why
- Avoid undefined checks in function body.

#### How
- `function f(a = 1) {}`.

#### Logic/Concept
- Default applies only when argument is `undefined`.

#### Example
```js
function power(base, exp = 2) {
  return base ** exp;
}
console.log(power(3));
```

#### Behavior from Example
- Prints `9` using default exponent.

### Rest Params

#### What
- Collect remaining arguments as real array.

#### Why
- Clean handling of variable-length input.

#### How
- `function f(...items) {}`.

#### Logic/Concept
- Must be last parameter.

#### Example
```js
function total(...nums) {
  return nums.reduce((s, n) => s + n, 0);
}
console.log(total(1, 2, 3, 4));
```

#### Behavior from Example
- Prints `10`.

### Recursion

#### What
- Function calling itself.

#### Why
- Natural fit for trees, nested structures, divide-and-conquer.

#### How
- Define base case and recursive case.

#### Logic/Concept
- Base case prevents infinite recursion.

#### Example
```js
function fact(n) {
  if (n <= 1) return 1;
  return n * fact(n - 1);
}
console.log(fact(5));
```

#### Behavior from Example
- Prints `120`.

### Lexical Scoping

#### What
- Scope determined by where code is written.

#### Why
- Predictable variable resolution.

#### How
- Inner functions can access outer variables.

#### Logic/Concept
- Lookup travels outward through lexical environment chain.

#### Example
```js
const x = "global";
function outer() {
  const x = "outer";
  function inner() {
    console.log(x);
  }
  inner();
}
outer();
```

#### Behavior from Example
- Prints `outer`, not `global`.

### Closures

#### What
- Function + captured lexical variables from outer scope.

#### Why
- Enable private state and function factories.

#### How
- Return inner function that uses outer variables.

#### Logic/Concept
- Captured variables persist even after outer function returns.

#### Example
```js
function makeCounter() {
  let count = 0;
  return function () {
    count += 1;
    return count;
  };
}

const c = makeCounter();
console.log(c(), c());
```

#### Behavior from Example
- Prints `1 2`; state is preserved across calls.

---

## Strict Mode

### What
- Restricted JavaScript mode enabled by `'use strict'`.

### Why
- Prevents silent mistakes and enforces safer behavior.

### How
- Add at top of script or function.
- ESM modules are strict by default.

### Logic/Concept
- Throws errors for unsafe patterns (like undeclared assignments).

### Example
```js
"use strict";

function demo() {
  // accidental = 1; // ReferenceError
  let declared = 1;
  return declared;
}

console.log(demo());
```

### Behavior from Example
- Safe code runs normally.
- Unsafe undeclared assignment would throw error.

---

## this Keyword

### What
- `this` references execution context object.

### Why
- Allows methods to access their owning object data.

### How
- Method call: object before dot becomes `this`.
- Function call: global object (non-strict) or undefined (strict).
- Arrow function: lexical `this` from outer scope.

### Logic/Concept
- `this` depends on call-site, not where function is defined (except arrow functions).

### Example
```js
const obj = {
  name: "Kai",
  regular() {
    return this.name;
  },
  arrowWrap() {
    const fn = () => this.name;
    return fn();
  }
};

console.log(obj.regular());
console.log(obj.arrowWrap());
```

### Behavior from Example
- Both lines print `Kai`.
- Arrow function keeps outer method `this`.

---

## Function Borrowing and Binding

### What
- Use function of one object with another object's data.

### Why
- Reuse logic without duplicating methods.

### How
- `call(thisArg, ...args)`.
- `apply(thisArg, argsArray)`.
- `bind(thisArg, ...args)` returns new function.

### Logic/Concept
- Explicitly controls function context.

### Example
```js
const person1 = {
  name: "Ava",
  intro(city) {
    return `${this.name} from ${city}`;
  }
};

const person2 = { name: "Noah" };

console.log(person1.intro.call(person2, "Delhi"));
console.log(person1.intro.apply(person2, ["Pune"]));
const bound = person1.intro.bind(person2, "Mumbai");
console.log(bound());
```

### Behavior from Example
- All calls use `person2` as `this`.
- Outputs custom city based on call style.

---

## Asynchronous JavaScript

### Event Loop

#### What
- Runtime model coordinating call stack, Web APIs, callback queue, microtask queue.

#### Why
- Enables non-blocking behavior in single-threaded JavaScript.

#### How
- Sync code runs first.
- Then microtasks (Promises).
- Then macrotasks (timers/events).

#### Logic/Concept
- Promise callbacks run before timer callbacks queued in same cycle.

#### Example
```js
console.log("A");
setTimeout(() => console.log("B"), 0);
Promise.resolve().then(() => console.log("C"));
console.log("D");
```

#### Behavior from Example
- Output order: `A`, `D`, `C`, `B`.

### `setTimeout` and `setInterval`

#### What
- Timer APIs for delayed and repeated execution.

#### Why
- Scheduling, polling, and delayed UI actions.

#### How
- `setTimeout(fn, delay)` once.
- `setInterval(fn, delay)` repeatedly.

#### Logic/Concept
- Delay is minimum threshold, exact timing not guaranteed.

#### Example
```js
const id = setInterval(() => console.log("tick"), 500);
setTimeout(() => clearInterval(id), 1600);
```

#### Behavior from Example
- Prints about three `tick` logs then stops.

### Callbacks and Callback Hell

#### What
- Callback is function passed to another function for later execution.
- Callback hell is deeply nested callback structure.

#### Why
- Callbacks are base async mechanism.
- Deep nesting hurts readability and error handling.

#### How
- Prefer promises/async-await for cleaner chaining.

#### Logic/Concept
- Inversion of control can complicate flow.

#### Example
```js
function step1(cb) { setTimeout(() => cb(null, "1"), 100); }
function step2(v, cb) { setTimeout(() => cb(null, v + "2"), 100); }

step1((e1, r1) => {
  if (e1) return;
  step2(r1, (e2, r2) => {
    if (e2) return;
    console.log(r2);
  });
});
```

#### Behavior from Example
- Prints `12` after nested async flow.

### Promises

#### What
- Object representing future completion/failure of async operation.

#### Why
- Better composition and error propagation than nested callbacks.

#### How
- States: pending, fulfilled, rejected.
- Use `.then()`, `.catch()`, `.finally()`.

#### Logic/Concept
- Promise chain passes result or error to next handlers.

#### Example
```js
new Promise((resolve) => resolve(10))
  .then((v) => v * 2)
  .then((v) => console.log(v))
  .catch((err) => console.error(err));
```

#### Behavior from Example
- Prints `20`.

### `async/await`

#### What
- Syntactic sugar over promises.

#### Why
- Makes async code look like synchronous flow.

#### How
- Mark function `async`.
- Use `await` for promise resolution.

#### Logic/Concept
- `await` pauses only inside async function, not whole program.

#### Example
```js
async function run() {
  const value = await Promise.resolve("done");
  console.log(value);
}
run();
```

#### Behavior from Example
- Prints `done` when promise resolves.

---

## Working with APIs

### Fetch

#### What
- Modern promise-based HTTP client in browser and modern runtimes.

#### Why
- Standard way to call REST APIs.

#### How
- `fetch(url, options)` and parse with `response.json()`.

#### Logic/Concept
- Network failures reject promise; HTTP errors require manual `response.ok` check.

#### Example
```js
async function loadUsers() {
  const res = await fetch("https://jsonplaceholder.typicode.com/users");
  if (!res.ok) throw new Error("HTTP error");
  const data = await res.json();
  console.log(data.length);
}
```

#### Behavior from Example
- On success prints number of users.
- Throws custom error for non-2xx status.

### XMLHttpRequest

#### What
- Older event-based HTTP API.

#### Why
- Still appears in legacy codebases.

#### How
- Create object, `open`, assign `onload/onerror`, then `send`.

#### Logic/Concept
- Callback/event style, not promise by default.

#### Example
```js
const xhr = new XMLHttpRequest();
xhr.open("GET", "https://jsonplaceholder.typicode.com/posts/1");
xhr.onload = () => console.log(JSON.parse(xhr.responseText).id);
xhr.onerror = () => console.log("request failed");
xhr.send();
```

#### Behavior from Example
- On success prints post id.
- On failure prints error text.

---

## Modules

### CommonJS

#### What
- Module format used primarily in older Node.js code.

#### Why
- Encapsulates code and exports reusable APIs.

#### How
- Import with `require`, export with `module.exports`.

#### Logic/Concept
- Loaded synchronously in Node runtime.

#### Example
```js
// math.cjs
module.exports.add = (a, b) => a + b;
```

#### Behavior from Example
- Other files can `require` and reuse `add`.

### ESM

#### What
- Standard JavaScript module system (`import`/`export`).

#### Why
- Works in browsers and modern Node.js.

#### How
- Named export/import or default export/import.

#### Logic/Concept
- Static structure enables tree-shaking and better tooling.

#### Example
```js
// math.js
export const sub = (a, b) => a - b;
```

#### Behavior from Example
- `sub` can be imported by name in other ESM files.

---

## Iterators and Generators

### Iterators

#### What
- Objects with `next()` returning `{ value, done }`.

#### Why
- Enables controlled sequential data access.

#### How
- Implement iterator protocol manually or via generators.

#### Logic/Concept
- `for...of` consumes iterators under the hood.

#### Example
```js
const iterator = [1, 2][Symbol.iterator]();
console.log(iterator.next());
console.log(iterator.next());
console.log(iterator.next());
```

#### Behavior from Example
- Returns values then marks completion with `done: true`.

### Generators

#### What
- Functions that can pause/resume with `yield`.

#### Why
- Simplifies writing custom iterators and lazy sequences.

#### How
- Declare with `function*`.

#### Logic/Concept
- Execution state is preserved between `.next()` calls.

#### Example
```js
function* gen() {
  yield 1;
  yield 2;
}

for (const v of gen()) console.log(v);
```

#### Behavior from Example
- Prints `1` then `2` sequentially.

---

## Classes

### What
- ES syntax over prototype-based inheritance.

### Why
- Cleaner OOP-style structure for constructors and shared methods.

### How
- Define `class`, instantiate with `new`, extend with `extends`.

### Logic/Concept
- Methods live on prototype, not duplicated per instance.

### Example
```js
class User {
  constructor(name) {
    this.name = name;
  }
  greet() {
    return `Hi ${this.name}`;
  }
}

class Admin extends User {
  greet() {
    return `Admin ${this.name}`;
  }
}

const a = new Admin("Rex");
console.log(a.greet());
```

### Behavior from Example
- Inherited class overrides method.
- Output is `Admin Rex`.

---

## Memory Management

### Memory Lifecycle

#### What
- Allocate -> use -> release.

#### Why
- Prevent leaks and high memory usage.

#### How
- Remove references when data no longer needed.

#### Logic/Concept
- Unreachable data becomes garbage-collectable.

#### Example
```js
let data = { items: new Array(1000).fill("x") };
console.log(data.items.length);
data = null;
```

#### Behavior from Example
- Object becomes eligible for GC after reference removal.

### Garbage Collection

#### What
- Automatic memory cleanup by engine.

#### Why
- Developers avoid manual free/delete memory operations.

#### How
- Engine tracks reachability.

#### Logic/Concept
- Mark-and-sweep style algorithms remove unreachable objects.

#### Example
```js
function make() {
  const tmp = { val: 1 };
  return tmp.val;
}
make();
```

#### Behavior from Example
- Temporary object becomes unreachable after function ends.

---

## DOM and Browser

### DOM APIs

#### What
- Browser APIs to read/update page nodes.

#### Why
- Build interactive interfaces.

#### How
- Select with `querySelector`, update text/class/style, attach listeners.

#### Logic/Concept
- Event-driven model reacts to user actions.

#### Example
```js
const btn = document.querySelector("#btn");
const out = document.querySelector("#out");

btn.addEventListener("click", () => {
  out.textContent = "Clicked";
});
```

#### Behavior from Example
- Clicking button updates output text in UI.

### Using Browser DevTools

#### What
- Built-in debugging suite in browsers.

#### Why
- Diagnose DOM, network, memory, and performance issues quickly.

#### How
- Use Elements, Console, Network, Performance, Memory tabs.

#### Logic/Concept
- Runtime inspection reveals actual state, not assumptions.

#### Example
```js
console.log("Open DevTools and inspect this log");
```

#### Behavior from Example
- Message appears in Console for inspection.

---

## Debugging

### Debugging Issues

#### What
- Process to find and fix bugs.

#### Why
- Ensures correctness and stable behavior.

#### How
- Add logs, use breakpoints, inspect call stack and variables.

#### Logic/Concept
- Reproduce issue first, then isolate root cause.

#### Example
```js
function calc(a, b) {
  debugger;
  return a + b;
}
console.log(calc(2, 3));
```

#### Behavior from Example
- Execution pauses at `debugger` in dev tools.

### Debugging Memory Leaks

#### What
- Finding retained objects that should be released.

#### Why
- Leaks slow app and can crash long-running sessions.

#### How
- Take heap snapshots, compare growth, inspect retainers.

#### Logic/Concept
- Persistent references (closures/listeners/timers) often cause leaks.

#### Example
```js
const cache = [];
setInterval(() => {
  cache.push(new Array(10000).fill("leak"));
  console.log(cache.length);
}, 1000);
```

#### Behavior from Example
- Memory usage grows continuously over time.

### Debugging Performance

#### What
- Measuring and improving runtime speed/responsiveness.

#### Why
- Fast apps improve UX and reduce resource usage.

#### How
- Profile with Performance tab and `console.time`.

#### Logic/Concept
- Expensive loops/reflows/blocking tasks cause jank.

#### Example
```js
console.time("loop");
let sum = 0;
for (let i = 0; i < 1_000_000; i++) sum += i;
console.timeEnd("loop");
```

#### Behavior from Example
- Console shows time taken by loop.

---

## Related Ecosystem

### React

#### What
- JavaScript library for building component-based UIs.

#### Why
- Declarative rendering and reusable components simplify frontend apps.

#### How
- Build components using JSX and state/props.

#### Logic/Concept
- UI is function of state.

#### Example
```jsx
function Welcome({ name }) {
  return <h1>Hello {name}</h1>;
}
```

#### Behavior from Example
- Component renders greeting using prop value.

### Node.js

#### What
- JavaScript runtime for server-side and tooling.

#### Why
- Use JavaScript outside browser for APIs, scripts, and services.

#### How
- Run files with `node`.
- Use built-in modules (`fs`, `http`, `path`) and npm ecosystem.

#### Logic/Concept
- Event-driven, non-blocking I/O model.

#### Example
```js
const http = require("http");

http.createServer((req, res) => {
  res.end("Hello Node");
}).listen(3000);
```

#### Behavior from Example
- Starts server on port 3000 and replies with `Hello Node`.

---

## Final Revision Grid (A to Z)
- Variables: declarations, hoisting, naming, scope (block/function/global), var/let/const.
- Data Types: string, number, bigint, boolean, undefined, null, symbol, primitive vs object.
- Object internals: prototype, prototypal inheritance, typeof, built-ins.
- Type Casting: explicit conversion and implicit coercion.
- Data Structures: Map, WeakMap, Set, WeakSet, Arrays, Typed Arrays, JSON.
- Equality: `==`, `===`, `Object.is`, value operators, SameValue/SameValueZero concepts.
- Loops: for, while, do...while, for...in, for...of, break, continue.
- Control Flow: if...else, switch, throw, try/catch/finally, Error.
- Operators: assignment, comparison, arithmetic, bitwise, logical, bigint, string, ternary, comma, unary.
- Functions: params, arrow, IIFE, arguments, stack, built-ins, default/rest, recursion, lexical scope, closures.
- Strict Mode and `this` behavior.
- Function borrowing and binding: call, apply, bind.
- Async JS: event loop, timers, callbacks, promises, callback hell, async/await.
- APIs: fetch and XMLHttpRequest.
- Modules: CommonJS and ESM.
- Iterators, generators, classes.
- Memory lifecycle and garbage collection.
- DOM APIs and browser DevTools.
- Debugging: issues, leaks, performance.
- Ecosystem: React and Node.js.
