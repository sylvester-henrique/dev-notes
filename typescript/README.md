# TypeScript Notes

## Javascript Fundamentals

* **Set:**
   * A Set is a collection of unique values. It is similar to an array but enforces that each element within the set must be unique. If you attempt to add a duplicate value, it will not be added.
   * Use Set when you need to store a collection of unique values and quickly check for the presence of a value.
* **Map:**
   * A Map is a collection of key-value pairs, where the keys can be of any data type (unlike plain JavaScript objects, which typically restrict keys to strings or Symbols). Each key in a Map is unique, and it maps to a specific value
   * Use Map when you need to store key-value pairs where keys can be of any data type, and you require efficient lookups, additions, and deletions based on those keys.
* **Array functions:** map, filter, find, reduce, sort, indexOf some, includes, every

## TypeScript Fundamentals

### Type annotations (primitives, arrays, objects)

#### Primitives

TODO: Talk about symbol, unknown and never

#### any vs unknown in TypeScript:

* **any:** Disables type checking completely - unsafe ⚠️
* **unknown:** Type-safe version of any - requires type checking before use ✅

**Unknown example:**

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

**Problems with any:**
* No type safety - defeats the purpose of TypeScript
* No autocomplete - IntelliSense doesn't work
* Runtime errors - bugs slip through to production
* Type pollution - spreads to other variables
* Refactoring nightmare - can't trust your types

**Benefits of unknown:**

* Type safe - forces you to check types
* No type pollution - doesn't spread
* Explicit narrowing - you must prove the type
* Catches bugs at compile time - not runtime
* Better refactoring - type system has your back

#### Arrays

TODO: talk about readonly array, tuple types, objects with methods, object destructuring with types.

* Interfaces vs Types
* Union & Intersection types
* Async/await with proper typing

### Object Types

TODO: talk about intersection types, Class vs Interface: When to use which

#### Interface

* Defining object shapes for type checking
* Defining API Response Types
* Function Type Definitions

#### Class

* When you need instances with methods
* Encapsulation with Private State
* Inheritance and Polymorphism

### Type vs Interface: When to Use Which?

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

### Utility types (Partial, Pick, Omit, Record, etc.)

#### Record

A way to define dictionaries with typed keys and values. Use cases:

**Defining Objects with Predefined Keys and Consistent Value Types**

```typescript
type UserRoles = 'admin' | 'editor' | 'viewer';
type UserPermissions = Record<UserRoles, boolean>;

const permissions: UserPermissions = {
    admin: true,
    editor: false,
    viewer: true,
};
```

**Creating Type-Safe Dictionaries or Maps.**

```typescript
type ErrorCodes = Record<string, string>;

const errors: ErrorCodes = {
    '404': 'Not Found',
    '500': 'Internal Server Error',
};
```

**Enforcing exhaustive case handling.**

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
```

#### Partial

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

#### Required

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

#### Readonly

```typescript
// Readonly makes all properties readonly
let readonlyTodo: Readonly<Todo> = {
  title: "Fixed",
  description: "Cannot change",
  completed: false
};

// readonlyTodo.completed = true; // ❌ Error
```

### Generics (functions, classes, constraints)

```typescript
function groupByTypeSafe<T, K extends keyof T>(
  array: T[],
  key: K
)
```

**Generics**
* T: The type of objects in the array
* K: A key that must exist in type T

**keyof**
* Ensures K is a valid key of T
* Provides compile-time safety and autocomplete

### Enums and literal types

#### Enums

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

#### Literal Types

```typescript
type CardinalDirection = "North" | "South" | "East" | "West";
type HttpMethod = "GET" | "POST" | "PUT" | "DELETE";
type Age = 18 | 21 | 65;

let facing: CardinalDirection = "North";
let requestMethod: HttpMethod = "POST";
let legalAge: Age = 18;
```

Use **Enums** when you need a runtime representation of your named constants, and when you might need to iterate over the enum values or perform reverse lookups (e.g., getting the name from a numeric value).

Use **Literal Types** (with Unions) when you primarily need compile-time type safety for a fixed set of primitive values and want to avoid generating extra runtime code. They are particularly effective for defining specific string or number values that a variable or function parameter can accept.

### Type guards (typeof, instanceof, custom guards)

#### typeof

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

#### instanceof

Used for class instances and constructor functions. Checks the prototype chain.

**Limitations:**

* Doesn't work with interfaces (they don't exist at runtime)
* Doesn't work with primitive values

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

#### Custom Type Guards (User-Defined Type Predicates)

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

#### The in Operator (Property Existence Check)

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

### Async/await with proper typing

TODO

### TypeScript Class Constructors

TODO: talk about Shorthand Syntax

## Common Interview Problems

* Array manipulation with proper typing
* Object transformations
* API response typing and parsing
* Generic utility function creation
* Error handling with typed errors
* Working with external libraries (proper type imports)

## Resources

[TypeScript Playground](https://www.typescriptlang.org/play)
