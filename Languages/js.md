# JavaScript Interview Topics (Core Only)

Focused reference for interview prep.
Each topic includes: What, Example, Logic, Input, Output.

## Quick Links
- [High Priority Topics](#high-priority-topics)
- [Execution Context](#execution-context)
- [Call Stack](#call-stack)
- [Hoisting](#hoisting)
- [Scope Lexical Scope](#scope-lexical-scope)
- [Temporal Dead Zone TDZ](#temporal-dead-zone-tdz)
- [Closures](#closures)
- [this Keyword](#this-keyword)
- [call apply bind](#call-apply-bind)
- [Prototype](#prototype)
- [proto](#proto)
- [Prototype Chain](#prototype-chain)
- [Prototypal Inheritance](#prototypal-inheritance)
- [First-Class Functions](#first-class-functions)
- [Higher-Order Functions](#higher-order-functions)
- [Callback Functions](#callback-functions)
- [Promises](#promises)
- [async await](#async-await)
- [Event Loop](#event-loop)
- [Microtask vs Macrotask](#microtask-vs-macrotask)
- [Event Bubbling](#event-bubbling)
- [Event Capturing](#event-capturing)
- [Event Delegation](#event-delegation)
- [Objects Shallow vs Deep Copy](#objects-shallow-vs-deep-copy)
- [Arrays map filter reduce](#arrays-map-filter-reduce)
- [Spread Rest](#spread-rest)
- [Destructuring](#destructuring)
- [DOM Manipulation](#dom-manipulation)
- [Event Listeners](#event-listeners)
- [let const](#let-const)
- [Arrow Functions](#arrow-functions)
- [Classes](#classes)
- [Modules importexport](#modules-importexport)
- [Iterators & Iterables](#iterators--iterables)
- [Async Iterators](#async-iterators)
- [Generators](#generators)
- [Async Generators](#async-generators)
- [Symbols](#symbols)
- [Computed Properties](#computed-properties)
- [Streams](#streams)
- [Buffers](#buffers)
- [HTTP Streaming SSE](#http-streaming-sse)
- [State Management](#state-management)
- [Control Flow](#control-flow)
- [Separation of Concerns](#separation-of-concerns)
- [Stack vs Heap](#stack-vs-heap)
- [Garbage Collection](#garbage-collection)

---

## High Priority Topics

Most asked in interviews:
- Execution Context
- Hoisting
- Scope and Closures
- this, call, apply, bind
- Prototype and Prototype Chain
- Promises, async await, Event Loop
- Microtask vs Macrotask
- Objects shallow vs deep copy
- Array methods map filter reduce
- DOM events: bubbling, capturing, delegation

### System & AI Development Priority:
- **Async Iterators** (streaming APIs)
- **Async Generators** (real-time data)
- **Streams** (data processing)
- **Closures** (state management)
- **Event Loop** (async brain)
- **Symbols** (protocols, hidden keys)
- **State Management** (agent systems)
- **Control Flow** (execution patterns)

---

## Execution Context

### What
Execution context is the environment in which JavaScript code runs.

### Example
```js
var x = 10;

function test() {
  var y = 20;
  console.log(x + y);
}

test();
```

### Logic
Global context is created first. Then function context is created when test() runs.

### Input
x = 10, y = 20

### Output
30

## Call Stack

### What
Call stack tracks function calls in LIFO order.

### Example
```js
function one() { two(); }
function two() { three(); }
function three() { console.log("done"); }

one();
```

### Logic
one -> two -> three pushed to stack, then popped after execution.

### Input
Call one()

### Output
done

## Hoisting

### What
Declarations move to top of scope during compilation.

### Example
```js
console.log(a);
var a = 5;

// console.log(b); // ReferenceError
let b = 10;
```

### Logic
var is hoisted and initialized as undefined. let is hoisted but remains in TDZ.

### Input
Read variables before declaration line

### Output
undefined for var a, ReferenceError for let b

## Scope Lexical Scope

### What
Scope decides where variables are accessible. Lexical means based on code location.

### Example
```js
const globalVar = "G";

function outer() {
  const outerVar = "O";
  function inner() {
    console.log(globalVar, outerVar);
  }
  inner();
}

outer();
```

### Logic
inner can access variables from parent scopes where it is defined.

### Input
Call outer()

### Output
G O

## Temporal Dead Zone TDZ

### What
TDZ is time between entering scope and let/const declaration line.

### Example
```js
{
  // console.log(a); // ReferenceError
  let a = 1;
  console.log(a);
}
```

### Logic
Variable exists but cannot be used before declaration line.

### Input
Access let variable before and after declaration

### Output
ReferenceError before declaration, 1 after declaration

## Closures

### What
Closure is function with access to outer function variables after outer returns.

### Example
```js
function counter() {
  let count = 0;
  return function () {
    count++;
    return count;
  };
}

const inc = counter();
console.log(inc());
console.log(inc());
```

### Logic
Returned function keeps reference to count.

### Input
Call inc() twice

### Output
1
2

## this Keyword

### What
this refers to the object based on call-site.

### Example
```js
const user = {
  name: "Ava",
  show() {
    console.log(this.name);
  }
};

user.show();
```

### Logic
Method call user.show() binds this to user.

### Input
Object with name and method call

### Output
Ava

## call apply bind

### What
Methods to set this manually.

### Example
```js
const person1 = { name: "Ria" };
const person2 = { name: "Sam" };

function greet(city) {
  console.log(this.name + " from " + city);
}

greet.call(person2, "Delhi");
greet.apply(person2, ["Pune"]);
const bound = greet.bind(person2, "Mumbai");
bound();
```

### Logic
call takes args directly, apply takes array, bind returns new function.

### Input
thisArg = person2, city values

### Output
Sam from Delhi
Sam from Pune
Sam from Mumbai

## Prototype

### What
Prototype is an object used for shared properties/methods.

### Example
```js
function Person(name) {
  this.name = name;
}

Person.prototype.sayHi = function () {
  return "Hi " + this.name;
};

const p = new Person("Nia");
console.log(p.sayHi());
```

### Logic
sayHi is stored once on prototype, shared by instances.

### Input
new Person("Nia")

### Output
Hi Nia

## proto

### What
proto is a reference from object to its prototype object.

### Example
```js
const base = { role: "base" };
const obj = Object.create(base);

console.log(obj.role);
console.log(Object.getPrototypeOf(obj) === base);
```

### Logic
If property not found on obj, engine checks prototype.

### Input
obj without own role property

### Output
base
true

## Prototype Chain

### What
Chain of prototypes used for property lookup.

### Example
```js
const grand = { a: 1 };
const parent = Object.create(grand);
parent.b = 2;
const child = Object.create(parent);

console.log(child.a, child.b);
```

### Logic
Lookup path: child -> parent -> grand -> null.

### Input
Access properties a and b on child

### Output
1 2

## Prototypal Inheritance

### What
Objects inherit directly from other objects.

### Example
```js
const animal = {
  eat() {
    return "eating";
  }
};

const dog = Object.create(animal);
console.log(dog.eat());
```

### Logic
dog inherits eat from animal via prototype link.

### Input
dog.eat()

### Output
eating

## First-Class Functions

### What
Functions are treated like values.

### Example
```js
function sayHi() {
  return "Hi";
}

function run(fn) {
  console.log(fn());
}

run(sayHi);
```

### Logic
Function can be assigned, passed, returned.

### Input
Pass sayHi as argument

### Output
Hi

## Higher-Order Functions

### What
Function that takes/returns another function.

### Example
```js
function multiplyBy(n) {
  return function (x) {
    return x * n;
  };
}

const double = multiplyBy(2);
console.log(double(5));
```

### Logic
multiplyBy returns specialized function.

### Input
n = 2, x = 5

### Output
10

## Callback Functions

### What
Callback is function passed to run later.

### Example
```js
function processUser(name, cb) {
  cb("Hello " + name);
}

processUser("Ira", function (message) {
  console.log(message);
});
```

### Logic
processUser controls when callback executes.

### Input
name = Ira

### Output
Hello Ira

## Promises

### What
Promise represents future result of async operation.

### Example
```js
const p = new Promise((resolve) => {
  setTimeout(() => resolve(42), 100);
});

p.then((value) => console.log(value));
```

### Logic
Promise moves pending -> fulfilled, then then handler runs.

### Input
Resolve value 42

### Output
42

## async await

### What
Cleaner syntax for working with promises.

### Example
```js
function getData() {
  return Promise.resolve("done");
}

async function run() {
  const result = await getData();
  console.log(result);
}

run();
```

### Logic
await pauses inside async function until promise resolves.

### Input
Resolved promise with string done

### Output
done

## Event Loop

### What
Event loop coordinates call stack and task queues.

### Example
```js
console.log("A");
setTimeout(() => console.log("B"), 0);
Promise.resolve().then(() => console.log("C"));
console.log("D");
```

### Logic
Sync code first, then microtasks, then macrotasks.

### Input
One promise callback and one timer callback

### Output
A
D
C
B

## Microtask vs Macrotask

### What
Microtasks (Promise, queueMicrotask) run before macrotasks (setTimeout, setInterval).

### Example
```js
setTimeout(() => console.log("timer"), 0);
Promise.resolve().then(() => console.log("promise"));
console.log("sync");
```

### Logic
After sync finishes, microtask queue drains fully before macrotask.

### Input
One promise and one timer

### Output
sync
promise
timer

## Event Bubbling

### What
Event moves from target element upward to ancestors.

### Example
```html
<div id="parent"><button id="child">Click</button></div>
<script>
  parent.addEventListener("click", () => console.log("parent"));
  child.addEventListener("click", () => console.log("child"));
</script>
```

### Logic
Click child triggers child listener first, then parent in bubbling phase.

### Input
User clicks button child

### Output
child
parent

## Event Capturing

### What
Event travels from root down to target before bubbling.

### Example
```html
<div id="parent"><button id="child">Click</button></div>
<script>
  parent.addEventListener("click", () => console.log("parent capture"), true);
  child.addEventListener("click", () => console.log("child"));
</script>
```

### Logic
Capture listener runs on parent before event reaches child.

### Input
User clicks button child

### Output
parent capture
child

## Event Delegation

### What
Attach one listener to parent to handle many child events.

### Example
```html
<ul id="list">
  <li data-id="1">One</li>
  <li data-id="2">Two</li>
</ul>
<script>
  list.addEventListener("click", (e) => {
    if (e.target.matches("li")) {
      console.log(e.target.dataset.id);
    }
  });
</script>
```

### Logic
Uses bubbling and event.target to detect clicked child.

### Input
Click second li

### Output
2

## Objects Shallow vs Deep Copy

### What
Shallow copy copies top level only. Deep copy copies nested objects too.

### Example
```js
const original = { name: "A", address: { city: "Delhi" } };

const shallow = { ...original };
shallow.address.city = "Pune";

const deep = JSON.parse(JSON.stringify(original));
deep.address.city = "Jaipur";

console.log(original.address.city);
console.log(deep.address.city);
```

### Logic
Shallow shares nested references. Deep copy creates separate nested structures.

### Input
Mutate nested city in shallow and deep copies

### Output
Pune
Jaipur

## Arrays map filter reduce

### What
Common transformation utilities.

### Example
```js
const nums = [1, 2, 3, 4];

const mapped = nums.map((n) => n * 2);
const filtered = nums.filter((n) => n % 2 === 0);
const reduced = nums.reduce((sum, n) => sum + n, 0);

console.log(mapped);
console.log(filtered);
console.log(reduced);
```

### Logic
map transforms, filter selects, reduce accumulates.

### Input
[1, 2, 3, 4]

### Output
[2, 4, 6, 8]
[2, 4]
10

## Spread Rest

### What
Spread expands values. Rest collects values.

### Example
```js
const a = [1, 2];
const b = [...a, 3, 4];

function total(...nums) {
  return nums.reduce((s, n) => s + n, 0);
}

console.log(b);
console.log(total(1, 2, 3));
```

### Logic
Same syntax ... but role depends on position.

### Input
Array a and numbers 1,2,3

### Output
[1, 2, 3, 4]
6

## Destructuring

### What
Extract values from arrays/objects into variables.

### Example
```js
const user = { name: "Mia", age: 22 };
const { name, age } = user;

const arr = [10, 20, 30];
const [first, second] = arr;

console.log(name, age);
console.log(first, second);
```

### Logic
Pattern on left pulls matching values from right.

### Input
Object user and array arr

### Output
Mia 22
10 20

## DOM Manipulation

### What
Reading and updating document elements.

### Example
```html
<p id="msg">Old</p>
<script>
  const el = document.querySelector("#msg");
  el.textContent = "New";
</script>
```

### Logic
Select node then modify content/property/style.

### Input
Initial text Old

### Output
Text on page becomes New

## Event Listeners

### What
Functions triggered when an event occurs.

### Example
```html
<button id="btn">Tap</button>
<script>
  btn.addEventListener("click", () => console.log("clicked"));
</script>
```

### Logic
Browser registers callback and calls it on event.

### Input
User clicks button

### Output
clicked

## let const

### What
Block-scoped declarations. const cannot be reassigned.

### Example
```js
let count = 1;
count = 2;

const PI = 3.14;
// PI = 3.1415; // TypeError

console.log(count, PI);
```

### Logic
Use const by default, let when reassignment is needed.

### Input
Reassign let and attempt const reassignment

### Output
2 3.14
TypeError for const reassignment

## Arrow Functions

### What
Short function syntax with lexical this.

### Example
```js
const obj = {
  value: 10,
  regular() {
    setTimeout(function () {
      console.log(this.value);
    }, 0);
  },
  arrow() {
    setTimeout(() => {
      console.log(this.value);
    }, 0);
  }
};

obj.regular();
obj.arrow();
```

### Logic
Regular function in setTimeout has its own this. Arrow captures outer this.

### Input
Call regular and arrow methods

### Output
undefined (or global value)
10

## Classes

### What
Class syntax for constructor + methods on prototype.

### Example
```js
class User {
  constructor(name) {
    this.name = name;
  }
  greet() {
    return "Hi " + this.name;
  }
}

const u = new User("Rey");
console.log(u.greet());
```

### Logic
class is syntactic sugar over prototypes.

### Input
new User("Rey")

### Output
Hi Rey

## Modules importexport

### What
Split code into reusable files using export and import.

### Example
```js
// math.js
export const add = (a, b) => a + b;

// app.js
import { add } from "./math.js";
console.log(add(2, 3));
```

### Logic
Explicit imports improve maintainability and tooling.

### Input
add(2, 3)

### Output
5

## Iterators & Iterables

### What
Iterators provide interface to iterate through collections. Iterables are objects that can be iterated.

### Example
```js
const arr = [1, 2, 3];
const iterator = arr[Symbol.iterator]();

console.log(iterator.next());
console.log(iterator.next());
console.log(iterator.next());
console.log(iterator.next());
```

### Logic
Symbol.iterator returns iterator object with next() method.

### Input
Array [1, 2, 3]

### Output
{value: 1, done: false}
{value: 2, done: false}
{value: 3, done: false}
{value: undefined, done: true}

## Async Iterators

### What
Iterators for async data sources, used with for await...of.

### Example
```js
const asyncIterable = {
  [Symbol.asyncIterator]() {
    let count = 0;
    return {
      async next() {
        if (count < 3) {
          await new Promise(resolve => setTimeout(resolve, 100));
          return { value: count++, done: false };
        }
        return { done: true };
      }
    };
  }
};

(async () => {
  for await (const num of asyncIterable) {
    console.log(num);
  }
})();
```

### Logic
Async iterator returns Promise from next() method.

### Input
Async iterable with delayed values

### Output
0
1
2

## Generators

### What
Functions that can pause and resume execution using yield.

### Example
```js
function* countGenerator() {
  yield 1;
  yield 2;
  yield 3;
}

const gen = countGenerator();
console.log(gen.next().value);
console.log(gen.next().value);
console.log(gen.next().value);
```

### Logic
Generator returns iterator, yield pauses and returns value.

### Input
Call generator and iterate with next()

### Output
1
2
3

## Async Generators

### What
Generators that can await async operations using yield with await.

### Example
```js
async function* fetchData() {
  yield await Promise.resolve("First");
  await new Promise(resolve => setTimeout(resolve, 100));
  yield await Promise.resolve("Second");
}

(async () => {
  for await (const data of fetchData()) {
    console.log(data);
  }
})();
```

### Logic
Combines generator pause capability with async operations.

### Input
Async generator with promises and delays

### Output
First
Second

## Symbols

### What
Unique identifiers for object properties, used for hidden keys and protocols.

### Example
```js
const idSymbol = Symbol('id');
const user = {
  name: 'John',
  [idSymbol]: 123
};

console.log(user.name);
console.log(user[idSymbol]);
console.log(Object.keys(user));
console.log(Reflect.ownKeys(user));
```

### Logic
Symbols are unique and not enumerable in Object.keys().

### Input
Object with symbol property

### Output
John
123
['name']
['name', Symbol(id)]

## Computed Properties

### What
Dynamic property names using brackets notation.

### Example
```js
const key = 'dynamic';
const obj = {
  [key]: 'value',
  ['computed' + 'Key']: 'result'
};

console.log(obj.dynamic);
console.log(obj.computedKey);
```

### Logic
Expressions inside [] are evaluated to create property names.

### Input
Dynamic key variables

### Output
value
result

## Streams

### What
Sequences of data that can be processed incrementally.

### Example
```js
// Readable stream example (Node.js)
const { Readable } = require('stream');

const readable = Readable.from(['chunk1', 'chunk2', 'chunk3']);

readable.on('data', (chunk) => {
  console.log('Received:', chunk.toString());
});

readable.on('end', () => {
  console.log('Stream ended');
});
```

### Logic
Streams emit data events as chunks become available.

### Input
Array converted to readable stream

### Output
Received: chunk1
Received: chunk2
Received: chunk3
Stream ended

## Buffers

### What
Fixed-size memory chunks for storing binary data.

### Example
```js
// Node.js Buffer
const buf = Buffer.from('Hello', 'utf8');
console.log(buf);
console.log(buf.toString());
console.log(buf[0]);

const buf2 = Buffer.alloc(4);
buf2.write('ABCD');
console.log(buf2.toString());
```

### Logic
Buffers store raw bytes, can be converted to/from strings.

### Input
String 'Hello'

### Output
<Buffer 48 65 6c 6c 6f>
Hello
72
ABCD

## HTTP Streaming SSE

### What
Server-Sent Events for real-time data streaming from server to client.

### Example
```js
// Server (Node.js)
const http = require('http');

http.createServer((req, res) => {
  res.writeHead(200, {
    'Content-Type': 'text/event-stream',
    'Cache-Control': 'no-cache',
    'Connection': 'keep-alive'
  });
  
  let count = 0;
  const interval = setInterval(() => {
    res.write(`data: Message ${count++}\n\n`);
    if (count > 3) {
      clearInterval(interval);
      res.end();
    }
  }, 1000);
}).listen(3000);

// Client
const eventSource = new EventSource('http://localhost:3000');
eventSource.onmessage = (event) => {
  console.log('Received:', event.data);
};
```

### Logic
SSE uses text/event-stream MIME type with data: prefixed messages.

### Input
HTTP request to streaming endpoint

### Output
Received: Message 0
Received: Message 1
Received: Message 2
Received: Message 3

## State Management

### What
Pattern for managing application state centrally.

### Example
```js
class StateManager {
  constructor(initialState = {}) {
    this.state = initialState;
    this.listeners = [];
  }
  
  setState(updates) {
    this.state = { ...this.state, ...updates };
    this.notify();
  }
  
  getState() {
    return this.state;
  }
  
  subscribe(listener) {
    this.listeners.push(listener);
    return () => {
      this.listeners = this.listeners.filter(l => l !== listener);
    };
  }
  
  notify() {
    this.listeners.forEach(listener => listener(this.state));
  }
}

const store = new StateManager({ count: 0 });
store.subscribe((state) => console.log('State changed:', state));
store.setState({ count: 1 });
store.setState({ count: 2 });
```

### Logic
Central state with subscription pattern for reactive updates.

### Input
State updates via setState

### Output
State changed: {count: 1}
State changed: {count: 2}

## Control Flow

### What
Patterns for managing execution flow in complex systems.

### Example
```js
// Sequential execution with error handling
async function executeSteps(steps) {
  const results = [];
  
  for (const step of steps) {
    try {
      const result = await step();
      results.push({ success: true, result });
    } catch (error) {
      results.push({ success: false, error: error.message });
      break; // Stop on first error
    }
  }
  
  return results;
}

// Usage
const steps = [
  () => Promise.resolve('Step 1 complete'),
  () => Promise.reject(new Error('Step 2 failed')),
  () => Promise.resolve('Step 3 complete') // Won't execute
];

executeSteps(steps).then(console.log);
```

### Logic
Control flow patterns handle sequencing, branching, and error recovery.

### Input
Array of async steps with one failure

### Output
[
  {success: true, result: 'Step 1 complete'},
  {success: false, error: 'Step 2 failed'}
]

## Separation of Concerns

### What
Architectural pattern separating different responsibilities.

### Example
```js
// Planner - decides what to do
class Planner {
  plan(goal) {
    return [
      { type: 'fetch', url: '/api/data' },
      { type: 'process', data: null },
      { type: 'save', result: null }
    ];
  }
}

// Executor - executes plans
class Executor {
  async execute(plan) {
    const results = [];
    
    for (const step of plan) {
      switch (step.type) {
        case 'fetch':
          step.data = await this.fetch(step.url);
          break;
        case 'process':
          step.result = this.process(step.data);
          break;
        case 'save':
          await this.save(step.result);
          break;
      }
      results.push(step);
    }
    
    return results;
  }
  
  async fetch(url) {
    return { data: 'fetched data' };
  }
  
  process(data) {
    return data.data.toUpperCase();
  }
  
  async save(result) {
    console.log('Saved:', result);
  }
}

// Tool - provides capabilities
class Tool {
  getCapabilities() {
    return ['fetch', 'process', 'save'];
  }
}

// Usage
const planner = new Planner();
const executor = new Executor();
const tool = new Tool();

const plan = planner.plan('Get and process data');
executor.execute(plan);
```

### Logic
Separate planning, execution, and tool capabilities into distinct components.

### Input
Goal to achieve

### Output
Saved: FETCHED DATA
[execution results]

## Stack vs Heap

### What
Stack stores primitive values and function frames. Heap stores objects.

### Example
```js
let a = 10;
let b = a;
b = 20;

const obj1 = { x: 1 };
const obj2 = obj1;
obj2.x = 99;

console.log(a, b);
console.log(obj1.x);
```

### Logic
Primitives copied by value. Objects copied by reference.

### Input
Change b and obj2.x

### Output
10 20
99

## Garbage Collection

### What
Automatic cleanup of unreachable objects.

### Example
```js
function create() {
  let data = { big: new Array(1000).fill("x") };
  console.log(data.big.length);
}

create();
```

### Logic
After function ends, data has no references and becomes collectible.

### Input
Call create()

### Output
1000, then object is eligible for garbage collection

---

## One-Line Revision
- Understand execution context and call stack deeply.
- Be strong in hoisting, TDZ, scope, closures.
- Master this, call, apply, bind.
- Know prototypes, chain, and inheritance clearly.
- Be perfect with promises, async await, event loop, microtask/macrotask.
- Practice array methods and object copy interview traps.
- Revise DOM events: bubbling, capturing, delegation.
