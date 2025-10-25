# 📚 Table of Contents

- [Components](#components)
- [Components lifecycle Hooks](#components-lifecycle-hooks)
- [Dependency Injection](#dependency-injection)
- [Routes](#routes)
- [Signals](#signals)

# Components

Components are the building blocks of an Angular application. They control a portion of the view. It's important to organize your application into components to avoid code repetition and make the application easy to maintain and evolve over time.

## Key aspects of Angular components

- A TypeScript class with behaviors such as handling user input and fetching data from a server
- An HTML template that controls what renders into the DOM
- A CSS selector that defines how the component is used in HTML
- It is possible to create components with Angular CLI
- The selector property in the `@Component` decorator defines the HTML tag used to insert the component into templates.
- Use the `@Input` decorator to pass data from a parent component to a child component.
- Use the `@Output` decorator along with EventEmitter to send data from a child component to a parent component.

Example:

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-my-component',
  templateUrl: './my-component.component.html',
  styleUrls: ['./my-component.component.css']
})
export class MyComponent { }
```

## Signals 

Signals allow you to manage state and react to changes in a more efficient way. It's possible to update and observe data state changes. Using signal allows Angular to optimize render updates.

```typescript
// writable signal
const count = signal(0);
console.log('The count is: ' + count());
count.set(3);
count.update(value => value + 1);

// computed signal (read-only) are both lazily evaluated and memoized
const count: WritableSignal<number> = signal(0);
const doubleCount: Signal<number> = computed(() => count() * 2);
```

### Effects

An effect is an operation that runs whenever one or more signal values change.

```typescript
effect(() => {
  console.log(`The current count is: ${count()}`);
});
```

Avoid using effects for propagation of state changes. Instead, use computed signals to derive state.

Effects are rarely needed in most application code, but may be useful in specific circumstances. Here are some examples of situations where an effect might be a good solution:

- Logging data being displayed and when it changes, either for analytics or as a debugging tool.
- Keeping data in sync with `window.localStorage`.
- Adding custom DOM behavior that can't be expressed with template syntax.

## Best Practices for Creating Components

When designing components in an Angular application, following best practices ensures your app is maintainable, scalable, and easy to understand.

### 1. Single Responsibility Principle

- Each component should have one clear purpose.
- Avoid components that handle multiple unrelated concerns.

### 2. Break Down by UI Elements

- Create components for reusable UI sections (headers, footers, navigation bars, cards, forms, dialogs).
- If a section of a page is visually or functionally distinct, consider making it a component.

### 3. Reusability

- Identify UI or logic used in multiple places and encapsulate them as reusable components.

### 4. Container vs. Presentational Components

- **Container components:** handle data fetching, state management, and business logic.
- **Presentational components:** focus on rendering and receive data via `@Input`, emit events via `@Output`.

### 5. Keep Components Small

- Smaller components are easier to maintain, understand, and test.
- If a component grows too large, split it into smaller components.

### 6. Feature Modules

- Organize related components into feature modules (e.g., `UserModule`, `ProductModule`) for modularity and lazy loading.

### 7. Encapsulation

- Use Angular’s encapsulation features to limit a component’s scope and avoid unintended side effects.

### 8. Avoid Deeply Nested Components

- Too many nested levels can make the app complex.
- Flatten the hierarchy where possible.

# Components lifecycle Hooks

Angular components have lifecycle hooks-special methods you can implement to react to different phases in a component’s existence. Common hooks include:

- **ngOnInit**: Called once after the component is initialized. Used for setup logic.
- **ngOnChanges**: Called when input properties change.
- **ngOnDestroy**: Called just before Angular destroys the component. Used for cleanup.
- **Others**: ngAfterViewInit, ngAfterContentInit, etc.

# Dependency Injection

TODO

# Routes

TODO

# Signals

TODO