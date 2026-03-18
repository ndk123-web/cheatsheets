# TypeScript Cheatsheet

A quick reference guide covering all important TypeScript concepts.

---

## What is TypeScript?
TypeScript is a typed superset of JavaScript that compiles to plain JavaScript.
It adds static types, better tooling, and safer refactoring for medium/large codebases.

---

## Table of Contents
- [Introduction](#introduction)
- [Installation and Setup](#installation-and-setup)
- [TypeScript Types](#typescript-types)
- [Assertions](#assertions)
- [Type System Core](#type-system-core)
- [Combining Types](#combining-types)
- [Type Guards and Narrowing](#type-guards-and-narrowing)
- [Functions](#functions)
- [Interfaces](#interfaces)
- [Classes](#classes)
- [Generics](#generics)
- [Utility Types](#utility-types)
- [Decorators](#decorators)
- [Advanced Types](#advanced-types)
- [Modules](#modules)
- [Ecosystem](#ecosystem)
- [Related Roadmaps](#related-roadmaps)

---

## Introduction

### Introduction to TypeScript
- Adds compile-time type checking to JavaScript.
- Improves DX with IntelliSense, autocomplete, and early error detection.

```ts
const user: string = "Aman";
```

### TypeScript vs JavaScript
- JavaScript: dynamic types at runtime.
- TypeScript: static checks at compile-time + emits JavaScript.

```ts
// TS error: Type 'number' is not assignable to type 'string'
let name: string = 123;
```

### TS and JS Interoperability
- TypeScript can compile existing JavaScript projects incrementally.
- `allowJs` lets `.js` files participate in TS build.

```json
{
  "compilerOptions": {
    "allowJs": true,
    "checkJs": true
  }
}
```

---

## Installation and Setup

### Installation and Configuration

```bash
npm install -D typescript
npx tsc --init
```

### tsconfig.json
Main project config file for the TypeScript compiler.

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "NodeNext",
    "strict": true,
    "outDir": "dist",
    "rootDir": "src"
  },
  "include": ["src"]
}
```

### Compiler Options (Common)
- `strict`: enables all strict checks.
- `noImplicitAny`: blocks implicit `any`.
- `strictNullChecks`: null/undefined safety.
- `module`, `target`, `baseUrl`, `paths`, `skipLibCheck`.

### Running TypeScript

#### tsc
```bash
npx tsc
npx tsc --watch
```

#### ts-node
```bash
npm install -D ts-node
npx ts-node src/index.ts
```

### TS Playground
- Use official playground for quick experiments and sharing snippets.
- URL: https://www.typescriptlang.org/play

---

## TypeScript Types

### Primitive Types

```ts
let isDone: boolean = false;
let count: number = 42;
let title: string = "TS";
let nothing: void = undefined;
let maybe: undefined = undefined;
let empty: null = null;
```

### Object Types

#### Interface
```ts
interface User {
  id: number;
  name: string;
}
```

#### Class
```ts
class Person {
  constructor(public name: string) {}
}
```

#### Enum
```ts
enum Role {
  Admin,
  User,
}
```

#### Array
```ts
const nums: number[] = [1, 2, 3];
const names: Array<string> = ["A", "B"];
```

#### Tuple
```ts
const pair: [string, number] = ["age", 22];
```

### Top Types

#### Object
```ts
let obj: object = { a: 1 };
```

#### unknown
```ts
let input: unknown = "hello";
if (typeof input === "string") {
  console.log(input.toUpperCase());
}
```

#### any
```ts
let risky: any = 123;
risky = "anything";
risky.nonExistent(); // no compile-time safety
```

### Bottom Types

#### never
```ts
function fail(msg: string): never {
  throw new Error(msg);
}
```

---

## Assertions

### as const
```ts
const config = {
  mode: "dark",
  retries: 3,
} as const;
```

### as [type]
```ts
const value = "42" as string;
```

### as any
```ts
const unsafe = value as any;
unsafe.maybeMissingMethod();
```

### Non-null Assertion
```ts
const el = document.getElementById("app")!;
```

### satisfies keyword
```ts
type Theme = "light" | "dark";
const settings = {
  theme: "dark",
  fontSize: 14,
} satisfies { theme: Theme; fontSize: number };
```

---

## Type System Core

### Type Inference
TypeScript infers types from values and usage.

```ts
let score = 99; // inferred as number
```

### Type Compatibility
Structural typing: compatible if shape matches.

```ts
type A = { name: string };
type B = { name: string; age: number };

const b: B = { name: "A", age: 20 };
const a: A = b; // OK (B has at least A's shape)
```

---

## Combining Types

### Union Types
```ts
let id: string | number;
id = "u1";
id = 1;
```

### Intersection Types
```ts
type Timestamped = { createdAt: Date };
type Named = { name: string };
type Item = Timestamped & Named;
```

### Type Aliases
```ts
type ID = string | number;
```

### keyof Operator
```ts
type User = { id: number; name: string };
type UserKeys = keyof User; // "id" | "name"
```

---

## Type Guards and Narrowing

### instanceof
```ts
class Car {}
const v: unknown = new Car();
if (v instanceof Car) {
  console.log("Car instance");
}
```

### typeof
```ts
function print(x: string | number) {
  if (typeof x === "string") return x.toUpperCase();
  return x.toFixed(2);
}
```

### Equality Narrowing
```ts
function same(a: string | number, b: string) {
  if (a === b) {
    // a is string here
    return a.toUpperCase();
  }
}
```

### Truthiness Narrowing
```ts
function greet(name?: string) {
  if (name) return `Hi ${name}`;
  return "Hi guest";
}
```

### Type Predicates
```ts
type Cat = { meow: () => void };
type Dog = { bark: () => void };

function isCat(pet: Cat | Dog): pet is Cat {
  return "meow" in pet;
}
```

---

## Functions

### TypeScript Functions
```ts
function add(a: number, b: number): number {
  return a + b;
}
```

### Typing Functions
```ts
const multiply: (x: number, y: number) => number = (x, y) => x * y;
```

### Function Overloading
```ts
function format(value: number): string;
function format(value: Date): string;
function format(value: number | Date): string {
  if (typeof value === "number") return value.toFixed(2);
  return value.toISOString();
}
```

---

## Interfaces

### TypeScript Interfaces
```ts
interface Product {
  id: number;
  name: string;
  readonly sku: string;
  discount?: number;
}
```

### Types vs Interfaces
- Interface: best for object contracts, declaration merging.
- Type: works with unions/intersections/primitives/tuples.

### Extending Interfaces
```ts
interface Animal { name: string }
interface Dog extends Animal { breed: string }
```

### Interface Declaration Merging
```ts
interface Box { width: number }
interface Box { height: number }
const box: Box = { width: 10, height: 20 };
```

### Hybrid Types
```ts
interface Counter {
  (start: number): string;
  interval: number;
  reset(): void;
}
```

---

## Classes

### Classes
```ts
class User {
  constructor(public name: string) {}
  greet() { return `Hi ${this.name}`; }
}
```

### Constructor Params
```ts
class Point {
  constructor(public x: number, public y: number) {}
}
```

### Constructor Overloading
```ts
class DateValue {
  value: Date;
  constructor(value: string);
  constructor(value: number);
  constructor(value: string | number) {
    this.value = new Date(value);
  }
}
```

### Access Modifiers
```ts
class Wallet {
  public owner: string;
  private balance: number;
  protected currency: string;

  constructor(owner: string, balance: number) {
    this.owner = owner;
    this.balance = balance;
    this.currency = "INR";
  }
}
```

### Abstract Classes
```ts
abstract class Shape {
  abstract area(): number;
}

class Circle extends Shape {
  constructor(private r: number) { super(); }
  area() { return Math.PI * this.r * this.r; }
}
```

### Inheritance vs Polymorphism
- Inheritance: child extends parent behavior.
- Polymorphism: same method name, different implementation.

### Method Overriding
```ts
class Animal {
  speak() { return "sound"; }
}
class Dog extends Animal {
  override speak() { return "bark"; }
}
```

---

## Generics

### Generics
```ts
function identity<T>(value: T): T {
  return value;
}
```

### Generic Types
```ts
type ApiResponse<T> = {
  data: T;
  error: string | null;
};
```

### Generic Constraints
```ts
function len<T extends { length: number }>(input: T): number {
  return input.length;
}
```

---

## Utility Types

```ts
type User = { id: number; name: string; email?: string };

type U1 = Partial<User>;                      // all optional
type U2 = Pick<User, "id" | "name">;       // selected keys
type U3 = Omit<User, "email">;              // removed keys
type U4 = Readonly<User>;                     // readonly keys
type U5 = Record<string, number>;             // key-value map
type U6 = Exclude<"a" | "b" | "c", "a">; // "b" | "c"
type U7 = Extract<"a" | "b", "b" | "c">; // "b"
type U8 = NonNullable<string | null>;         // string
type U9 = Parameters<(a: number, b: string) => void>; // [number, string]
type U10 = ReturnType<() => Promise<number>>; // Promise<number>
type U11 = InstanceType<typeof Date>;         // Date

type U12 = Awaited<Promise<Promise<string>>>; // string
```

---

## Decorators

Enable in config (legacy decorators flow in many setups):

```json
{
  "compilerOptions": {
    "experimentalDecorators": true
  }
}
```

Example:

```ts
function sealed(constructor: Function) {
  Object.seal(constructor);
  Object.seal(constructor.prototype);
}

@sealed
class Service {}
```

---

## Advanced Types

### Mapped Types
```ts
type Flags<T> = {
  [K in keyof T]: boolean;
};
```

### Conditional Types
```ts
type IsString<T> = T extends string ? true : false;
```

### Literal Types
```ts
type Direction = "left" | "right";
```

### Template Literal Types
```ts
type EventName<T extends string> = `on${Capitalize<T>}`;
type ClickEvent = EventName<"click">; // "onClick"
```

### Recursive Types
```ts
type Json = string | number | boolean | null | Json[] | { [k: string]: Json };
```

---

## Modules

### TypeScript Modules
Use `export` and `import` for file-level modules.

```ts
// math.ts
export const add = (a: number, b: number) => a + b;
```

### Namespaces
Used mostly in legacy code or global script contexts.

```ts
namespace Utils {
  export const version = "1.0.0";
}
```

### Ambient Modules
Declare types for non-typed external modules.

```ts
declare module "legacy-lib" {
  export function doThing(): void;
}
```

### External Modules
Any file with top-level import/export is an external module.

### Namespace Augmentation
```ts
declare namespace NodeJS {
  interface ProcessEnv {
    APP_ENV: "dev" | "prod";
  }
}
```

### Global Augmentation
```ts
declare global {
  interface Window {
    appVersion: string;
  }
}
export {};
```

---

## Ecosystem

### Formatting
- Prettier for consistent style.

```bash
npm install -D prettier
```

### Linting
- ESLint with TypeScript plugin.

```bash
npm install -D eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin
```

### Useful Packages
- `ts-node`
- `tsx`
- `zod`
- `type-fest`
- `tslib`
- `vite`

### Build Tools
- Vite
- esbuild
- SWC
- tsup
- webpack (with ts-loader)

---

## Related Roadmaps
- JavaScript Roadmap
- Node.js Roadmap
- Backend Roadmap
- Frontend Roadmap

---

## Usage
Use this as a quick navigation file.
Each topic can be expanded into its own `.md` file for detailed explanation.
