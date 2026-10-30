# JavaScript DSA — Quick Cheatsheet

> Interview-time syntax reference. Short examples, common operations, and patterns. Focus on remembering **what to write**.

## 1. Strings

```js
s.length                 // length
s[i]                     // character at index
s.at(-1)                 // last character
s.slice(l, r)            // substring [l, r)
s.includes("abc")        // contains?
s.indexOf("a")           // index, or -1
s.startsWith("ab")
s.endsWith("yz")
s.toLowerCase()
s.toUpperCase()
s.trim()                 // remove outer spaces
s.replace("a", "b")      // first match
s.replaceAll("a", "b")
s.split(" ")              // string -> array
arr.join("")              // array -> string
[...s].reverse().join("") // reverse string
```

**Useful conversions**

```js
[..."hello"]                  // ["h", "e", "l", "l", "o"]
"1,2,3".split(",").map(Number) // [1, 2, 3]
String(123)                   // "123"
Number("123")                 // 123
```

## 2. Regex

| Need | Pattern |
|---|---|
| Digits | `/\\d/g` |
| Non-digits | `/\\D/g` |
| Letters | `/[a-z]/gi` |
| Alphanumeric only | `/[^a-z0-9]/gi` |
| Spaces | `/\\s+/g` |
| Only digits | `/^\\d+$/.test(s)` |
| Any uppercase | `/[A-Z]/.test(s)` |

```js
/\d/.test(s)                  // true if digit exists
s.match(/\d+/g)               // all digit groups (or null)
s.replace(/[^a-z0-9]/gi, "")   // remove special chars
s.trim().split(/\s+/)         // split by any whitespace
```

## 3. Arrays

```js
arr.length
arr.push(x)             // add at end
arr.pop()               // remove from end
arr.unshift(x)          // add at start
arr.shift()             // remove from start
arr.splice(i, 1)        // remove at index i (mutates)
arr.slice(l, r)         // copy [l, r) (no mutation)
arr.includes(x)
arr.indexOf(x)          // -1 if absent
arr.reverse()           // mutates
[...arr]                // shallow copy
[...new Set(arr)]       // unique values
```

**Most-used array methods**

```js
arr.map(x => x * 2)             // transform
arr.filter(x => x > 0)          // keep matching
arr.reduce((sum, x) => sum + x, 0) // aggregate
arr.some(x => x === target)     // at least one?
arr.every(x => x > 0)           // all?
arr.find(x => x > 10)           // first matching value
arr.findIndex(x => x > 10)      // its index, or -1
```

## 4. Loops

```js
// Index + value, full control (break/continue)
for (let i = 0; i < arr.length; i++) {
  const x = arr[i];
}

// Values
for (const x of arr) { }

// Index + value
for (const [i, x] of arr.entries()) { }

// Callback (no normal break/continue)
arr.forEach((x, i) => { });

// Transform into NEW array
const doubled = arr.map((x, i) => x * 2);

// Object keys
for (const key in obj) { }

// Map entries
for (const [key, value] of map) { }
```

**Remember:** `for...of` = values. `for...in` = keys. Use classic `for` when you need index control or early exit.

## 5. Map, Set, Object

```js
// Map: key -> value
const map = new Map();
map.set(key, value);
map.get(key);           // undefined if missing
map.has(key);
map.delete(key);
map.size;

// Frequency count
map.set(x, (map.get(x) || 0) + 1);

// Set: unique values
const set = new Set(arr);
set.add(x);
set.has(x);
set.delete(x);
[...set];               // array of unique values

// Plain object frequency
const freq = {};
freq[x] = (freq[x] || 0) + 1;
Object.keys(freq);
Object.values(freq);
Object.entries(freq);
```

## 6. Basic Data Structures

```js
// Stack (LIFO)
const stack = [];
stack.push(x);
stack.at(-1);           // peek
stack.pop();

// Queue (FIFO) — avoid shift() in large BFS
const q = [];
let head = 0;
q.push(start);
while (head < q.length) {
  const node = q[head++];
}

// Min / max
Math.min(...arr);
Math.max(...arr);
```

## 7. Sorting

```js
arr.sort((a, b) => a - b); // numbers ascending
arr.sort((a, b) => b - a); // numbers descending
arr.sort();                // strings lexicographically
arr.sort((a, b) => a[0] - b[0]); // sort by first field

// Multi-condition
arr.sort((a, b) => a[0] - b[0] || a[1] - b[1]);

// Keep original unchanged
const sorted = [...arr].sort((a, b) => a - b);
```

**Important:** Default `sort()` treats values as strings. Numeric arrays need a comparator. `sort()` mutates the original array.

## 8. Destructuring & Shortcuts

```js
const [first, second, ...rest] = arr;
const copy = [...arr];
const merged = [...a, ...b];
[a[i], a[j]] = [a[j], a[i]]; // swap

const value = obj.key ?? defaultValue; // default only for null/undefined
const last = arr.at(-1);
const matrix = Array.from({ length: m }, () => Array(n).fill(0));
```

## 9. Common DSA Templates

**Frequency map**
```js
const freq = new Map();
for (const x of arr) freq.set(x, (freq.get(x) || 0) + 1);
```

**Two pointers**
```js
let l = 0, r = arr.length - 1;
while (l < r) {
  // compare arr[l], arr[r]
  l++; // or r--
}
```

**Sliding window**
```js
let l = 0;
for (let r = 0; r < arr.length; r++) {
  // include arr[r]
  while (/* window invalid */) l++;
  // process window [l, r]
}
```

**Prefix sum**
```js
const pre = Array(arr.length + 1).fill(0);
for (let i = 0; i < arr.length; i++) pre[i + 1] = pre[i] + arr[i];
// sum from l to r = pre[r + 1] - pre[l]
```

**Binary search**
```js
let l = 0, r = arr.length - 1;
while (l <= r) {
  const mid = l + Math.floor((r - l) / 2);
  if (arr[mid] === target) break;
  if (arr[mid] < target) l = mid + 1;
  else r = mid - 1;
}
```

## 10. Don't Forget These

- Numeric sort: always use `(a, b) => a - b`.
- `sort`, `reverse`, `splice`, `push`, `pop` mutate the array.
- `slice` does **not** mutate; `splice` does.
- Avoid `shift()` for large queues; use a head index.
- `map` returns a new array; `forEach` does not.
- Use `Map` for frequency counting and `Set` for fast membership.
- `===` is safer than `==`.
- `typeof null === "object"` (JavaScript quirk).
- `Number` is exact only up to `Number.MAX_SAFE_INTEGER`.
- `Math.max(...arr)` can fail for very large arrays.

## References

- [MDN — Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)
- [MDN — String](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)
- [MDN — Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)
- [MDN — Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)
- [MDN — Regular Expressions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_expressions)
