# JavaScript Cheatsheet

Comprehensive end-to-end reference for JavaScript from core V8 runtime internals, execution context, and event loops to advanced async streaming, metaprogramming (Proxy/Reflect), memory management, and modern ES2020-ES2024+ features.

---

## Table of Contents
- [High Priority Topics](#high-priority-topics)
- [1 JavaScript Runtime, Engines, and Memory Layout](#1-javascript-runtime-engines-and-memory-layout)
- [2 Execution Context, Call Stack, and Hoisting](#2-execution-context-call-stack-and-hoisting)
- [3 Scopes, Lexical Environment, and Closures](#3-scopes-lexical-environment-and-closures)
- [4 Data Types, Type Coercion, and Equality](#4-data-types-type-coercion-and-equality)
- [5 The this Keyword and Explicit Binding](#5-the-this-keyword-and-explicit-binding)
- [6 Prototypes, \_\_proto\_\_, and Prototypal Inheritance](#6-prototypes-__proto__-and-prototypal-inheritance)
- [7 Objects, Immutability, and Cloning](#7-objects-immutability-and-cloning)
- [8 Arrays, Iterables, and Functional Methods](#8-arrays-iterables-and-functional-methods)
- [9 Asynchronous JavaScript, Promises, and async/await](#9-asynchronous-javascript-promises-and-asyncawait)
- [10 Event Loop, Microtasks, and Macrotasks](#10-event-loop-microtasks-and-macrotasks)
- [11 Generators, Iterators, and Async Iteration](#11-generators-iterators-and-async-iteration)
- [12 Symbols, Well-Known Symbols, and Hidden Properties](#12-symbols-well-known-symbols-and-hidden-properties)
- [13 Map, Set, WeakMap, and WeakSet](#13-map-set-weakmap-and-weakset)
- [14 Proxy and Reflect API](#14-proxy-and-reflect-api)
- [15 Streams, Buffers, and Data Processing](#15-streams-buffers-and-data-processing)
- [16 Modern ECMAScript Features (ES2020 - ES2024+)](#16-modern-ecmascript-features-es2020---es2024)
- [17 DOM Manipulation, Event Flow, and Optimization](#17-dom-manipulation-event-flow-and-optimization)
- [18 Modules, CommonJS vs ESM, and Dynamic Imports](#18-modules-commonjs-vs-esm-and-dynamic-imports)
- [19 Concurrency, Web Workers, and Worker Threads](#19-concurrency-web-workers-and-worker-threads)
- [20 Testing, Tooling, and Performance Profiling](#20-testing-tooling-and-performance-profiling)
- [21 Design Patterns and Functional Programming](#21-design-patterns-and-functional-programming)
- [22 High-Yield Interview Questions and Reality Check](#22-high-yield-interview-questions-and-reality-check)

---

## High Priority Topics

Most asked in JavaScript interviews and senior engineering assessments:
1. **Execution Context Lifecycle & Call Stack**
2. **Hoisting (`var` vs `let`/`const` vs Function Declarations) & Temporal Dead Zone (TDZ)**
3. **Lexical Scope, Scope Chain, and Closures**
4. **`this` Binding Rules (Default, Implicit, Explicit `call`/`apply`/`bind`, `new`, Arrow Functions)**
5. **Prototype, `__proto__`, and Prototype Chain Lookup**
6. **Event Loop Mechanics (Microtasks: Promises/`queueMicrotask` vs Macrotasks: `setTimeout`/I/O)**
7. **Promises (`Promise.all`, `allSettled`, `race`, `any`) & `async`/`await` internals**
8. **Shallow Copy (`Object.assign`, spread) vs Deep Copy (`structuredClone`, recursion)**
9. **Metaprogramming with `Proxy` and `Reflect`**
10. **Garbage Collection (Mark-and-Sweep, Generational GC, `WeakMap`/`WeakSet`, Memory Leaks)**

---

## 1 JavaScript Runtime, Engines, and Memory Layout

### V8 Engine Execution Pipeline
```
JavaScript Source Code
       │
       ▼ [Parser]
Abstract Syntax Tree (AST)
       │
       ▼ [Ignition (Bytecode Interpreter)]
Bytecode ───► Executes Immediately
       │
       ▼ (Profiles hot functions)
[TurboFan (Optimizing JIT Compiler)]
       │
       ▼
Optimized Machine Code (De-optimizes if types mutate!)
```

### Memory Architecture: Stack vs Heap
| Region | Stores | Allocation | Access Speed | Managed By |
| :--- | :--- | :--- | :--- | :--- |
| **Call Stack** | Primitive values (`number`, `boolean`, etc.), function execution contexts, reference pointers | Static / Fixed | Extremely fast (LIFO) | OS / Runtime |
| **Memory Heap** | Objects, Arrays, Functions, Closures | Dynamic size | Slower (Pointer lookup) | **Garbage Collector** |

### Garbage Collection Algorithms
- **Mark-and-Sweep**: Traverses from "roots" (Global window/globalThis, current stack frame). Objects unreachable from roots are marked for sweep.
- **Generational GC (V8)**:
  - **Young Generation (Nursery)**: High churn; collected rapidly using Scavenge algorithm.
  - **Old Generation**: Survived multiple scavenge cycles; collected via Mark-Sweep-Compact.
- **Common Memory Leaks**:
  1. Accidental global variables (`window.leakedData = ...`)
  2. Forgotten timers / intervals (`setInterval` without `clearInterval`)
  3. Uncleared DOM element references detached from DOM tree
  4. Stale closures holding references to large structures

---

## 2 Execution Context, Call Stack, and Hoisting

### Execution Context (EC) Structure
Every execution context contains:
1. **Variable Environment (VE)**: Stores `var` declarations, arguments.
2. **Lexical Environment (LE)**: Stores `let`, `const`, outer lexical parent reference (Outer Env).
3. **`this` Binding**: Bound during creation phase.

### Execution Context Phases
```js
console.log(a); // undefined (Hoisted)
// console.log(b); // ReferenceError: Cannot access 'b' before initialization (TDZ)

var a = 10;
let b = 20;

function sayHi() {
  console.log("Hi");
}
```

- **Phase 1: Creation (Memory Allocation Phase)**:
  - Global Object created (`window` in browser, `global` in Node).
  - `this` assigned to Global Object.
  - Function declarations: Stored **in full memory** with definition.
  - `var` variables: Allocated and initialized to `undefined`.
  - `let` & `const` variables: Allocated but **uninitialized** (entered into Temporal Dead Zone).
- **Phase 2: Execution Phase**:
  - Code runs line-by-line, assigns values, invokes functions.

### Temporal Dead Zone (TDZ)
The period between entering the scope and the variable declaration line where accessing `let`/`const` throws a `ReferenceError`.

---

## 3 Scopes, Lexical Environment, and Closures

### Types of Scope
1. **Global Scope**: Accessible everywhere.
2. **Function Scope**: Created by `function` blocks (`var`, `let`, `const`).
3. **Block Scope**: Created by `{ ... }` (`let`, `const` only).
4. **Module Scope**: Isolated per ESM file.

### Closures
A closure is the combination of a function bundled together with references to its **lexical environment** (outer variables).

```js
// 1. Data Encapsulation / Private State
function createCounter(initialValue = 0) {
  let count = initialValue; // Private variable in heap

  return {
    increment: () => ++count,
    decrement: () => --count,
    getCount: () => count,
  };
}

const counter = createCounter(10);
console.log(counter.increment()); // 11
console.log(counter.getCount());  // 11
```

```js
// 2. High-Performance Memoization via Closure
function memoize(fn) {
  const cache = new Map();
  return function (...args) {
    const key = JSON.stringify(args);
    if (cache.has(key)) return cache.get(key);
    const result = fn.apply(this, args);
    cache.set(key, result);
    return result;
  };
}

const slowSquare = (n) => {
  let res = 0;
  for (let i = 0; i < 1e7; i++) { res += n; }
  return res;
};
const fastSquare = memoize(slowSquare);
```

---

## 4 Data Types, Type Coercion, and Equality

### The 8 JavaScript Data Types
- **7 Primitives** (Immutable, passed by value):
  1. `string`
  2. `number` (IEEE-754 64-bit double float)
  3. `bigint` (arbitrary-precision integer)
  4. `boolean`
  5. `undefined`
  6. `null` (Note: `typeof null === "object"` is a legacy bug in JS engine)
  7. `symbol` (unique identifier)
- **1 Non-Primitive** (Mutable, passed by reference):
  8. `object` (Plain objects, Arrays, Functions, Dates, Maps, Sets)

### Equality Comparison: `==` vs `===` vs `Object.is()`
| Comparison | `NaN === NaN` | `+0 === -0` | Coercion (`"5" == 5`) | `null == undefined` |
| :--- | :--- | :--- | :--- | :--- |
| **Loose Equality (`==`)** | `false` | `true` | **`true`** (Performs type coercion) | **`true`** |
| **Strict Equality (`===`)** | `false` | `true` | `false` (No coercion) | `false` |
| **SameValue (`Object.is`)** | **`true`** | **`false`** | `false` (No coercion) | `false` |

```js
// Implicit Coercion Rules:
console.log([] + []);         // "" (both converted to primitive strings)
console.log([] + {});         // "[object Object]"
console.log({} + []);         // "[object Object]" (or 0 if parsed as block statement in console)
console.log("5" - 3);         // 2 ('-' coerces string to number)
console.log("5" + 3);         // "53" ('+' with string coerces number to string)
console.log(true + true);     // 2 (true coerced to 1)
```

---

## 5 The this Keyword and Explicit Binding

### The 5 `this` Binding Rules (in order of priority)
1. **`new` Binding**: `new Constructor()` ➔ `this` points to newly instantiated object.
2. **Explicit Binding**: `.call(context, ...args)`, `.apply(context, [args])`, `.bind(context)` ➔ `this` points to supplied context.
3. **Implicit Binding**: `obj.method()` ➔ `this` points to the object left of the dot (`obj`).
4. **Default Binding**: Plain function call `fn()` ➔ `window` (non-strict) or `undefined` (strict mode `"use strict"`).
5. **Arrow Functions**: Do **NOT** have their own `this`. They resolve `this` lexically from their enclosing scope at declaration time.

```js
const user = {
  name: "Aman",
  regularFn: function () {
    console.log("regular:", this.name);
  },
  arrowFn: () => {
    console.log("arrow:", this.name); // 'this' is lexical window/module
  },
};

const profile = { name: "Navnath" };

user.regularFn.call(profile); // "regular: Navnath" (Explicit)
user.arrowFn.call(profile);   // "arrow: undefined" (Arrow ignore explicit binding!)
```

---

## 6 Prototypes, \_\_proto\_\_, and Prototypal Inheritance

### Prototype Architecture
- Every JavaScript function has a `.prototype` property.
- Every JavaScript object has an internal `[[Prototype]]` link (accessible via `Object.getPrototypeOf(obj)` or `__proto__`).

```
[instance: dog]
  └── __proto__ ──► [Dog.prototype]
                        └── __proto__ ──► [Animal.prototype]
                                              └── __proto__ ──► [Object.prototype]
                                                                    └── __proto__ ──► null
```

### Prototypal Inheritance in Modern JavaScript
```js
// ES6 Class syntax compiles down to prototypal chain
class Animal {
  constructor(name) {
    this.name = name;
  }

  speak() {
    return `${this.name} makes a noise`;
  }
}

class Dog extends Animal {
  #secretTag; // True private field (ES2022)

  constructor(name, breed) {
    super(name);
    this.breed = breed;
    this.#secretTag = "DOG_VERIFIED";
  }

  // Method overriding + super call
  speak() {
    return `${this.name} barks!`;
  }

  // Static class method
  static createPuppy(name) {
    return new Dog(name, "Puppy");
  }
}

const rover = new Dog("Rover", "Husky");
console.log(rover.speak()); // "Rover barks!"
console.log(rover instanceof Animal); // true (traverses prototype chain)
```

---

## 7 Objects, Immutability, and Cloning

### Object Property Descriptors
```js
const user = {};

Object.defineProperty(user, "id", {
  value: "USER_001",
  writable: false,     // Cannot be reassigned
  enumerable: true,    // Appears in Object.keys() / for...in
  configurable: false, // Cannot be deleted or reconfigured
});
```

### Object Protection Levels
| Method | Can Add Props? | Can Delete Props? | Can Modify Existing Props? |
| :--- | :--- | :--- | :--- |
| `Object.preventExtensions(obj)` | **No** | Yes | Yes |
| `Object.seal(obj)` | **No** | **No** | Yes |
| `Object.freeze(obj)` | **No** | **No** | **No** (Shallow freeze) |

### Cloning Objects: Shallow vs Deep
```js
const original = {
  title: "Dev",
  tags: ["js", "ts"],
  meta: { date: new Date() },
};

// 1. Shallow Copy (Spread / Object.assign) - Nested objects are SHARED references!
const shallow = { ...original };
shallow.tags.push("react"); // Mutates original.tags as well!

// 2. Native Deep Copy: structuredClone (ES2022)
// Handles Dates, Sets, Maps, RegExps, ArrayBuffers, and Circular References!
const deep = structuredClone(original);
deep.tags.push("node"); // Leaves original.tags untouched!
```

---

## 8 Arrays, Iterables, and Functional Methods

### Essential Higher-Order Array Methods
```js
const transactions = [
  { id: 1, amount: 100, type: "credit" },
  { id: 2, amount: 50, type: "debit" },
  { id: 3, amount: 200, type: "credit" },
];

// 1. reduce: Accumulate single value
const totalCredit = transactions
  .filter((t) => t.type === "credit")
  .reduce((sum, t) => sum + t.amount, 0); // 300

// 2. flatMap: Map and flatten 1 level
const tags = ["frontend web", "backend node"].flatMap((s) => s.split(" "));
// ["frontend", "web", "backend", "node"]
```

### Immutable Array Methods (ES2023)
```js
const list = [3, 1, 2];

// toSorted, toReversed, toSpliced return a NEW array without mutating original
const sorted = list.toSorted();     // [1, 2, 3] (list remains [3, 1, 2])
const reversed = list.toReversed(); // [2, 1, 3]
const updated = list.with(1, 99);   // [3, 99, 2] (replaces index 1)
```

---

## 9 Asynchronous JavaScript, Promises, and async/await

### The 3 States of a Promise
```
                  ┌──► Fulfilled (value) ──► .then(onFulfilled)
[Promise: Pending]
                  └──► Rejected (error)  ──► .catch(onRejected)
```

### Promise Combinators
| Combinator | Behavior | Fails When |
| :--- | :--- | :--- |
| `Promise.all([p1, p2])` | Resolves array when **all** succeed | **Any** promise rejects (Fast-fail) |
| `Promise.allSettled([p1, p2])` | Resolves array of `{ status, value/reason }` for **all** | **Never rejects** |
| `Promise.race([p1, p2])` | Settles with the **first** promise to settle (fulfill or reject) | First to settle rejects |
| `Promise.any([p1, p2])` | Resolves with the **first fulfilled** value | **All** promises reject (`AggregateError`) |

```js
// Promise.allSettled practical usage
const results = await Promise.allSettled([
  fetch("/api/users"),
  fetch("/api/reports"),
]);

results.forEach((res) => {
  if (res.status === "fulfilled") {
    console.log("Success:", res.value);
  } else {
    console.error("Failed:", res.reason);
  }
});
```

---

## 10 Event Loop, Microtasks, and Macrotasks

### Execution Priority Architecture
```
1. Call Stack (Synchronous Code)
       │
       ▼ (Stack becomes empty)
2. Microtask Queue (Drained COMPLETELY before moving forward!)
   - Promise callbacks (.then, .catch, .finally)
   - queueMicrotask(...)
   - process.nextTick(...) [Node.js - Top Priority]
   - MutationObserver
       │
       ▼ (Microtask queue is empty)
3. Render Queue (Browser only - style, layout, repaint)
       │
       ▼
4. Macrotask Queue (Takes ONE task, then checks Microtasks again!)
   - setTimeout / setInterval
   - setImmediate [Node.js]
   - I/O operations / Network callbacks
   - UI user events (clicks, inputs)
```

### Step-by-Step Tracing Example
```js
console.log("1");

setTimeout(() => {
  console.log("2");
}, 0);

Promise.resolve().then(() => {
  console.log("3");
  queueMicrotask(() => console.log("4"));
});

console.log("5");

// OUTPUT: 1 -> 5 -> 3 -> 4 -> 2
// Explanation:
// Sync: 1, 5
// Microtasks: 3, then 4 (queued during 3)
// Macrotask: 2
```

---

## 11 Generators, Iterators, and Async Iteration

### Custom Iterable (`Symbol.iterator`)
```js
const range = {
  start: 1,
  end: 3,
  [Symbol.iterator]() {
    let current = this.start;
    const last = this.end;
    return {
      next() {
        if (current <= last) {
          return { value: current++, done: false };
        }
        return { value: undefined, done: true };
      },
    };
  },
};

for (const num of range) {
  console.log(num); // 1, 2, 3
}
```

### Generators & Async Generators (Streaming Data)
```js
// Generator function: pause and resume execution
function* idGenerator() {
  let id = 1;
  while (true) {
    const step = yield id++; // Can receive values via .next(val)
    if (step) id += step;
  }
}

// Async Generator for paginated API streaming
async function* fetchAllPages(endpoint) {
  let page = 1;
  let hasMore = true;

  while (hasMore) {
    const response = await fetch(`${endpoint}?page=${page++}`);
    const data = await response.json();
    yield data.items;
    hasMore = data.hasMore;
  }
}

// Consuming with for await...of
for await (const chunk of fetchAllPages("/api/logs")) {
  console.log("Received chunk:", chunk.length);
}
```

---

## 12 Symbols, Well-Known Symbols, and Hidden Properties

### Creating and Using Symbols
```js
const privateId = Symbol("userId");
const globalSym = Symbol.for("app.version"); // Global symbol registry

const user = {
  name: "Aman",
  [privateId]: "HIDDEN_12345",
};

console.log(Object.keys(user)); // ["name"] (Symbols are hidden from normal iteration)
console.log(Object.getOwnPropertySymbols(user)); // [Symbol(userId)]
```

### Well-Known Symbols
- `Symbol.iterator`: Defines default iteration behavior for `for...of`.
- `Symbol.asyncIterator`: Defines `for await...of` streaming.
- `Symbol.toPrimitive`: Custom object-to-primitive conversion logic.
- `Symbol.hasInstance`: Customizes behavior of `instanceof`.

```js
const money = {
  amount: 500,
  [Symbol.toPrimitive](hint) {
    if (hint === "number") return this.amount;
    if (hint === "string") return `$${this.amount}`;
    return this.amount; // default
  },
};

console.log(+money);       // 500
console.log(`${money}`);   // "$500"
```

---

## 13 Map, Set, WeakMap, and WeakSet

### `Map` vs `Object` Comparison
| Feature | `Map` | `Object` |
| :--- | :--- | :--- |
| **Key Types** | Any value (Objects, Functions, Primitives) | `string` or `symbol` only |
| **Iteration Order** | Strictly insertion order | Numeric keys sorted, then insertion |
| **Size** | `map.size` (O(1)) | `Object.keys(obj).length` (O(N)) |
| **Performance** | Optimized for frequent additions/removals | Optimized for fixed shape access |

### `WeakMap` and `WeakSet` (Memory Leak Prevention)
- Keys **MUST be objects**.
- Keys are held **weakly**: If no other references to the object exist, it is automatically garbage collected.
- Not iterable (no `.size`, `.keys()`, `for...of`).

```js
// Practical Use: Associating metadata with DOM nodes without memory leaks
const clickCounts = new WeakMap();

function trackButtonClick(buttonElement) {
  const count = clickCounts.get(buttonElement) || 0;
  clickCounts.set(buttonElement, count + 1);
}
// When buttonElement is removed from DOM, its entry in WeakMap is automatically GC'd!
```

---

## 14 Proxy and Reflect API

### Metaprogramming with Proxy
A `Proxy` wraps a target object and intercepts fundamental operations (traps).

```js
const targetData = { name: "Aman", age: 24 };

const reactiveProxy = new Proxy(targetData, {
  get(target, prop, receiver) {
    console.log(`[READ] Accessed property: ${String(prop)}`);
    return Reflect.get(target, prop, receiver);
  },
  set(target, prop, value, receiver) {
    if (prop === "age" && (typeof value !== "number" || value < 0)) {
      throw new TypeError("Age must be a positive number");
    }
    console.log(`[WRITE] Setting ${String(prop)} = ${value}`);
    return Reflect.set(target, prop, value, receiver);
  },
});

reactiveProxy.age = 25; // [WRITE] Setting age = 25
// reactiveProxy.age = -5; // Throws TypeError: Age must be a positive number
```

---

## 15 Streams, Buffers, and Data Processing

### Binary Data in JavaScript
- `ArrayBuffer`: Fixed-length raw binary buffer in heap memory.
- `TypedArray` (`Uint8Array`, `Int32Array`, `Float64Array`): Views providing structured binary access.
- `DataView`: Heterogeneous endian-controlled binary reader/writer.

```js
const buffer = new ArrayBuffer(16); // 16 bytes
const view = new DataView(buffer);
view.setInt32(0, 1000, true); // Little-endian 32-bit integer
```

### Web Streams API
```js
// ReadableStream consumer
async function readStream(readableStream) {
  const reader = readableStream.getReader();
  const decoder = new TextDecoder();

  while (true) {
    const { done, value } = await reader.read();
    if (done) break;
    console.log("Chunk received:", decoder.decode(value));
  }
}
```

---

## 16 Modern ECMAScript Features (ES2020 - ES2024+)

```js
// 1. Optional Chaining (?.) & Nullish Coalescing (??)
const userCity = user?.profile?.address?.city ?? "Default City";

// 2. Logical Assignment Operators (??=, ||=, &&=)
let config = null;
config ??= { retries: 3 }; // Assigns only if config is null or undefined

// 3. Object.hasOwn (Safer alternative to Object.prototype.hasOwnProperty)
const hasEmail = Object.hasOwn(user, "email");

// 4. Top-Level Await (ES2022 Modules)
const dbConnection = await connectToDatabase();

// 5. Object.groupBy (ES2024)
const people = [
  { name: "Alice", role: "admin" },
  { name: "Bob", role: "user" },
  { name: "Charlie", role: "admin" },
];
const byRole = Object.groupBy(people, (p) => p.role);
// { admin: [{ name: "Alice", ... }, { name: "Charlie", ... }], user: [{ name: "Bob", ... }] }

// 6. Promise.withResolvers() (ES2024)
const { promise, resolve, reject } = Promise.withResolvers();
```

---

## 17 DOM Manipulation, Event Flow, and Optimization

### The 3 Phases of DOM Event Flow
```
        [Window]
           │ ▲
1. Capture │ │ 3. Bubble
           ▼ │
        [Target Element] ◄── 2. Target Phase
```

```js
// Event Delegation Pattern: 1 listener on parent handles 1000s of child items
const userList = document.getElementById("user-list");

userList.addEventListener("click", (event) => {
  const target = event.target.closest("li.user-item");
  if (!target) return;

  const userId = target.dataset.id;
  console.log(`Clicked user: ${userId}`);
});
```

### Performance Optimization: Reflow vs Repaint
- **Reflow (Layout)**: Recalculates element positions/geometry (`offsetWidth`, `style.width`, `appendChild`). Expensive!
- **Repaint**: Visual changes that don't affect geometry (`color`, `background-color`, `visibility`).
- **Batching with `DocumentFragment`**:
```js
const fragment = document.createDocumentFragment();
for (let i = 0; i < 100; i++) {
  const el = document.createElement("div");
  el.textContent = `Item ${i}`;
  fragment.appendChild(el);
}
document.body.appendChild(fragment); // Triggers ONLY 1 reflow!
```

---

## 18 Modules, CommonJS vs ESM, and Dynamic Imports

### CommonJS vs ES Modules
| Feature | CommonJS (`require`) | ES Modules (`import`/`export`) |
| :--- | :--- | :--- |
| **Loading** | Synchronous, Dynamic | Asynchronous, Static analysis |
| **Scope** | File-level wrapped function | Strict mode by default, distinct module scope |
| **Bindings** | Values are **copied** at export time | **Live references** to exported variables |
| **Top-Level `this`** | Points to `exports` object | `undefined` |
| **Tree-Shaking** | Hard / impossible | **Supported natively** |

```js
// Dynamic Import (Code-splitting & conditional loading)
async function triggerExport() {
  const { exportToCsv } = await import("./csvExporter.js");
  exportToCsv(data);
}
```

---

## 19 Concurrency, Web Workers, and Worker Threads

JavaScript execution in the main thread is single-threaded. CPU-heavy tasks must be offloaded to worker threads.

```js
// main.js
const worker = new Worker("worker.js");

worker.postMessage({ command: "COMPUTE_HASH", data: hugePayload });

worker.onmessage = (event) => {
  console.log("Worker completed result:", event.data);
};

// worker.js (Runs in isolated background thread)
self.onmessage = (event) => {
  const result = heavyComputation(event.data);
  self.postMessage(result);
};
```

---

## 20 Testing, Tooling, and Performance Profiling

### Testing with Vitest / Jest
```js
import { describe, it, expect, vi } from "vitest";

function calculateDiscount(price, percentage) {
  if (percentage < 0 || percentage > 100) throw new RangeError("Invalid discount");
  return price - (price * percentage) / 100;
}

describe("Discount Calculator", () => {
  it("calculates 20% discount correctly", () => {
    expect(calculateDiscount(100, 20)).toBe(80);
  });

  it("throws for invalid ranges", () => {
    expect(() => calculateDiscount(100, 150)).toThrow(RangeError);
  });
});
```

### Performance Profiling API
```js
performance.mark("task-start");
runComplexOperation();
performance.mark("task-end");

performance.measure("Task Duration", "task-start", "task-end");
const measures = performance.getEntriesByName("Task Duration");
console.log(`Execution time: ${measures[0].duration.toFixed(2)}ms`);
```

---

## 21 Design Patterns and Functional Programming

### 1. Pub-Sub / Event Emitter Pattern
```js
class EventEmitter {
  constructor() {
    this.events = new Map();
  }

  on(event, listener) {
    if (!this.events.has(event)) this.events.set(event, new Set());
    this.events.get(event).add(listener);
    return () => this.events.get(event).delete(listener); // Unsubscribe
  }

  emit(event, ...args) {
    if (this.events.has(event)) {
      this.events.get(event).forEach((listener) => listener(...args));
    }
  }
}
```

### 2. Currying & Function Composition
```js
// Currying: transforms fn(a, b, c) into fn(a)(b)(c)
const curry = (fn) => {
  return function curried(...args) {
    if (args.length >= fn.length) return fn.apply(this, args);
    return (...nextArgs) => curried.apply(this, args.concat(nextArgs));
  };
};

// Function Composition (Pipe: Left-to-Right execution)
const pipe = (...fns) => (x) => fns.reduce((v, f) => f(v), x);

const trim = (str) => str.trim();
const toUpper = (str) => str.toUpperCase();
const exclaim = (str) => `${str}!`;

const formatGreeting = pipe(trim, toUpper, exclaim);
console.log(formatGreeting("  hello world  ")); // "HELLO WORLD!"
```

---

## 22 High-Yield Interview Questions and Reality Check

### 1. Explain the output of `for (var i = 0; i < 3; i++) { setTimeout(() => console.log(i), 0); }` vs `let`.
> **Answer**:
> - With `var`: Prints `3, 3, 3`. `var` is function-scoped, so all 3 callbacks share the same single global/function variable `i`. By the time the macrotask queue executes the callbacks, the loop has finished and `i === 3`.
> - With `let`: Prints `0, 1, 2`. `let` is block-scoped, so each loop iteration creates a **new lexical scope binding** for `i` preserved in that callback's closure.

### 2. What is the difference between `Object.freeze()` and `const`?
> **Answer**: `const` prevents variable identifier reassignment (`const x = {}` cannot do `x = 5`), but object properties can still be mutated (`x.a = 10`). `Object.freeze()` makes the object values immutable at runtime (`x.a = 10` fails in strict mode), but does not prevent the variable from pointing to another object unless combined with `const`.

### 3. What is the difference between `__proto__` and `prototype`?
> **Answer**: `prototype` is a property present on constructor functions / classes used as the blueprint to assign `[[Prototype]]` when instances are created via `new`. `__proto__` (or `Object.getPrototypeOf()`) is the actual reference pointer on an instance pointing to its prototype up the chain.

### 4. What is the difference between `process.nextTick()` and `setImmediate()` in Node.js?
> **Answer**: `process.nextTick()` callbacks are processed immediately after the current operation in the microtask phase (before the event loop continues). `setImmediate()` callbacks are placed in the Check phase queue of the event loop and execute on the next turn of the event loop cycle.

### 5. Why does `0.1 + 0.2 !== 0.3` in JavaScript?
> **Answer**: JavaScript numbers use the IEEE-754 double-precision 64-bit floating-point standard. In binary base-2, fractions like 0.1 and 0.2 are repeating decimals that cannot be stored with infinite precision, leading to small rounding errors (`0.30000000000000004`).

---

### Reality Check & Best Practices
- Always use `const` by default; only use `let` when reassignment is explicitly required. Never use `var`.
- Use `structuredClone()` instead of `JSON.parse(JSON.stringify())` for deep cloning to preserve Dates, Sets, Maps, and prevent circular reference crashes.
- Use `Event Delegation` when binding listeners to repeated lists instead of attaching separate listeners to each DOM node.
- In Node.js / Server environments, avoid synchronous blocking CPU loops on the main thread; offload heavy processing to `worker_threads` or async streaming pipelines.
- Prefer `Map` over plain objects when keys are dynamically generated, deleted, or of non-string types.
