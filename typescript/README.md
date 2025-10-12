# 📚 Table of Contents

- [Primitive Types](#types-primitives-and-special-types)
- [Non-Primitive Types](#non-primitive-types)
- [Utility types (Record, Partial, Required, Readonly)](#utility-types-record-partial-required-readonly)
- [Generics](#generics)
- [Enums and literal types](#enums-and-literal-types)
- [Type guards (typeof, instanceof, custom guards)](#type-guards-typeof-instanceof-custom-guards)
- [Async/await with proper typing](#asyncawait-with-proper-typing)
- [TypeScript Classes: Features Beyond JavaScript](#typescript-classes-features-beyond-javascript)
- [Common Interview Problems](#common-interview-problems)
- [Resources](#resources)

# Primitive Types

## Primitives

| Type      | Description                                   | Example                         |
|-----------|-----------------------------------------------|---------------------------------|
| string    | Textual data                                  | `"hello"`, `'world'`            |
| number    | Numeric values (int, float, hex, etc.)        | `42`, `3.14`, `0xFF`            |
| boolean   | True/false values                             | `true`, `false`                 |
| symbol    | Unique, immutable identifier (ES6+)           | `Symbol("desc")`                |
| bigint    | Arbitrarily large integers (ES2020+)          | `9007199254740991n`             |
| undefined | Declared but not assigned a value             | `undefined`                     |
| null      | Intentional absence of any object value       | `null`                          |

## Special types

### any vs unknown in TypeScript:

* **any:** Disables type checking completely - unsafe ⚠️
* **unknown:** Type-safe version of any - requires type checking before use ✅

```typescript
let value: unknown = "hello";

// ❌ CANNOT use without type checking
// value.toUpperCase();        // Error: Object is of type 'unknown'
// value.toFixed(2);           // Error: Object is of type 'unknown'
// value();                    // Error: Object is of type 'unknown'
// value[0];                   // Error: Object is of type 'unknown'

// ❌ CANNOT assign to other types without checking
// let str: string = value;    // Error: Type 'unknown' is not assignable to type 'string'
// let num: number = value;    // Error: Type 'unknown' is not assignable to type 'number'

// ✅ MUST narrow the type first (Type Guards!)
if (typeof value === "string") {
  // Now TypeScript knows it's a string
  console.log(value.toUpperCase()); // OK!
  let str: string = value;          // OK!
}

if (typeof value === "number") {
  console.log(value.toFixed(2));    // OK!
}

// ✅ Type narrowing with instanceof
if (value instanceof Date) {
  console.log(value.toISOString()); // OK!
}

// ✅ Type narrowing with custom type guard
function isString(val: unknown): val is string {
  return typeof val === "string";
}
```

Problems with any:
* No type safety - defeats the purpose of TypeScript
* No autocomplete - IntelliSense doesn't work
* Runtime errors - bugs slip through to production
* Type pollution - spreads to other variables
* Refactoring nightmare - can't trust your types

Benefits of unknown:

* Type safe - forces you to check types
* No type pollution - doesn't spread
* Explicit narrowing - you must prove the type
* Catches bugs at compile time - not runtime
* Better refactoring - type system has your back

### never

- `never` represents **values that never occur**.
- Used for functions that **always throw**, **never return**, or unreachable code.

#### Common Use Cases

#### 1. Functions that always throw

```typescript
function throwError(message: string): never {
  throw new Error(message);
}
```

#### 2. Infinite loops

```typescript
function forever(): never {
  while (true) {}
}
```

#### 3. Exhaustiveness checks in discriminated unions

The `never` type is useful for ensuring all cases of a union are handled. If a new case is added and not handled, TypeScript will give a compile-time error at the `never` assignment.

```typescript
type Status = "loading" | "success" | "error";

function handleStatus(status: Status): string {
  switch (status) {
    case "loading":
      return "Loading...";
    case "success":
      return "Success!";
    case "error":
      return "Error!";
    default:
      // This block should be unreachable if all cases are handled
      const _unreachable: never = status;
      return _unreachable;
  }
}
```

Differences from `void`

- `void` means a function does not return a value (may return `undefined`).
- `never` means a function **never returns at all** (it throws or loops forever).

# Non-Primitive Types

## 1. Object

- The most general non-primitive type.
- Includes arrays, functions, custom objects, classes, etc.
- Can be described with `{}` or the more specific `object` type.

```typescript
let obj: object = { a: 1, b: 2 };
let arr: object = [1, 2, 3]; // Arrays are objects
let fun: object = () => {};  // Functions are objects

// Specific shape
let user: { name: string; age: number } = { name: "Alice", age: 30 };
```

## 2. Array

- Ordered list of elements.
- Annotated as `Type[]` or `Array<Type>`.

```typescript
let numbers: number[] = [1, 2, 3];
let users: Array<{ name: string; age: number }> = [{ name: "Bob", age: 25 }];
```

## 3. Function

- Functions are objects with callable signatures.
- You can annotate them with parameter and return types.

```typescript
let add: (x: number, y: number) => number = (a, b) => a + b;
type Logger = (msg: string) => void;
```

## 4. Class

- Blueprint for creating objects with properties and methods.
- Classes define both type and value (runtime).

```typescript
class Animal {
  constructor(public name: string) {}
  speak(): string {
    return `${this.name} makes a sound`;
  }
}
let pet: Animal = new Animal("Dog");
```

## 5. Tuple

- Fixed-length, ordered array of types.
- Useful for representing sets of related values.

```typescript
let point: [number, number] = [10, 20];
let result: [boolean, string] = [true, "OK"];
```

## 6. Enum

- Named set of numeric or string constants.
- Useful for fixed sets of options.

```typescript
enum Direction { North, South, East, West }
let dir: Direction = Direction.North;
```

## Special Types

## 7. Interface

- Describes the shape of an object.
- Used for contracts and type checking, not for runtime values.

```typescript
interface User {
  id: number;
  username: string;
  email: string;
}
let user: User = { id: 1, username: "alice", email: "alice@example.com" };
```

## 8. Type Alias

- Alternative name for a type (primitive, object, union, intersection, etc.).

```typescript
type Point = { x: number; y: number };
type Status = "loading" | "success" | "error";
```

## 9. Union & Intersection Types

- **Union**: A value can be one of several types.
- **Intersection**: A value must satisfy all types.

```typescript
type Id = string | number; // union
type Employee = Person & { employeeId: number }; // intersection
```

## Other Non-Primitive Types

- **Date**: Built-in object for dates/times.
- **RegExp**: Built-in object for regular expressions.
- **Map/Set**: Key-value and unique collection objects.

```typescript
let date: Date = new Date();
let regex: RegExp = /abc/;
let map: Map<string, number> = new Map();
let set: Set<number> = new Set([1, 2, 3]);
```

## Key Difference

- **Primitive types** are immutable and compared by value.
- **Non-primitive types** are reference types, compared by reference (object identity).

## Summary Table

| Type         | Description                    | Example                      |
|--------------|--------------------------------|------------------------------|
| object       | General object                 | `{}`                         |
| array        | Ordered collection             | `[1, 2, 3]`                  |
| function     | Callable                       | `(x) => x * 2`               |
| class        | Blueprint for objects          | `class Animal {}`            |
| tuple        | Fixed-length array             | `[number, string]`           |
| enum         | Named constants                | `enum Dir {N, S, E, W}`      |
| interface    | Object contract                | `interface User {...}`       |
| type alias   | Type shortcut                  | `type Id = string | number`  |
| union        | One of several types           | `string | number`            |
| intersection | All combined types             | `Person & Employee`          |

## Interface vs Class: When to Use Which?

## Interface

* Defining object shapes for type checking
* Defining API Response Types
* Function Type Definitions

## Class

* When you need instances with methods
* Encapsulation with Private State
* Inheritance and Polymorphism

## Type vs Interface: When to Use Which?

```typescript
// ========== USE TYPE FOR: ==========

// 1. Primitives
type ID = string | number;

// 2. Unions
type Status = "pending" | "approved" | "rejected";

// 3. Tuples
type Point = [number, number];

// 4. Functions
type MathOperation = (a: number, b: number) => number;

// 5. Complex combinations
type Result<T> = { success: true; data: T } | { success: false; error: string };

// ========== USE INTERFACE FOR: ==========

// 1. Object shapes (especially if they might be extended)
interface User {
  id: number;
  name: string;
}

// 2. Class contracts
interface Drawable {
  draw(): void;
}

class Circle implements Drawable {
  draw() {
    console.log("Drawing circle");
  }
}

// 3. Declaration merging (adding properties later)
interface Window {
  customProperty: string;
}

interface Window {
  anotherProperty: number;
}

// Now Window has both properties

// ========== BOTH WORK FOR: ==========

// Objects
type UserType = { name: string };
interface UserInterface { name: string }

// Extending
type ExtendedType = UserType & { age: number };
interface ExtendedInterface extends UserInterface { age: number }
```

# Utility types (Record, Partial, Required, Readonly)

## Record

A way to define dictionaries with typed keys and values. Use cases:

### Defining Objects with Predefined Keys and Consistent Value Types

```typescript
type UserRoles = 'admin' | 'editor' | 'viewer';
type UserPermissions = Record<UserRoles, boolean>;

const permissions: UserPermissions = {
    admin: true,
    editor: false,
    viewer: true,
};
```

### Creating Type-Safe Dictionaries or Maps

```typescript
type ErrorCodes = Record<string, string>;

const errors: ErrorCodes = {
    '404': 'Not Found',
    '500': 'Internal Server Error',
};
```

### Enforcing exhaustive case handling

```typescript
enum ThemeColor {
    Primary = 'primary',
    Secondary = 'secondary',
    Accent = 'accent',
}

type ColorPalette = Record<ThemeColor, string>;

const myPalette: ColorPalette = {
    [ThemeColor.Primary]: '#007bff',
    [ThemeColor.Secondary]: '#6c757d',
    [ThemeColor.Accent]: '#ffc107',
};

// setting '#6c757d' to color
const color = myPalette.secondary;
```

## Partial

```typescript
interface Todo {
  title: string;
  description: string;
  completed: boolean;
}

// Partial makes all properties optional
let partialTodo: Partial<Todo> = {
  title: "Learn TypeScript"
  // description and completed are optional
};
```

## Required

```typescript
// Required makes all properties required
interface OptionalTodo {
  title?: string;
  description?: string;
}

let requiredTodo: Required<OptionalTodo> = {
  title: "Must have",
  description: "Both properties required"
};
```

## Readonly

```typescript
// Readonly makes all properties readonly
let readonlyTodo: Readonly<Todo> = {
  title: "Fixed",
  description: "Cannot change",
  completed: false
};

// readonlyTodo.completed = true; // ❌ Error
```

# Generics

Generics allow you to write flexible, reusable, and type-safe code by making types parameters instead of hardcoded values.

## What Are Generics?

- Generics are like variables for types.
- They enable you to create components (functions, classes, interfaces, etc.) that work with any data type while still enforcing type safety.

## Basic Generic Example

```typescript
function identity<T>(value: T): T {
  return value;
}

const num = identity<number>(42);       // T is number
const str = identity<string>("hello");  // T is string
```

## Generic Types

You can define generic types for objects, arrays, and more.

```typescript
type Box<T> = {
  value: T;
};

const numberBox: Box<number> = { value: 123 };
const stringBox: Box<string> = { value: "abc" };
```

## Generic Interfaces

```typescript
interface Result<T> {
  success: boolean;
  data: T;
}

const userResult: Result<{ name: string }> = {
  success: true,
  data: { name: "Alice" }
};
```

## Generic Classes

```typescript
class Pair<A, B> {
  constructor(public first: A, public second: B) {}
}

const pair = new Pair<string, number>("age", 30);
```

## Generic Constraints

You can restrict what types can be used with a generic using `extends`.

```typescript
function getLength<T extends { length: number }>(item: T): number {
  return item.length;
}

getLength("hello");       // OK
getLength([1, 2, 3]);     // OK
// getLength(123);        // Error: number doesn't have length property
```

You can use multiple generics with constraints

```typescript
// keyof ensures K is a valid key of T
function groupByTypeSafe<T, K extends keyof T>(
  array: T[],
  key: K
)
```

## Default Generic Types

You can provide default types for generics:

```typescript
function wrap<T = string>(value: T): T[] {
  return [value];
}

wrap();          // T is string by default, returns string[]
wrap(10);        // T is number, returns number[]
```

## Why Use Generics?

- Write DRY code: Avoid repeating similar type definitions.
- Reusable: Functions and classes can work with any type.
- Type-safe: Errors are caught at compile time.

# Enums and literal types

## Enums

```typescript
enum Direction {
  Up,    // defaults to 0
  Down,  // defaults to 1
  Left,  // defaults to 2
  Right  // defaults to 3
}

enum Status {
  Success = "SUCCESS",
  Failure = "FAILURE",
  Pending = "PENDING"
}

let currentDirection: Direction = Direction.Up;
let operationStatus: Status = Status.Pending;
```

## Literal Types

```typescript
type CardinalDirection = "North" | "South" | "East" | "West";
type HttpMethod = "GET" | "POST" | "PUT" | "DELETE";
type Age = 18 | 21 | 65;

let facing: CardinalDirection = "North";
let requestMethod: HttpMethod = "POST";
let legalAge: Age = 18;
```

- Use **Enums** when you need a runtime representation of your named constants, and when you might need to iterate over the enum values or perform reverse lookups (e.g., getting the name from a numeric value).

- Use **Literal Types** (with Unions) when you primarily need compile-time type safety for a fixed set of primitive values and want to avoid generating extra runtime code. They are particularly effective for defining specific string or number values that a variable or function parameter can accept.

| Feature                  | Literal Types                  | Enums                         |
|--------------------------|--------------------------------|-------------------------------|
| Compile-time only        | ✅ Yes                         | ❌ No (runtime object)        |
| Runtime mapping/lookup   | ❌ No                          | ✅ Yes                        |
| Iteration over values    | ❌ Manual                     | ✅ Built-in                   |
| Discriminated unions     | ✅ Yes                        | ❌ Not idiomatic              |
| Lightweight API status   | ✅ Yes                        | ❌ Overkill                   |
| Named constants          | ❌ No                         | ✅ Yes                        |
| Reverse lookup           | ❌ No                         | ✅ Numeric enums only         |

# Type guards (typeof, instanceof, custom guards)

## typeof

Used to check the type of primitive types (string, number, boolean, symbol, bigint, undefined, function). Ex:

```typescript
function processValue(value: string | number): string {
  // Type guard using typeof
  if (typeof value === "string") {
    // TypeScript knows 'value' is a string here
    return value.toUpperCase();
  } else {
    // TypeScript knows 'value' is a number here
    return value.toFixed(2);
  }
}

console.log(processValue("hello")); // "HELLO"
console.log(processValue(42.567));  // "42.57"

// ========== More Complex Example ==========

function formatInput(input: string | number | boolean | undefined): string {
  if (typeof input === "string") {
    return `String: ${input}`;
  }
  
  if (typeof input === "number") {
    return `Number: ${input}`;
  }
  
  if (typeof input === "boolean") {
    return `Boolean: ${input}`;
  }
  
  // typeof undefined === "undefined"
  return "No value provided";
}

// ========== Common Pitfall ==========

function checkArray(value: any) {
  // ❌ WRONG: typeof [] === "object"
  if (typeof value === "array") {
    // This will never execute!
  }
  
  // ✅ CORRECT: Use Array.isArray()
  if (Array.isArray(value)) {
    console.log("It's an array!");
  }
}

// ========== typeof with functions ==========

function executeIfFunction(value: string | (() => void)): void {
  if (typeof value === "function") {
    value(); // TypeScript knows it's a function
  } else {
    console.log(value); // TypeScript knows it's a string
  }
}
```

## instanceof

Used for class instances and constructor functions. Checks the prototype chain.

Limitations:

- Doesn't work with interfaces (they don't exist at runtime)
- Doesn't work with primitive values

**Examples:**

```typescript
class Dog {
  bark() {
    console.log("Woof!");
  }
}

class Cat {
  meow() {
    console.log("Meow!");
  }
}

function makeSound(animal: Dog | Cat): void {
  // instanceof checks if animal is an instance of Dog
  if (animal instanceof Dog) {
    animal.bark(); // TypeScript knows it's a Dog
  } else {
    animal.meow(); // TypeScript knows it's a Cat
  }
}

const myDog = new Dog();
const myCat = new Cat();

makeSound(myDog); // "Woof!"
makeSound(myCat); // "Meow!"

// ========== With Built-in Classes ==========

function processInput(value: Date | string): string {
  if (value instanceof Date) {
    return value.toISOString();
  } else {
    return value.toUpperCase();
  }
}

console.log(processInput(new Date()));
console.log(processInput("hello"));

// ========== Error Handling ==========

function handleError(error: unknown): string {
  if (error instanceof Error) {
    return `Error: ${error.message}`;
  }
  
  if (typeof error === "string") {
    return `Error: ${error}`;
  }
  
  return "Unknown error occurred";
}

try {
  throw new Error("Something went wrong");
} catch (e) {
  console.log(handleError(e));
}
```

## Custom Type Guards (User-Defined Type Predicates)

Used to create your own type-checking functions using `is` keyword. Ex:

```typescript
interface User {
  id: number;
  name: string;
  email: string;
}

interface Admin {
  id: number;
  name: string;
  email: string;
  permissions: string[];
}

// Type predicate function
function isAdmin(user: User | Admin): user is Admin {
  return 'permissions' in user;
}

function greetUser(user: User | Admin): string {
  if (isAdmin(user)) {
    // TypeScript knows 'user' is Admin here
    return `Hello Admin ${user.name}, you have ${user.permissions.length} permissions`;
  } else {
    // TypeScript knows 'user' is User here
    return `Hello ${user.name}`;
  }
}
```

## The in Operator (Property Existence Check)

Check if property exists in an object. Ex:

```typescript
interface Bird {
  fly: () => void;
  layEggs: () => void;
}

interface Fish {
  swim: () => void;
  layEggs: () => void;
}

function move(animal: Bird | Fish): void {
  // Check if 'fly' property exists
  if ("fly" in animal) {
    // TypeScript knows it's a Bird
    animal.fly();
  } else {
    // TypeScript knows it's a Fish
    animal.swim();
  }
}

// ========== With Optional Properties ==========

interface Config {
  debug?: boolean;
  timeout?: number;
  apiKey: string;
}

function initializeConfig(config: Config): void {
  if ("debug" in config && config.debug) {
    console.log("Debug mode enabled");
  }
  
  if ("timeout" in config) {
    console.log(`Timeout: ${config.timeout}ms`);
  }
}
```

# TypeScript Classes: Features Beyond JavaScript

TypeScript enhances class constructors with features not present in JavaScript:

### 1. Type Annotations

TypeScript allows you to annotate constructor parameters and class properties with types.

```typescript
class User {
  name: string;
  age: number;

  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }
}
```

### 2. Parameter Properties (Access Modifiers in Constructor)

TypeScript supports access modifiers (`public`, `private`, `protected`, `readonly`) directly in constructor parameters, automatically declaring and initializing the class properties.

```typescript
class User {
  constructor(
    public name: string,
    private age: number,
    readonly isAdmin: boolean
  ) {
    // Properties are automatically created and initialized
  }
}
```

### 3. Compile-Time Type Checking

TypeScript checks types and property access at compile-time.

```typescript
const u = new User("Alice", "not-a-number", true); // Error: age must be a number
console.log(u.age); // Error: age is private
```

### 4. Optional and Default Parameters

TypeScript supports optional parameters and default values with type checking.

```typescript
class User {
  constructor(public name: string, public age: number = 18, public email?: string) {}
}
```

### 5. Generics in Classes and Constructors

TypeScript enables generic classes for type-safe patterns.

```typescript
class Box<T> {
  constructor(public value: T) {}
}

const box = new Box<number>(42); // value is number
```

### 6. No Runtime Impact

All TypeScript class features are erased at compile time. The resulting JavaScript is standard class syntax, with no types or access modifiers.

# Common Interview Problems

- Array manipulation with proper typing
- Object transformations
- API response typing and parsing
- Generic utility function creation
- Error handling with typed errors

# Resources

- [TypeScript Playground](https://www.typescriptlang.org/play)
