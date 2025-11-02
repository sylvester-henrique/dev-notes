# 📚 Table of Contents

- [Components](#components)
- [Components lifecycle Hooks](#components-lifecycle-hooks)
- [Data Binding](#data-binding)
- [Pipes](#pipes)
- [Modules](#modules)
- [Routes](#routes)
- [Dependency Injection](#dependency-injection)
- [Forms](#forms)

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

# Data Binding

Data binding is one of the core concepts that enables communication between the component class (TypeScript) and its template (HTML). Data binding allows you to display data, respond to user input, and keep your application state synchronized with the UI

## Data Binding Summary Table

| Binding Type       | Syntax                | Direction               | Use Case                |
|--------------------|-----------------------|-------------------------|-------------------------|
| Interpolation      | `{{ value }}`         | Component → Template    | Display properties      |
| Property Binding   | `[prop]="value"`      | Component → Template    | Set DOM properties      |
| Attribute Binding  | `[attr.name]="v"`     | Component → Attribute   | Set HTML attributes     |
| Event Binding      | `(event)="fn()"`      | Template → Component    | Handle user actions     |
| Two-way Binding    | `[(ngModel)]`         | Both                    | Form fields             |

## Best practices

- Use interpolation for text content.
- Use property binding for dynamic element properties.
- Use event binding for handling user actions.
- Use two-way binding for forms and user input.
- Avoid using two-way binding unless necessary—prefer one-way bindings for clarity and maintainability

## Property Binding vs Attribute Binding in Angular

Understanding the distinction between property binding and attribute binding is essential for effective Angular development.

### Property Binding

**Property binding** sets a property on a DOM element, allowing dynamic, interactive behavior managed by the browser.

```html
<!-- Property binding: disables the button dynamically -->
<button [disabled]="isDisabled">Save</button>
```

```typescript
// In your component
isDisabled = true; // Button will be disabled; if false, it will be enabled
```

The `[disabled]="isDisabled"` syntax binds the button's DOM property `disabled` to the component property `isDisabled`. The button will become enabled/disabled dynamically as the value changes.

### Attribute Binding

**Attribute binding** sets a static HTML attribute or custom data attribute on an element, useful for non-standard or metadata attributes.

```html
<!-- Attribute binding: sets a custom data attribute -->
<button [attr.data-tracking-id]="trackingId">Save</button>
```

```typescript
// In your component
trackingId = 'btn-save-123';
```

The `[attr.data-tracking-id]="trackingId"` syntax assigns the `data-tracking-id` attribute to the button element. This is used for metadata, analytics, accessibility, or custom data attributes that are not managed by the browser's DOM API.

| Binding Type     | Example                            | Purpose                                |
|------------------|------------------------------------|----------------------------------------|
| Property Binding | `[disabled]="isDisabled"`          | Dynamic, browser-managed properties    |
| Attribute Binding| `[attr.data-tracking-id]="value"`  | Static/custom HTML attributes/data     |

# Pipes

A pipe is a simple function that takes an input value and returns a transformed value, which is then displayed in the template. Pipes are commonly used for display formatting such as dates, currency, uppercase/lowercase conversion, and more.

## Using Pipes in Templates

```html
<!-- Uppercase pipe -->
<p>{{ username | uppercase }}</p>
<!-- If username is "Sylvester", output: "SYLVESTER" -->

<!-- Date pipe with format -->
<p>{{ birthday | date:'longDate' }}</p>
<!-- If birthday is new Date(2025, 10, 1), output: "November 1, 2025" -->

<!-- Currency pipe with currency code -->
<p>{{ price | currency:'USD' }}</p>
<!-- If price is 99.99, output: "$99.99" -->

<!-- Slice pipe to extract part of a string -->
<p>{{ name | slice:0:4 }}</p>
<!-- If name is "Angular", output: "Angu" -->
```

## Pipes Best Practices

- Use built-in pipes for common formatting needs.
- Keep custom pipes simple and focused on transformation.
- For complex logic or filtering large data sets, prefer handling it in the component instead of a pipe for better performance.

# Modules

Purpose of Modules

- **Organization**: Divide your app into logical units (e.g., features, shared utilities).
- **Encapsulation**: Hide internal implementation details from other parts of your app.
- **Dependency Management**: Declare dependencies (services, other modules).
- **Lazy Loading**: Load parts of your app on demand for better performance.

TODO: talk about when to use module vs standalone components. How would you split a large Angular application into multiple modules?

# Routes

 Routing is the mechanism that enables navigation between different views or components in your single-page application (SPA). The Angular Router **maps URLs to components**, **manages browser history**, and supports advanced features like **lazy loading** and **route guards**.

## Lazy loading

Lazy loading is a technique where modules (and their associated components, services, etc.) are loaded only when the user navigates to a route that requires them, rather than loading everything up front. This improves app performance, especially for large applications.

## Guards

Route guards are services that control whether a user can access or activate a route. They help protect routes based on permissions, authentication, data availability, or other conditions.

Types of Route Guards
- **CanActivate**: Controls if a route can be activated.
- **CanDeactivate**: Controls if a route can be exited.
- **CanLoad**: Controls if a lazy-loaded module can be loaded.
- **Resolve**: Pre-fetches data before activating a route.
- **CanActivateChild**: Controls if child routes can be activated.

# Dependency Injection

Dependency Injection (DI) is a design pattern where dependencies (such as services) are supplied to a class rather than being created by the class itself. Angular implements DI using injectors and the `@Injectable` decorator, allowing you to inject services into components or other services via constructor parameters.

## Services

A service in Angular is a class that encapsulates reusable logic, such as data fetching, business rules, or state management, and is not directly tied to the UI. Services are injected into components or other services. Components, on the other hand, control a section of the UI, manage templates, and handle user interactions.

## Benefits of Using Services & DI

- **Code Reusability**: Share logic across multiple components.
- **Testability**: Easily mock services for unit testing.
- **Loose Coupling**: Components depend on abstractions, not concrete classes.
- **Scalability and Separation of Concerns**: Manage complex logic outside of components.

## Service Lifetime: When Are Instances Created?

- **Root Level:** Created once when the app starts.
- **Component Level:** Created each time the component is instantiated.
- **Module Level:**
  - **Eagerly loaded:** Created when the module is loaded with the app.
  - **Lazy-loaded:** Created when the module is first loaded via navigation.

## Visual Summary

```plaintext
App Root Injector
│
├── Singleton Service (providedIn: 'root')
│
├── Eagerly Loaded Module
│     └── Module Service (singleton across app)
│
└── Lazy-Loaded Module (via Router)
      └── Module Service (singleton within this lazy-loaded module only)
```

## Best Practices

- Use `providedIn: 'root'` for app-wide singletons and shared logic.
- Provide services at the **component level** for isolated, per-instance state.
- Use **lazy-loaded module providers** for features that need isolated state or should not share service instances with the rest of the app.  

# Forms 

Angular provides two powerful approaches for handling forms: **template-driven forms** and **reactive forms**. Each has its own strengths and use cases.

## Template-Driven Forms

- Form logic is primarily defined in the HTML template.
- Suitable for simple forms and quick prototyping.
- Uses Angular directives like `ngModel` for two-way binding.
- Less code in TypeScript, more declarative in HTML.
- Relies on `FormsModule`.
- Validation handled via HTML attributes and directives.

### Example

```typescript
// app.module.ts
import { FormsModule } from '@angular/forms';

@NgModule({
  imports: [FormsModule]
})
export class AppModule {}
```

```html
<form #form="ngForm" (ngSubmit)="onSubmit(form.value)">
  <input name="username" [(ngModel)]="username" required>
  <input name="email" [(ngModel)]="email" email>
  <button type="submit">Submit</button>
</form>
```

## Reactive Forms

- Form logic is defined in the component class using model-driven APIs.
- Suitable for complex, dynamic, or large forms.
- Uses `FormControl`, `FormGroup`, and `FormBuilder`.
- Explicit and programmatic control over form structure and validation.
- Relies on `ReactiveFormsModule`.
- Validation handled in TypeScript with validators.

### Example

```typescript
// app.module.ts
import { ReactiveFormsModule } from '@angular/forms';

@NgModule({
  imports: [ReactiveFormsModule]
})
export class AppModule {}
```

```typescript
// component.ts
import { FormGroup, FormControl, Validators } from '@angular/forms';

form = new FormGroup({
  username: new FormControl('', Validators.required),
  email: new FormControl('', [Validators.required, Validators.email])
});

onSubmit() {
  console.log(this.form.value);
}
```

```html
<form [formGroup]="form" (ngSubmit)="onSubmit()">
  <input formControlName="username">
  <input formControlName="email">
  <button type="submit">Submit</button>
</form>
```

## Comparison Table

| Feature                | Template-Driven         | Reactive                        |
|------------------------|------------------------|----------------------------------|
| Form definition        | In template            | In TypeScript                   |
| Validation             | Template directives    | Validators in TypeScript        |
| Best for               | Simple forms           | Complex/dynamic forms           |
| Module required        | FormsModule            | ReactiveFormsModule             |
| Testability            | Harder                 | Easier                          |
| Dynamic forms          | Difficult              | Easy                            |

## When to Use Which?

- **Use Template-Driven Forms:** For simple, straightforward forms where you want to keep logic in the template.
- **Use Reactive Forms:** For complex forms, dynamic validation, conditional fields, or when you need full programmatic control and easier testing.