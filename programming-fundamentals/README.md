# 📚 Table of Contents

- [Object-Oriented Programming (OOP)](#object-oriented-programming-oop)
- [Clean Code](#clean-code)
  - [SOLID Principles](#solid-principles)
- [Extreme Programming (XP)](#extreme-programming-xp)

## Object-Oriented Programming (OOP)

Object-Oriented Programming (OOP) is a programming paradigm that organizes code around objects, which represent real-world entities, and their interactions. Objects are created from classes which are blueprints that define what methods and properties the object has. The core concepts of OOP are Encapsulation, Abstraction, Inheritance, and Polymorphism. These principles provide a foundation for creating modular, reusable, and maintainable software.

### Encapsulation

Encapsulation is a mechanism of restricting direct access to some components of an object while allowing controlled access through methods. It bundles the data (fields) and the code (methods) that operates on the data into a single unit (class). The primary aim is to ensure data security and integrity by hiding the internal implementation details from the outside world. The encapsulation is done through:
- **Access modifiers** for properties and methods, such as: `private`, `protected` and `public`
- **Getters and setters**, used to access and modify private and protected attributes safely

### Abstraction

The goal of Abstraction is to hide complex implementation details of a class while exposing only necessary functionalities for clients of this class. This is done through abstract classes and interfaces. 

**Benefits:**
- Reduces complexity
- Improves code maintainability, reusability and flexibility

### Inheritance

Inheritance allows a class (child or subclass) to derive or inherit properties and behaviors from another class (parent or superclass). It enables code reuse and establishes a relationship between classes. 

**Benefits:**
- Promotes code reusability
- Supports polymorphism when subclasses override parent methods
- **Extensibility**: simplifies the addition of new features

### Polymorphism

Is a concept which states that an object can have many forms. It has two types:

**Compile-time polymorphism**: is determined at compile time, achieved through overloading. For example, multiple methods of a class can have the same name, since they have different parameters.

**Run-time polymorphism**: achieved through
- **Method overriding**: when a subclass provides a specific implementation, overriding the superclass method. The code can call the method of the superclass, but at compile time, the method of the subclass is called.
- **Late binding**: objects of a derived class can be treated as objects of a base class in places such as method parameters, collections and arrays.

### Advantages of OOP

- **Modularity**: Code is organized into objects and classes, making it easier to manage.
- **Reusability**: Code can be reused through inheritance and composition.
- **Flexibility**: Polymorphism and abstraction provide flexibility in extending and modifying behavior.
- **Maintainability**: Encapsulation ensures that code changes in one part don't inadvertently affect other parts.

## Clean Code

Clean code refers to writing code that is readable, maintainable, and efficient. It's not just about making code work but making it understandable and easy to modify for yourself and other developers. Clean code adheres to best practices, principles, and conventions that improve quality and reduce technical debt.

### Characteristics of Clean Code

- **Readable**
- **Maintainable**
- **Testable**
- **Efficient**
- **Self-Explanatory**

### Best Practices for Writing Clean Code

- **Use Meaningful Names**: variables, functions, and classes should describe their purpose
- **Keep Functions Small**: functions should do one thing and do it well. If a function is too large, consider breaking it into smaller functions
- **Avoid Magic Numbers and Strings**: use constants or enums instead of hardcoding values.
- **Write DRY Code (Don't Repeat Yourself)**: Avoid duplicating code by abstracting common logic into reusable functions or classes
- **Use Comments Wisely**: comments should explain why, not what the code is doing
- **Adhere to a Style Guide**: follow consistent code formatting and style conventions, for example Microsoft C# conventions
- **Handle Errors**: use exception handling to manage unexpected scenarios without crashing the application

### Principles for Clean Code

- **KISS (Keep It Simple, Stupid)**: write simple and straightforward code to reduce complexity
- **YAGNI (You Aren't Gonna Need It)**: avoid writing code for features you think you'll need in the future. So if you don't need it right now, don't implement it.
- **SOLID Principles** (see [SOLID Principles](#solid-principles) section below)
- **Separation of Concerns**: each module or class should have a clear and specific purpose

### SOLID Principles

SOLID is an acronym for five design principles that help create maintainable, scalable, and robust object-oriented software:

#### Single Responsibility Principle (SRP)

A class should have only one reason to change, meaning it should have high cohesion and handle only one responsibility. Classes must be cohesive in the tasks they perform and should not have too many responsibilities.

**Example:** In a payroll system, an `IRRF` class should only handle income tax calculation logic, while an `INSS` class should only handle social security calculation logic. If there's a change in the INSS business logic, it would only affect the `INSS` class.

#### Open-Closed Principle (OCP)

Software entities (classes, modules, functions) should be open for extension but closed for modification. This means you should be able to add new functionality without changing existing code.

**Example:** Having an `IINSS` interface with a `calculate(double grossSalary)` method allows you to create new implementations (like `INSSAntigo` for the old formula used until February 2020) without modifying the original `INSS` class implementation.

#### Liskov Substitution Principle (LSP)

Objects of a superclass should be replaceable with objects of its subclasses without breaking the application or causing side effects. Child classes should not alter the behavior expected from the parent class.

**Key Point:** When using inheritance, derived classes should extend functionality without changing the core behavior that clients depend on. If a subclass changes the expected behavior of its parent, it violates this principle.

#### Interface Segregation Principle (ISP)

Clients should not be forced to depend on interfaces they don't use. When interfaces have too many responsibilities, they should be segregated into smaller, more specific interfaces.

**Key Point:** Avoid "fat" interfaces with many methods. Instead, create focused interfaces so that implementing classes only need to implement methods they actually use.

#### Dependency Inversion Principle (DIP)

Classes should depend on abstractions (interfaces) rather than concrete implementations. This principle inverts the traditional dependency structure where high-level business logic depends directly on low-level implementation details.

**Why it matters:** When a class depends on a concrete implementation, any change to that implementation forces you to modify the dependent class. By depending on an interface instead, your class becomes decoupled from specific implementations and more flexible to changes.

**Example:** Instead of the `IRRF` class directly depending on the concrete `INSS` class, it should depend on the `IINSS` interface. This way:
- You can switch between different INSS implementations (current formula, old formula, test mocks) without touching the `IRRF` code
- If the internal logic of `INSS` changes, `IRRF` remains unaffected because it only interacts through the interface contract
- The code becomes easier to test, extend, and maintain

**In simple terms:** Depend on "what something does" (interface) rather than "how it does it" (concrete class).

### Benefits of Clean Code

- **Improved Collaboration**: other developers can quickly understand and work on your code
- **Reduced Bugs**: readable and maintainable code minimizes errors
- **Faster Debugging and Refactoring**: a clean code is easier to troubleshoot and update
- **Long-Term Sustainability**: as applications grow, clean code ensures they remain manageable

## Extreme Programming (XP)

Is an agile software development methodology that focuses on short cycles, customer collaboration, responsiveness to changing requirements, simple design and high quality code. 

### XP Practices

- **Pair programming**
- **Test-Driven Development (TDD)**
- **Continuous Integration**: Integrate and test code frequently (several times a day)
- **Refactoring**: Continuously improve the code structure without changing its behavior
- **Simple design**: Only build what's necessary for now. Avoid over engineering
- **Small Releases**: Deliver software frequently, in small, functional releases
- **Collective Code Ownership**: Any developer can change any part of the code at any time.
- **Sustainable Pace**: No overtime or burnout. Work at a steady pace.
- **On-Site Customer**: Have the customer available full-time for questions and feedback
- **Coding Standards**: Consistent code style and practices across the team.

### When is XP Appropriate?

- **Rapidly Changing Requirements**: when the client isn't 100% sure what they want, XP's frequent feedback loops and small iterations help reduce waste.
- **High-Risk or Complex Projects**: when technical risk is high and mistakes are costly, XP's practices like TDD, Continuous Integration (CI) and Pair Programming help catch issues early
- **Projects Needing High Software Quality**: If bugs are unacceptable (e.g. financial, healthcare, safety systems), XP's focus on testing and clean code makes it a strong choice.
- **Customer Availability**: if you have a customer who can participate daily (or almost daily), for questions and feedbacks
- **Short Release Cycles**: XP is ideal when the business needs to deliver small updates frequently (weekly or bi-weekly), rather than long release cycles
