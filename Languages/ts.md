# TypeScript Cheatsheet

Comprehensive end-to-end reference for TypeScript from core static typing foundations to advanced type gymnastics, package registries, module systems, Zod runtime validation, React/Fullstack architecture, testing, and production tooling.

---

## Table of Contents
- [High Priority Topics](#high-priority-topics)
- [1 Setup, Toolchains, and Package Registries](#1-setup-toolchains-and-package-registries)
- [2 Compiler and tsconfig.json Mastery](#2-compiler-and-tsconfigjson-mastery)
- [3 Core Primitives and Literal Types](#3-core-primitives-and-literal-types)
- [4 Top, Bottom, and Special Types](#4-top-bottom-and-special-types)
- [5 Objects, Type Aliases, and Interfaces](#5-objects-type-aliases-and-interfaces)
- [6 Union, Intersection, and Discriminated Unions](#6-union-intersection-and-discriminated-unions)
- [7 Type Narrowing, Guards, and Assertion Functions](#7-type-narrowing-guards-and-assertion-functions)
- [8 Functions, Overloading, and this Typing](#8-functions-overloading-and-this-typing)
- [9 Arrays, Tuples, and Readonly Slices](#9-arrays-tuples-and-readonly-slices)
- [10 Generics, Constraints, and Const Type Parameters](#10-generics-constraints-and-const-type-parameters)
- [11 Index Types, keyof, and typeof](#11-index-types-keyof-and-typeof)
- [12 Conditional Types and the infer Keyword](#12-conditional-types-and-the-infer-keyword)
- [13 Mapped Types and Key Remapping](#13-mapped-types-and-key-remapping)
- [14 Template Literal Types and String Manipulation](#14-template-literal-types-and-string-manipulation)
- [15 Built-in Utility Types Exhaustive Guide](#15-built-in-utility-types-exhaustive-guide)
- [16 Classes, OOP, and Parameter Properties](#16-classes-oop-and-parameter-properties)
- [17 Enums vs Const Objects](#17-enums-vs-const-objects)
- [18 Modules, Imports/Exports, and Interoperability](#18-modules-importsexports-and-interoperability)
- [19 Ambient Declarations, .d.ts, and Module Augmentation](#19-ambient-declarations-dts-and-module-augmentation)
- [20 Modern TC39 Decorators](#20-modern-tc39-decorators)
- [21 Runtime Schema Validation (Zod Integration)](#21-runtime-schema-validation-zod-integration)
- [22 Frontend and Fullstack Typing Patterns (React & APIs)](#22-frontend-and-fullstack-typing-patterns-react--apis)
- [23 Testing, Linters, and Monorepos](#23-testing-linters-and-monorepos)
- [24 High-Yield Interview Questions and Reality Check](#24-high-yield-interview-questions-and-reality-check)

---

## High Priority Topics

Most asked in TypeScript interviews and core architecture:
1. **`type` vs `interface` (Declaration Merging, Extensibility, Unions, Performance)**
2. **`unknown` vs `any` vs `never` vs `void` (The Type Hierarchy Matrix)**
3. **Discriminated Unions & Exhaustiveness Checking with `never`**
4. **`satisfies` Operator vs Type Annotations vs `as const`**
5. **Type Predicates (`val is Type`) vs Assertion Signatures (`asserts val is Type`)**
6. **Conditional Types & Pattern Matching with `infer`**
7. **Mapped Types with Key Remapping (`as`) & Template Literals**
8. **Structural Typing (Duck Typing) vs Nominal Typing & Excess Property Checks**
9. **ESM vs CJS Interoperability & Type-Only Imports (`import type`)**
10. **Runtime Boundary Validation (Zod + `z.infer`)**

---

## 1 Setup, Toolchains, and Package Registries

### Package Managers and Registries
| Tool / Registry | Description | Command |
| :--- | :--- | :--- |
| **npm** | Default Node.js package manager | `npm i -D typescript @types/node` |
| **pnpm** | Fast, disk space efficient (hard linking) | `pnpm add -D typescript @types/node` |
| **yarn** | Berry / Classic workspace manager | `yarn add -D typescript @types/node` |
| **bun** | Native TS execution engine & package manager | `bun add -d typescript` (Run: `bun run src/index.ts`) |
| **jsr** | Modern TypeScript-first registry by Deno | `npx jsr add @std/path` (native `.ts` source distribution) |

### Executing TypeScript in Development
```bash
# 1. Native / Fast execution with tsx (Recommended for Node)
npm install -D tsx
npx tsx src/index.ts
npx tsx watch src/index.ts

# 2. ts-node (Classic)
npm install -D ts-node
npx ts-node src/index.ts

# 3. Bun (Native TS engine, zero config)
bun run src/index.ts
```

### Publishing Typed Packages (`package.json`)
```json
{
  "name": "@my-org/core-lib",
  "version": "1.0.0",
  "type": "module",
  "main": "./dist/index.cjs",
  "module": "./dist/index.js",
  "types": "./dist/index.d.ts",
  "exports": {
    ".": {
      "import": {
        "types": "./dist/index.d.ts",
        "default": "./dist/index.js"
      },
      "require": {
        "types": "./dist/index.d.cts",
        "default": "./dist/index.cjs"
      }
    }
  },
  "scripts": {
    "build": "tsc -p tsconfig.build.json",
    "prepublishOnly": "npm run build"
  }
}
```

---

## 2 Compiler and tsconfig.json Mastery

### Production `tsconfig.json`
```json
{
  "compilerOptions": {
    /* Language & Environment Target */
    "target": "ES2022",
    "lib": ["ES2022", "DOM", "DOM.Iterable"],
    "module": "NodeNext",
    "moduleResolution": "NodeNext",

    /* Output & Directory Structure */
    "outDir": "./dist",
    "rootDir": "./src",
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,

    /* Strict Type-Checking (Always enable in production) */
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "noImplicitThis": true,
    "alwaysStrict": true,

    /* Advanced Linter Checks */
    "noUncheckedIndexedAccess": true,       // Accessing array[i] yields T | undefined
    "exactOptionalPropertyTypes": true,     // Distinguishes { a?: string } from { a?: string | undefined }
    "noImplicitReturns": true,              // Ensures all code branches return a value
    "noFallthroughCasesInSwitch": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,

    /* Interoperability & Emit */
    "esModuleInterop": true,
    "isolatedModules": true,
    "verbatimModuleSyntax": true,          // Enforces 'import type' for type-only imports
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,

    /* Path Aliasing */
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    }
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

### Monorepo Project References (`composite: true`)
```json
// packages/core/tsconfig.json
{
  "compilerOptions": {
    "composite": true,
    "outDir": "./dist",
    "rootDir": "./src"
  }
}

// root tsconfig.json
{
  "files": [],
  "references": [
    { "path": "./packages/core" },
    { "path": "./packages/api" }
  ]
}
```
Run `tsc --build` for lightning-fast incremental monorepo compilation.

---

## 3 Core Primitives and Literal Types

### Primitive Scalar Types
```ts
const count: number = 42;
const username: string = "alex";
const isActive: boolean = true;
const bigId: bigint = 9007199254740991n;
const uniqueKey: symbol = Symbol("uniqueId");
const unassigned: undefined = undefined;
const emptyValue: null = null;
```

### Literal Types and Type Widening
```ts
let mutableStr = "hello"; // Inferred as type: string (widened)
const fixedStr = "hello"; // Inferred as literal type: "hello"

type Direction = "North" | "South" | "East" | "West";
type HttpSuccessCode = 200 | 201 | 204;
```

### `as const` vs `satisfies`
| Modifier / Keyword | What it does | Runtime output |
| :--- | :--- | :--- |
| `as const` | Freezes literal types, marks objects/arrays as `readonly` | None (compile-time only) |
| `satisfies` | Validates type conformance without widening literal types | None (compile-time only) |
| `: Type` Annotation | Validates type but widens exact literals to broad types | None (compile-time only) |

```ts
type RouteConfig = {
  path: string;
  method: "GET" | "POST";
};

// 1. satisfies preserves exact literals:
const route = {
  path: "/api/users",
  method: "GET",
} satisfies RouteConfig;

console.log(route.method); // Inferred as exact "GET", not broadened to "GET" | "POST"

// 2. as const creates deeply readonly object:
const appConfig = {
  env: "production",
  port: 8080,
  features: ["auth", "payments"],
} as const;
// appConfig.port = 9000; // COMPILE ERROR: Cannot assign to read-only property
```

---

## 4 Top, Bottom, and Special Types

### The Complete Type Hierarchy Matrix
| Type | Assign Anything To It? | Can Assign It To Other Types? | Allowed Operations | Ideal Use Case |
| :--- | :--- | :--- | :--- | :--- |
| `any` | **Yes** | **Yes** (Bypasses compiler) | All operations (No safety) | Legacy JS migration only |
| `unknown` | **Yes** (Top Type) | **No** (Only to `any` and `unknown`) | **None** without narrowing | API inputs, dynamic JSON |
| `void` | `undefined` only | `any`, `unknown` | None | Functions returning nothing |
| `never` | **No** (Bottom Type) | **Yes** (Assignable to everything) | None | Exhaustiveness checking |

```ts
// 1. unknown (Safe Top Type)
function parseApiResponse(raw: unknown) {
  // raw.toUpperCase(); // COMPILE ERROR: Object is of type 'unknown'
  if (typeof raw === "string") {
    console.log(raw.toUpperCase()); // OK: Narrowed to string
  }
}

// 2. never (Bottom Type - Empty Set)
function throwFatalError(msg: string): never {
  throw new Error(`Fatal Error: ${msg}`);
}
```

---

## 5 Objects, Type Aliases, and Interfaces

### `type` vs `interface` Comparison Table
| Feature | `interface` | `type` Alias |
| :--- | :--- | :--- |
| **Object shape definition** | `interface User { id: string }` | `type User = { id: string }` |
| **Declaration Merging** | **Yes** (Multiple definitions merge) | **No** (Throws duplicate identifier) |
| **Extending / Composition** | `interface B extends A` | `type B = A & { extra: string }` |
| **Unions / Primitives / Tuples** | No | **Yes** (`type ID = string \| number`) |
| **Compiler Performance** | Faster (Cached flat object lookup) | Slightly slower on large intersection chains |
| **Best Practice** | Public APIs, OOP contracts, Library `.d.ts` | Domain logic, unions, utility transformations |

### Practical Patterns
```ts
// 1. Interface Declaration Merging (Augmentation)
interface AppSettings {
  appName: string;
}
interface AppSettings {
  port: number; // Automatically merged into { appName: string; port: number; }
}

// 2. Structural Typing (Duck Typing)
type Point2D = { x: number; y: number };
type Point3D = { x: number; y: number; z: number };

const p3: Point3D = { x: 10, y: 20, z: 30 };
const p2: Point2D = p3; // OK: p3 has all required fields of Point2D
```

### Excess Property Checks
Excess property checks occur ONLY on direct object literals:
```ts
interface User {
  name: string;
}

// ERROR: Object literal may only specify known properties
// const u1: User = { name: "Alex", age: 25 };

// Bypassed via intermediate variable (Structural subtyping):
const rawObj = { name: "Alex", age: 25 };
const u2: User = rawObj; // OK!
```

---

## 6 Union, Intersection, and Discriminated Unions

### Union (`|`) and Intersection (`&`)
```ts
// Union: value can be ANY of the types
type Primitive = string | number | boolean;

// Intersection: value must have ALL properties of both types
type Identifiable = { id: string };
type Timestamped = { createdAt: Date; updatedAt: Date };
type Entity = Identifiable & Timestamped;
```

### Discriminated Unions (Tagged Unions)
A pattern using a literal discriminator property (e.g. `status`, `kind`, `type`) for compile-time safety.

```ts
type AsyncData<T> =
  | { status: "idle" }
  | { status: "pending" }
  | { status: "success"; data: T; timestamp: number }
  | { status: "error"; error: Error };

function handleData<T>(state: AsyncData<T>): string {
  switch (state.status) {
    case "idle":
      return "Ready to fetch";
    case "pending":
      return "Loading data...";
    case "success":
      return `Loaded: ${JSON.stringify(state.data)}`;
    case "error":
      return `Failed: ${state.error.message}`;
    default: {
      // Exhaustiveness check: If a new variant is added, TS flags error here!
      const _exhaustiveCheck: never = state;
      return _exhaustiveCheck;
    }
  }
}
```

---

## 7 Type Narrowing, Guards, and Assertion Functions

### Built-in Narrowing Mechanisms
```ts
// 1. typeof (Primitives)
function printValue(val: string | number) {
  if (typeof val === "string") return val.toUpperCase();
  return val.toFixed(2);
}

// 2. instanceof (Class instances)
if (err instanceof TypeError) { /* handle */ }

// 3. 'in' operator (Object keys)
type Admin = { role: "admin"; permissions: string[] };
type Customer = { role: "customer"; loyaltyPoints: number };

function authorize(user: Admin | Customer) {
  if ("permissions" in user) {
    console.log(user.permissions); // user is Admin
  }
}
```

### Custom Type Predicates (`val is Type`)
```ts
interface HTTPError {
  statusCode: number;
  message: string;
}

function isHttpError(error: unknown): error is HTTPError {
  return (
    typeof error === "object" &&
    error !== null &&
    "statusCode" in error &&
    typeof (error as HTTPError).statusCode === "number"
  );
}
```

### Assertion Functions (`asserts condition`)
```ts
function assertNonNull<T>(val: T, msg?: string): asserts val is NonNullable<T> {
  if (val === null || val === undefined) {
    throw new Error(msg ?? "Value must not be null or undefined");
  }
}

function processId(id: string | null) {
  assertNonNull(id, "ID is required");
  console.log(id.toUpperCase()); // id is statically typed as 'string'
}
```

---

## 8 Functions, Overloading, and this Typing

### Function Types and Rest Parameters
```ts
type AsyncHandler<TInput, TOutput> = (input: TInput) => Promise<TOutput>;

const calculateTotal = (taxRate: number, ...prices: number[]): number => {
  return prices.reduce((sum, p) => sum + p * (1 + taxRate), 0);
};
```

### Function Overloading
```ts
// Overload signatures
function parseInput(value: string): string[];
function parseInput(value: number): number[];
// Implementation signature (hidden from callers)
function parseInput(value: string | number): (string | number)[] {
  if (typeof value === "string") return value.split(",");
  return Array.from({ length: value }, (_, i) => i + 1);
}

const s = parseInput("a,b,c"); // string[]
const n = parseInput(5);       // number[]
```

### Typing `this` in Methods
```ts
interface SessionManager {
  token: string;
  getToken(this: SessionManager): string;
}

const manager: SessionManager = {
  token: "abc-123",
  getToken() {
    return this.token;
  },
};
```

---

## 9 Arrays, Tuples, and Readonly Slices

```ts
// 1. Arrays & Readonly Arrays
const mutableArr: number[] = [1, 2, 3];
const immutableArr: readonly number[] = [1, 2, 3];
// immutableArr.push(4); // COMPILE ERROR: Property 'push' does not exist

// 2. Named Tuples
type GeoCoord = [latitude: number, longitude: number, altitude?: number];
const loc: GeoCoord = [37.7749, -122.4194];

// 3. Rest Tuple Elements
type CommandArgs = [command: string, ...flags: string[]];
const cmd: CommandArgs = ["git", "commit", "-m", "Initial commit"];
```

---

## 10 Generics, Constraints, and Const Type Parameters

### Generic Functions, Interfaces, and Defaults
```ts
interface ApiResponse<TData = unknown, TMeta = Record<string, unknown>> {
  status: number;
  data: TData;
  meta: TMeta;
}
```

### Generic Constraints (`extends`)
```ts
interface HasId {
  id: string | number;
}

function findById<T extends HasId>(items: T[], id: string | number): T | undefined {
  return items.find((item) => item.id === id);
}
```

### Const Type Parameters (TS 5.0+)
```ts
// Without <const T>, passing ["admin", "editor"] infers string[]
// With <const T>, infers readonly ["admin", "editor"]
function defineRoles<const T extends readonly string[]>(roles: T): T {
  return roles;
}

const appRoles = defineRoles(["admin", "editor", "viewer"]);
```

---

## 11 Index Types, keyof, and typeof

```ts
interface DatabaseUser {
  id: number;
  username: string;
  profile: {
    bio: string;
    avatarUrl: string;
  };
}

// 1. keyof operator: extracts union of keys
type UserKeys = keyof DatabaseUser; // "id" | "username" | "profile"

// 2. Indexed Access Type (T[K])
type UserProfile = DatabaseUser["profile"]; // { bio: string; avatarUrl: string }
type BioType = DatabaseUser["profile"]["bio"]; // string

// 3. typeof operator: extracts type from runtime value
const defaultOptions = { timeoutMs: 5000, retryLimit: 3 };
type Options = typeof defaultOptions; // { timeoutMs: number; retryLimit: number; }

// 4. Type-safe property getter
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}
```

---

## 12 Conditional Types and the infer Keyword

### Mechanics of Conditional Types
```ts
type IsArray<T> = T extends any[] ? true : false;
type A1 = IsArray<string[]>; // true
type A2 = IsArray<number>;   // false
```

### Distributive Conditional Types
Naked generic type parameters distribute across unions:
```ts
type ToArray<T> = T extends any ? T[] : never;
type Distributed = ToArray<string | number>; // string[] | number[]

// Disable distribution using tuple wrapping [T]:
type NonDistributive<T> = [T] extends [any] ? T[] : never;
type Combined = NonDistributive<string | number>; // (string | number)[]
```

### Pattern Matching with `infer`
```ts
// 1. Extract Return Type
type InferReturn<T> = T extends (...args: any[]) => infer R ? R : never;

// 2. Recursively Unwrap Promise (Awaited)
type DeepAwaited<T> = T extends Promise<infer U> ? DeepAwaited<U> : T;
type Unwrapped = DeepAwaited<Promise<Promise<string>>>; // string

// 3. Extract Array / Tuple Element
type ArrayElement<T> = T extends (infer E)[] ? E : never;
type Num = ArrayElement<number[]>; // number

// 4. Extract First Argument of Function
type FirstArgument<T> = T extends (arg1: infer A, ...rest: any[]) => any ? A : never;
type FnArg = FirstArgument<(token: string, retries: number) => void>; // string
```

---

## 13 Mapped Types and Key Remapping

### Modifiers (`+` and `-`)
```ts
type Mutable<T> = {
  -readonly [K in keyof T]: T[K]; // Removes readonly
};

type Concrete<T> = {
  [K in keyof T]-?: T[K];          // Removes optional (?)
};
```

### Key Remapping via `as`
```ts
// 1. Generate Getter Methods
type CreateGetters<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K];
};

interface Account {
  id: string;
  balance: number;
}
type AccountGetters = CreateGetters<Account>;
// { getId: () => string; getBalance: () => number; }

// 2. Filter Properties by Type (Filtering with 'never')
type StringPropertiesOnly<T> = {
  [K in keyof T as T[K] extends string ? K : never]: T[K];
};

type Filtered = StringPropertiesOnly<{ id: number; name: string; email: string; active: boolean }>;
// { name: string; email: string; }
```

---

## 14 Template Literal Types and String Manipulation

### Template Literal Combinations
```ts
type HttpMethod = "GET" | "POST" | "PUT" | "DELETE";
type Endpoint = "/users" | "/orders";
type ApiRoute = `${HttpMethod} ${Endpoint}`;
// "GET /users" | "GET /orders" | "POST /users" | "POST /orders" ...
```

### Built-in String Modifiers
- `Uppercase<S>`
- `Lowercase<S>`
- `Capitalize<S>`
- `Uncapitalize<S>`

### Deep Object Path Extraction (`Path<T>`)
```ts
type Path<T> = T extends object
  ? { [K in keyof T]: `${string & K}` | `${string & K}.${Path<T[K]>}` }[keyof T]
  : never;

type AppConfig = {
  database: {
    host: string;
    port: number;
    ssl: { enabled: boolean };
  };
};

type ConfigPaths = Path<AppConfig>;
// "database" | "database.host" | "database.port" | "database.ssl" | "database.ssl.enabled"
```

---

## 15 Built-in Utility Types Exhaustive Guide

| Utility | Definition | Practical Usage Example |
| :--- | :--- | :--- |
| `Partial<T>` | All properties optional | `type UpdateUserDto = Partial<User>` |
| `Required<T>` | All properties mandatory | `type StrictConfig = Required<Config>` |
| `Readonly<T>` | All properties immutable | `type FrozenState = Readonly<State>` |
| `Record<K, T>` | Key-value dictionary | `type Cache = Record<string, CachedItem>` |
| `Pick<T, K>` | Selects specific keys | `type UserSummary = Pick<User, "id" \| "name">` |
| `Omit<T, K>` | Excludes specific keys | `type PublicUser = Omit<User, "passwordHash">` |
| `Exclude<T, U>` | Excludes types from union | `type NonAdmin = Exclude<Role, "superadmin">` |
| `Extract<T, U>` | Filters shared types from union | `type Action = Extract<Event, "click" \| "hover">` |
| `NonNullable<T>` | Removes `null` & `undefined` | `type ValidStr = NonNullable<string \| null>` |
| `Parameters<T>` | Extracts parameter tuple | `type Args = Parameters<typeof fetch>` |
| `ReturnType<T>` | Extracts return type | `type Output = ReturnType<typeof parse>` |
| `ConstructorParameters<T>` | Extracts constructor args | `type CtrArgs = ConstructorParameters<typeof Date>` |
| `InstanceType<T>` | Extracts class instance | `type Client = InstanceType<typeof ApiClient>` |
| `Awaited<T>` | Recursively unwraps Promise | `type Data = Awaited<ReturnType<typeof fetchUser>>` |

---

## 16 Classes, OOP, and Parameter Properties

### Parameter Properties & Encapsulation
```ts
abstract class BaseRepository<TEntity> {
  abstract tableName: string;
  abstract findById(id: string): Promise<TEntity | null>;

  protected logQuery(query: string) {
    console.log(`[${this.tableName}] Executing: ${query}`);
  }
}

class UserRepository extends BaseRepository<{ id: string; name: string }> {
  public override tableName = "users";

  // Parameter properties automatically assign constructor args to fields
  constructor(
    private readonly dbPool: object,
    public readonly replicaRegion: string = "us-east-1",
    protected maxRetries: number = 3,
  ) {
    super();
  }

  public async findById(id: string) {
    this.logQuery(`SELECT * FROM ${this.tableName} WHERE id = ${id}`);
    return { id, name: "Sample User" };
  }

  // True ECMAScript private field (#): Inaccessible at runtime outside class
  #secretCryptoKey = "AES_KEY_256";
}
```

---

## 17 Enums vs Const Objects

### Tradeoff Comparison
| Feature | `enum` | `const enum` | `as const` Object (Recommended) |
| :--- | :--- | :--- | :--- |
| **JS Code Emission** | IIFE with reverse mapping | Inlined literals | Standard plain JS object |
| **Tree-Shaking** | Poor (Bundlers cannot eliminate) | Fragile with `isolatedModules` | **Perfect** |
| **Type Safety** | Numeric enums accept arbitrary numbers | Inlined literals | Exact union literal safety |

### Recommended Best Practice: `as const` Object
```ts
export const HttpStatus = {
  OK: 200,
  BAD_REQUEST: 400,
  UNAUTHORIZED: 401,
  NOT_FOUND: 404,
  INTERNAL_SERVER_ERROR: 500,
} as const;

// Automatically extract type unions
export type HttpStatusCode = (typeof HttpStatus)[keyof typeof HttpStatus]; // 200 | 400 | 401 | 404 | 500
export type HttpStatusKey = keyof typeof HttpStatus;                      // "OK" | "BAD_REQUEST" | ...
```

---

## 18 Modules, Imports/Exports, and Interoperability

### ESM vs CommonJS Patterns
```ts
// 1. Explicit Type-Only Imports (Zero bundle overhead)
import type { Request, Response } from "express";
import { json, type RequestHandler } from "express";

// 2. Re-exporting modules
export type { UserDto } from "./dto/user.dto";
export { UserService } from "./services/user.service";

// 3. Dynamic Imports (Lazy loading)
async function loadAnalytics() {
  const { trackEvent } = await import("./analytics.js");
  trackEvent("PAGE_VIEW");
}
```

### Triple-Slash Directives (`/// <reference />`)
Used in ambient `.d.ts` declaration files to establish compiler dependencies.
```ts
/// <reference types="node" />
/// <reference lib="es2022" />
/// <reference path="./globals.d.ts" />
```

---

## 19 Ambient Declarations, .d.ts, and Module Augmentation

### External Library Augmentation
```ts
// Augmenting Express Request with authenticated user payload
import "express";

declare module "express" {
  export interface Request {
    user?: {
      userId: string;
      roles: string[];
    };
  }
}
```

### Global Environment Augmentation (`globals.d.ts`)
```ts
declare global {
  namespace NodeJS {
    interface ProcessEnv {
      NODE_ENV: "development" | "production" | "test";
      PORT: string;
      DATABASE_URL: string;
      JWT_SECRET: string;
    }
  }

  interface Window {
    __INITIAL_STATE__?: Record<string, unknown>;
  }
}

export {}; // Ensure file is an isolated module
```

---

## 20 Modern TC39 Decorators

Standard Stage 3 Decorators (TS 5.0+, no `experimentalDecorators` needed).

```ts
// Method Decorator for Performance Timing & Logging
function LogExecution<This, Args extends any[], Return>(
  target: (this: This, ...args: Args) => Return,
  context: ClassMethodDecoratorContext<This, (this: This, ...args: Args) => Return>
) {
  const methodName = String(context.name);

  return function (this: This, ...args: Args): Return {
    console.log(`[START] ${methodName} called with:`, args);
    const start = performance.now();
    const result = target.call(this, ...args);
    console.log(`[FINISH] ${methodName} executed in ${(performance.now() - start).toFixed(2)}ms`);
    return result;
  };
}

class PaymentService {
  @LogExecution
  chargeCustomer(amount: number, currency: string) {
    return `Charged ${amount} ${currency}`;
  }
}
```

---

## 21 Runtime Schema Validation (Zod Integration)

TypeScript types are erased at runtime. Use **Zod** for runtime input parsing + automatic static type inference.

```ts
import { z } from "zod";

// 1. Define Zod Runtime Schema
export const CreateUserSchema = z.object({
  id: z.string().uuid(),
  username: z.string().min(3).max(30),
  email: z.string().email(),
  role: z.enum(["admin", "moderator", "user"]).default("user"),
  age: z.number().int().positive().optional(),
});

// 2. Derive Static TypeScript Type automatically
export type CreateUserDto = z.infer<typeof CreateUserSchema>;

// 3. Parse and Validate at API boundary
export function validateCreateUser(payload: unknown): CreateUserDto {
  const result = CreateUserSchema.safeParse(payload);
  if (!result.success) {
    throw new Error(`Validation failed: ${result.error.issues.map((i) => i.message).join(", ")}`);
  }
  return result.data; // Statically typed as CreateUserDto
}
```

---

## 22 Frontend and Fullstack Typing Patterns (React & APIs)

### React Component Props and Hooks Typing
```tsx
import React, { useState, useRef, useReducer, type ReactNode } from "react";

// Component Props with Children & HTML Attributes
interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  variant: "primary" | "secondary" | "danger";
  isLoading?: boolean;
  leftIcon?: ReactNode;
}

export const Button: React.FC<ButtonProps> = ({
  variant,
  isLoading,
  leftIcon,
  children,
  ...rest
}) => {
  return (
    <button className={`btn btn-${variant}`} disabled={isLoading} {...rest}>
      {leftIcon && <span className="icon">{leftIcon}</span>}
      {isLoading ? "Loading..." : children}
    </button>
  );
};

// React Hooks Typing
export function CounterComponent() {
  const [count, setCount] = useState<number>(0);
  const inputRef = useRef<HTMLInputElement>(null);

  // useReducer typing with Discriminated Union
  type Action = { type: "increment" } | { type: "decrement" } | { type: "reset"; payload: number };
  const [state, dispatch] = useReducer((state: number, action: Action) => {
    switch (action.type) {
      case "increment": return state + 1;
      case "decrement": return state - 1;
      case "reset": return action.payload;
    }
  }, 0);

  return <div ref={inputRef}>Count: {state}</div>;
}
```

---

## 23 Testing, Linters, and Monorepos

### Vitest / Jest Unit Testing
```ts
import { describe, it, expect, vi } from "vitest";

function sum(a: number, b: number): number {
  return a + b;
}

describe("sum module", () => {
  it("adds two numbers correctly", () => {
    expect(sum(2, 3)).toBe(5);
  });

  it("handles async operations", async () => {
    const mockFn = vi.fn().mockResolvedValue("data");
    const result = await mockFn();
    expect(result).toBe("data");
  });
});
```

### Modern ESLint & Biome Setup
```bash
# 1. ESLint with TypeScript
npm install -D eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin

# 2. Biome (Fast Rust-based Linter & Formatter alternative)
npm install -D --save-exact @biomejs/biome
npx biome check --write ./src
```

---

## 24 High-Yield Interview Questions and Reality Check

### 1. What is the difference between Structural Typing and Nominal Typing?
> **Answer**: TypeScript uses **Structural Typing** ("duck typing"), where type compatibility is determined entirely by the shape and members of the object, rather than explicit declarations or class inheritance. If object `A` has all the properties expected by type `B`, `A` is assignable to `B`. In contrast, languages like Java, C#, or Rust use Nominal typing, where types must share an explicit declared name or hierarchy.

### 2. What are Excess Property Checks and when do they apply?
> **Answer**: TypeScript applies excess property checks **only when assigning fresh object literals** directly to a target type (e.g., `const u: User = { name: "Aman", age: 25 }`), throwing an error if unknown keys exist. When assigning through an existing variable reference, excess property checks are bypassed due to structural subtyping compatibility.

### 3. What is Type Erasure?
> **Answer**: During the compilation step, `tsc` removes all TypeScript syntax—interfaces, type aliases, generic type arguments, and type annotations—emitting clean JavaScript. Types **do not exist at runtime** and cannot be inspected with `typeof` (which only returns JS primitives like `"object"` or `"string"`). Runtime validation must be done with custom type guards or libraries like Zod.

### 4. What is the difference between `unknown` and `any`?
> **Answer**: `any` disables the type-checker entirely, allowing you to invoke arbitrary methods and access missing properties without safety. `unknown` is the type-safe top type: you can assign any value to it, but the compiler **blocks all operations** until you explicitly narrow it using type guards (`typeof`, `instanceof`, or custom predicates).

### 5. Why is `satisfies` preferred over `: Type` annotations for configuration objects?
> **Answer**: A type annotation (`const config: AppConfig = { ... }`) broadens properties to the wide interface definition, losing specific literal and tuple inference. The `satisfies` keyword (`const config = { ... } satisfies AppConfig`) validates that the object conforms to the contract while **preserving exact literal types**, enabling perfect downstream autocomplete and type safety.

### 6. What does `noUncheckedIndexedAccess: true` accomplish in `tsconfig.json`?
> **Answer**: By default, indexing an array (`arr[i]`) or Record (`map[key]`) returns `T`, assuming the element exists. When `noUncheckedIndexedAccess: true` is enabled, the compiler marks indexed access as `T | undefined`, forcing developers to handle potential out-of-bounds or missing key conditions safely.

---

### Reality Check & Best Practices
- Never use `any` in production code; use `unknown` with a type guard or Zod parser.
- Prefer `as const` objects over TypeScript `enum`s for cleaner tree-shaking and zero runtime emission bugs.
- Enable `strict: true` and `noUncheckedIndexedAccess: true` on every new project.
- Use Discriminated Unions with exhaustive `never` checks for all state machines, API responses, and Redux/action handlers.
- Use `import type` (or `"verbatimModuleSyntax": true`) to prevent accidental runtime imports of type-only files.
