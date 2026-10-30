# DSA in JavaScript Cheatsheet

A practical, interview-focused JavaScript reference for solving Data Structures and Algorithms problems. This complements the C++ DSA sheet: focus on syntax recall, built-in methods, common data structures, and concise patterns that reduce implementation time.

> Use JavaScript to express the algorithm clearly. Know the time complexity and edge cases; built-in methods do not replace algorithmic reasoning.

## Table of Contents
- [1. Quick Template and Input](#1-quick-template-and-input)
- [2. Data Types and Type Checks](#2-data-types-and-type-checks)
- [3. Strings](#3-strings)
- [4. Regular Expressions](#4-regular-expressions)
- [5. Arrays and Common Operations](#5-arrays-and-common-operations)
- [6. Loops and Iteration Styles](#6-loops-and-iteration-styles)
- [7. Map, Set, and Objects](#7-map-set-and-objects)
- [8. Stack, Queue, and Deque Patterns](#8-stack-queue-and-deque-patterns)
- [9. Sorting Techniques](#9-sorting-techniques)
- [10. Useful Math and Bitwise Operations](#10-useful-math-and-bitwise-operations)
- [11. Common DSA Patterns in JavaScript](#11-common-dsa-patterns-in-javascript)
- [12. Gotchas and Complexity](#12-gotchas-and-complexity)
- [13. Quick Interview Reference](#13-quick-interview-reference)

---

## 1. Quick Template and Input

LeetCode usually provides the function signature. For local Node.js scripts:

```js
const fs = require("node:fs");
const input = fs.readFileSync(0, "utf8").trim().split(/\s+/);
```

Useful defaults:

```js
const nums = [4, 2, 7];
const n = nums.length;
const str = "hello";
const chars = [...str]; // Unicode code points (not grapheme clusters)
```

Use `const` by default; use `let` when reassignment is required. Avoid `var`.

## 2. Data Types and Type Checks

JavaScript primitive types: `string`, `number`, `bigint`, `boolean`, `undefined`, `symbol`, and `null`. Objects include arrays, functions, maps, sets, and plain objects.

```js
typeof 10;             // "number"
typeof "hi";           // "string"
typeof true;           // "boolean"
typeof undefined;      // "undefined"
typeof 10n;            // "bigint"
typeof null;           // "object" (historical JS quirk)
Array.isArray([1, 2]); // true
Number.isNaN(NaN);     // true
Number.isFinite(10);   // true
value == null;         // true for null OR undefined
value === null;        // only null
```

**Equality:** Prefer `===` and `!==` to avoid implicit type coercion.

**Number caveat:** JavaScript `number` is IEEE-754 double precision. Integers are exactly represented only within `Number.MAX_SAFE_INTEGER` (`2 ** 53 - 1`). Use `BigInt` for larger integer arithmetic; do not mix `BigInt` and `number` directly.

```js
const big = 9007199254740993n;
const next = big + 2n;
const safe = Number.isSafeInteger(123);
```

## 3. Strings

Strings are immutable. Methods return new strings; they do not mutate the original.

### Access, search, and slicing

```js
const s = "javascript";
s.length;                 // 10
s[0];                     // "j"
s.at(-1);                 // "t" (negative index supported)
s.includes("script");     // true
s.indexOf("a");           // 1; -1 if absent
s.lastIndexOf("a");       // 3
s.startsWith("java");     // true
s.endsWith("script");     // true
s.slice(0, 4);            // "java"
s.slice(-6);              // "script"
s.substring(0, 4);        // "java" (negative values become 0; swaps start/end)
```

### Transform and split

```js
"  Hello JS  ".trim();          // "Hello JS"
"hello".toUpperCase();          // "HELLO"
"HELLO".toLowerCase();          // "hello"
"one-two".replace("-", " ");    // "one two" (first match)
"a-a-a".replaceAll("a", "x");   // "x-x-x"
"one,two,three".split(",");     // ["one", "two", "three"]
["a", "b"].join("-");           // "a-b"
"ha".repeat(3);                 // "hahaha"
```

### Character/code-point operations

```js
const chars = [..."hello"];     // ["h", "e", "l", "l", "o"]
"ABC".charCodeAt(0);            // UTF-16 code unit: 65
"😄".codePointAt(0);            // Unicode code point
String.fromCharCode(65);        // "A"
String.fromCodePoint(0x1F604);  // "😄"
```

### Reverse and palindrome

```js
const reversed = [...s].reverse().join("");
const isPalindrome = (s) => {
  let left = 0, right = s.length - 1;
  while (left < right) {
    if (s[left++] !== s[right--]) return false;
  }
  return true;
};
```

### Frequency count

```js
const freq = new Map();
for (const ch of str) freq.set(ch, (freq.get(ch) ?? 0) + 1);

// Plain object alternative for simple ASCII/lowercase keys:
const count = {};
for (const ch of str) count[ch] = (count[ch] ?? 0) + 1;
```

**Useful interview transformations**

```js
const onlyDigits = str.replace(/\D/g, "");
const lettersOnly = str.replace(/[^a-z]/gi, "");
const normalized = str.toLowerCase().replace(/[^a-z0-9]/g, "");
const words = str.trim().split(/\s+/);
```

## 4. Regular Expressions

Regex is useful for validation, filtering, tokenizing, and cleaning strings. Use `/.../flags` literals.

| Pattern | Meaning |
| --- | --- |
| `\\d` / `\\D` | digit / non-digit |
| `\\w` / `\\W` | word character / non-word |
| `\\s` / `\\S` | whitespace / non-whitespace |
| `[a-z]` | lowercase ASCII letter |
| `[A-Z]` | uppercase ASCII letter |
| `[0-9]` | digit |
| `[^...] ` | character not in the set |
| `+` / `*` / `?` | 1+ / 0+ / optional |
| `{m,n}` | between m and n repetitions |
| `^` / `$` | start / end of input (or line with m flag) |
| `( ... )` | capturing group |
| `(?: ... )` | non-capturing group |
| `a|b` | a OR b |

### Test, match, replace, split

```js
/^\d+$/.test("123");             // true: only digits
/[aeiou]/i.test("sky");          // false: contains a vowel?
"ab12cd34".match(/\d+/g);        // ["12", "34"]
"ab12cd34".match(/\d+/);         // ["12"] first match
"abc123".replace(/\d/g, "");     // "abc"
"a  b   c".split(/\s+/);         // ["a", "b", "c"]
"cat CAT".match(/cat/gi);         // ["cat", "CAT"]
```

### Common patterns

```js
const digits = s.match(/\d/g) ?? [];
const letters = s.match(/[a-z]/gi) ?? [];
const words = s.match(/[a-z0-9]+/gi) ?? [];
const isDigitsOnly = /^\d+$/.test(s);
const hasUpper = /[A-Z]/.test(s);
const collapseSpaces = s.trim().replace(/\s+/g, " ");
const removeNonAlphaNumeric = s.replace(/[^a-z0-9]/gi, "");
```

**Regex warning:** `\w` and `[a-z]` are not universal Unicode-letter matchers. For Unicode-aware text, use property escapes where supported, e.g. `/\p{L}/u` for letters and `/\p{N}/u` for numbers. Be careful with the stateful `g`/`y` flags on repeated `.test()` calls because `lastIndex` changes.

## 5. Arrays and Common Operations

Arrays are dynamic and zero-indexed. Most direct indexed reads/writes are O(1).

### Create, access, update

```js
const a = [10, 20, 30];
a.length;              // 3
a[0];                  // 10
a[a.length - 1];       // 30
a.at(-1);              // 30
a[1] = 99;
const zeros = Array(5).fill(0);
const matrix = Array.from({ length: 3 }, () => Array(4).fill(0));
```

### Add and remove

```js
const a = [2, 3];
a.push(4);             // append; returns new length
a.pop();               // remove last; returns removed value
a.unshift(1);          // add at front; O(n)
a.shift();             // remove first; O(n)
a.splice(1, 1);        // remove 1 item at index 1; mutates
a.splice(1, 0, 8);     // insert 8 at index 1; mutates
```

### Search and inspect

```js
a.includes(3);                 // boolean
a.indexOf(3);                  // index or -1
a.find(x => x > 5);            // first matching value
a.findIndex(x => x > 5);       // index or -1
a.some(x => x < 0);            // at least one?
a.every(x => x >= 0);          // all?
```

### Transform and aggregate

```js
const doubled = a.map(x => x * 2);                 // new array
const evens = a.filter(x => x % 2 === 0);          // selected values
const sum = a.reduce((acc, x) => acc + x, 0);      // aggregate
const flat = [[1, 2], [3]].flat();                 // [1, 2, 3]
const pairs = a.map((value, index) => [index, value]);
```

### Copy and slicing

```js
const copy = [...a];          // shallow copy
const copy2 = a.slice();      // shallow copy
const part = a.slice(1, 3);   // [start, end), does not mutate
const reversedCopy = [...a].reverse();
```

### Destructuring and spread

```js
const [first, second, ...rest] = [10, 20, 30, 40];
// first=10, second=20, rest=[30,40]

const combined = [...a, ...b];
const [head, ...tail] = nums;
const swapped = (arr, i, j) => {
  [arr[i], arr[j]] = [arr[j], arr[i]];
};
```

### Convert between string and array

```js
const chars = [..."hello"];
const joined = chars.join("");
const nums = "10,20,30".split(",").map(Number);
const digits = String(12345).split("").map(Number);
```

## 6. Loops and Iteration Styles

Choose the loop based on whether you need values, indexes, transformation, filtering, or early exit.

```js
// Classic for: full control, indexes, reverse loops, break/continue
for (let i = 0; i < arr.length; i++) {
  console.log(i, arr[i]);
}

// Reverse traversal
for (let i = arr.length - 1; i >= 0; i--) { /* ... */ }

// for...of: values of arrays, strings, sets
for (const value of arr) { /* ... */ }

// for...in: enumerable keys (commonly object keys); avoid for array values
for (const key in obj) { console.log(key, obj[key]); }

// forEach: callback for each array item; cannot break/continue the outer loop
arr.forEach((value, index) => { /* ... */ });

// map: transform each item and return a new array
const doubled = arr.map((value, index) => value * 2);

// filter: keep items that pass a condition
const positives = arr.filter(value => value > 0);

// entries: index-value pairs
for (const [index, value] of arr.entries()) { /* ... */ }

// Object iteration
for (const [key, value] of Object.entries(obj)) { /* ... */ }
```

**2D arrays**

```js
for (let r = 0; r < grid.length; r++) {
  for (let c = 0; c < grid[r].length; c++) {
    const value = grid[r][c];
  }
}
for (const row of grid) {
  for (const value of row) { /* ... */ }
}
```

**Important:** `map()` is for producing a transformed array; `forEach()` is for side effects. Use `for` or `for...of` when you need early `break`, `continue`, or precise index control. Do not use `for...in` to iterate array values.

## 7. Map, Set, and Objects

### Map (key-value pairs)

Map keys can be values of any type. Average lookup/insert/delete is typically O(1).

```js
const map = new Map();
map.set("a", 1);
map.set("b", 2);
map.get("a");             // 1
map.has("c");             // false
map.delete("a");
map.size;

for (const [key, value] of map) { /* ... */ }
const freq = new Map();
for (const x of nums) freq.set(x, (freq.get(x) ?? 0) + 1);
```

### Set (unique values)

```js
const set = new Set([1, 2, 2, 3]);
set.add(4);
set.has(2);               // true
set.delete(1);
set.size;
const unique = [...new Set(nums)];
```

### Plain objects

```js
const freq = {};
freq[key] = (freq[key] ?? 0) + 1;
Object.keys(freq);        // keys
Object.values(freq);      // values
Object.entries(freq);     // [key, value] pairs
Object.hasOwn(freq, key); // own-property check
```

Prefer `Map` when keys are not guaranteed to be safe strings, or when you need frequent additions/deletions and a clear key-value API. Prefer `Set` for membership and deduplication.

## 8. Stack, Queue, and Deque Patterns

### Stack (LIFO) using an array

```js
const stack = [];
stack.push(value);  // push
const top = stack.at(-1); // peek
const removed = stack.pop(); // pop
```

### Queue (FIFO) with a head pointer

Avoid repeated `shift()` for large queues because it moves remaining elements.

```js
const queue = [];
let head = 0;
queue.push(start);
while (head < queue.length) {
  const node = queue[head++];
  // process node; queue.push(neighbor)
}
```

### Monotonic stack template

```js
const stack = [];
const answer = Array(nums.length).fill(-1);
for (let i = 0; i < nums.length; i++) {
  while (stack.length && nums[stack.at(-1)] < nums[i]) {
    const j = stack.pop();
    answer[j] = nums[i];
  }
  stack.push(i);
}
```

## 9. Sorting Techniques

JavaScript's `sort()` mutates the original array and sorts elements as strings by default. Always provide a numeric comparator for numbers.

```js
const nums = [10, 2, 30, 4];
nums.sort((a, b) => a - b); // ascending
nums.sort((a, b) => b - a); // descending

const words = ["pear", "apple", "fig"];
words.sort(); // lexicographic

const people = [{ age: 30 }, { age: 20 }];
people.sort((a, b) => a.age - b.age);

// Sort without mutating original (modern JS)
const sorted = [...nums].sort((a, b) => a - b);
const sorted2 = nums.toSorted((a, b) => a - b);
```

### Comparator rules

- Return a negative number when `a` should come before `b`.
- Return zero when they are equal for the ordering.
- Return a positive number when `a` should come after `b`.

### Multi-key sorting

```js
items.sort((a, b) => {
  if (a.score !== b.score) return b.score - a.score; // score descending
  return a.name.localeCompare(b.name);               // name ascending
});

// Intervals: start ascending, then end ascending
intervals.sort((a, b) => a[0] - b[0] || a[1] - b[1]);
```

### Sorting strings and characters

```js
const chars = [..."dcab"].sort().join(""); // "abcd"
const sortedWords = words.toSorted((a, b) => a.localeCompare(b));
```

## 10. Useful Math and Bitwise Operations

```js
Math.max(...nums);
Math.min(...nums);
Math.floor(5 / 2);       // 2
Math.ceil(5 / 2);        // 3
Math.trunc(-2.8);        // -2
Math.abs(-5);            // 5
Math.sqrt(49);           // 7
Math.pow(2, 10);         // 1024
2 ** 10;                 // 1024
Number.isInteger(4);     // true
```

Spread into `Math.max/min` can exceed argument limits for very large arrays; use a loop or `reduce` for large inputs.

### Modulo and negative values

```js
((x % k) + k) % k; // normalized non-negative remainder for positive k
```

### Bitwise operations (32-bit signed integers)

```js
x & 1;          // test odd/even
x << 1;         // shift left (beware 32-bit conversion)
x >> 1;         // signed right shift
x >>> 1;        // unsigned right shift
x & (x - 1);    // remove lowest set bit
```

JavaScript bitwise operators convert operands to 32-bit integers. For large integer bit manipulation, use `BigInt` bitwise operators.

## 11. Common DSA Patterns in JavaScript

### Frequency map

```js
const freq = new Map();
for (const x of nums) freq.set(x, (freq.get(x) ?? 0) + 1);
```

### Two pointers

```js
let left = 0, right = nums.length - 1;
while (left < right) {
  const sum = nums[left] + nums[right];
  if (sum === target) break;
  if (sum < target) left++;
  else right--;
}
```

### Sliding window

```js
let left = 0, sum = 0;
for (let right = 0; right < nums.length; right++) {
  sum += nums[right];
  while (sum > target) sum -= nums[left++];
  // window is [left, right]
}
```

### Prefix sum

```js
const prefix = Array(nums.length + 1).fill(0);
for (let i = 0; i < nums.length; i++) {
  prefix[i + 1] = prefix[i] + nums[i];
}
// sum of nums[l..r] = prefix[r + 1] - prefix[l]
```

### Binary search

```js
let left = 0, right = nums.length - 1;
while (left <= right) {
  const mid = left + Math.floor((right - left) / 2);
  if (nums[mid] === target) { /* found */ break; }
  if (nums[mid] < target) left = mid + 1;
  else right = mid - 1;
}
```

### BFS grid directions

```js
const dirs = [[1, 0], [-1, 0], [0, 1], [0, -1]];
for (const [dr, dc] of dirs) {
  const nr = r + dr, nc = c + dc;
  if (nr < 0 || nr >= rows || nc < 0 || nc >= cols) continue;
  // validate and visit grid[nr][nc]
}
```

## 12. Gotchas and Complexity

| Operation | Typical complexity / note |
| --- | --- |
| `arr[i]`, `arr.length` | O(1) |
| `push()`, `pop()` | Amortized O(1) |
| `shift()`, `unshift()`, middle `splice()` | O(n) |
| `includes()`, `indexOf()`, `find()` | O(n) |
| `map()`, `filter()`, `forEach()`, `reduce()` | O(n), excluding callback cost |
| `sort()` | Usually O(n log n); exact algorithm is engine-dependent |
| `Map/Set` get/add/delete | Average sublinear; commonly treated as O(1) in interview analysis |
| `slice()`, spread-copy array | O(n) |
| `reverse()` | O(n), mutates array |

### Common mistakes

1. **Default sort is lexicographic:** `[1, 10, 2].sort()` becomes `[1, 10, 2]`; use `(a, b) => a - b`.
2. **Mutation:** `sort()`, `reverse()`, `splice()`, `push()`, and `pop()` mutate the array.
3. **Queue cost:** repeated `shift()` can make BFS inefficient; use a head pointer.
4. **Empty reduce:** `[].reduce(...)` without an initial value throws. Supply an initial accumulator.
5. **Array fill references:** `Array(3).fill([])` reuses the same inner array. Use `Array.from({ length: 3 }, () => [])`.
6. **String immutability:** assign the returned value from `replace()`, `trim()`, etc.
7. **Numeric precision:** use `Number.isSafeInteger()` or `BigInt` when needed.
8. **Object key coercion:** object keys are strings or symbols; use `Map` for arbitrary key types.
9. **Falsy values:** do not confuse `0`, `false`, `""`, `null`, and `undefined`. Use `??` when only nullish values need a default.

## 13. Quick Interview Reference

| Need | JavaScript |
| --- | --- |
| String to character array | `[...str]` |
| Character array to string | `chars.join("")` |
| Split words by whitespace | `str.trim().split(/\\s+/)` |
| Remove non-alphanumeric | `str.replace(/[^a-z0-9]/gi, "")` |
| Check regex | `/pattern/.test(str)` |
| Extract all digits | `str.match(/\\d/g) ?? []` |
| Unique values | `[...new Set(arr)]` |
| Frequency counter | `Map` + `get/set` |
| Any element matches | `arr.some(fn)` |
| All elements match | `arr.every(fn)` |
| Transform array | `arr.map(fn)` |
| Filter array | `arr.filter(fn)` |
| Sum array | `arr.reduce((s, x) => s + x, 0)` |
| Numeric ascending sort | `arr.sort((a, b) => a - b)` |
| Descending sort | `arr.sort((a, b) => b - a)` |
| Stack | `push()` / `pop()` |
| Queue | `push()` + head index |
| Loop values | `for (const x of arr)` |
| Loop index and value | `for (const [i, x] of arr.entries())` |
| Safe default for nullish | `value ?? fallback` |

---

## References
- [MDN: JavaScript Guide](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide)
- [MDN: String](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)
- [MDN: Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)
- [MDN: Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)
- [MDN: Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)
- [MDN: RegExp](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_expressions)
- [MDN: Array sort](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)
- [ECMAScript specification](https://tc39.es/ecma262/)
