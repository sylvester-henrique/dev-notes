# 📚 Table of Contents

- [Object-Oriented Programming (OOP)](#object-oriented-programming-oop)
- [Clean Code](#clean-code)
  - [SOLID Principles](#solid-principles)
- [Clean Architecture](#clean-architecture)
- [Domain Driven Design (DDD)](#domain-driven-design-ddd)
- [Design Patterns](#design-patterns)
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

## Clean Architecture

Clean Architecture is a software design philosophy introduced by Robert C. Martin (Uncle Bob) that emphasizes separation of concerns and independence of frameworks, UI, databases, and external dependencies. The goal is to create systems that are maintainable, testable, and flexible to change.

### Core Principles

**Independence**: The architecture should be independent of:
- **Frameworks**: The architecture doesn't depend on the existence of some library or framework. Frameworks are tools, not constraints.
- **UI**: The UI can change without changing the rest of the system. A web UI could be replaced with a console UI without affecting business rules.
- **Database**: Business rules are not bound to a specific database. You can swap SQL Server for Oracle, MongoDB, or any other database.
- **External Services**: Business rules don't know anything about the outside world (APIs, file systems, etc.).

**Testability**: Business rules can be tested without the UI, database, web server, or any external element.

### The Dependency Rule

The most important rule in Clean Architecture: **source code dependencies must point inward toward higher-level policies**.

Think of the architecture as concentric circles (like a target), where the center contains your core business logic and outer circles contain implementation details:
- **Inner circles** = Business rules (what your application does)
- **Outer circles** = Implementation details (how it does it - UI, database, frameworks)

**The Rule**: Code in inner circles cannot know about or import anything from outer circles. This means:
- ✅ Outer layers CAN depend on inner layers (a Controller can use a Use Case)
- ❌ Inner layers CANNOT depend on outer layers (a Use Case cannot use a Controller)
- ❌ Inner layers CANNOT mention classes, functions, or variables from outer layers

**Why it matters**: Your business logic (the core of your application) stays pure and independent. You can change your database from SQL Server to MongoDB, or your UI from web to mobile, without touching your business rules.

**Practical example**: 
- Your `CalculatePayroll` use case (inner layer) can call an `IEmployeeRepository` interface
- Your `SqlEmployeeRepository` (outer layer) implements that interface
- The use case never knows about SQL, Entity Framework, or any database details
- You can swap the database implementation without changing business logic

**In simple terms**: Your business rules are the king - they don't depend on anyone. Everything else (UI, database, frameworks) are servants that depend on and serve the business rules.

### Architecture Layers

#### 1. Entities (Enterprise Business Rules)
- The innermost layer containing enterprise-wide business rules
- These are the core business objects of the application
- Least likely to change when something external changes
- Can be used across multiple applications in the enterprise

#### 2. Use Cases (Application Business Rules)
- Contains application-specific business rules
- Orchestrates the flow of data to and from entities
- Directs entities to use their enterprise-wide business rules to achieve the goals of the use case
- Changes in this layer should not affect entities
- Should not be affected by changes to UI, database, or frameworks

#### 3. Interface Adapters (Controllers, Presenters, Gateways)
- Converts data from the format most convenient for use cases and entities to the format most convenient for external agents (database, web)
- Contains MVC architecture of a GUI
- Presenters, Views, and Controllers all belong here
- Data is converted from the form most convenient for entities and use cases into the form most convenient for persistence frameworks

#### 4. Frameworks and Drivers (External Layer)
- The outermost layer composed of frameworks and tools
- Database, Web Framework, UI, External APIs
- Generally you don't write much code here, mostly glue code that communicates to the next circle inward
- This layer is where all the details go

### Key Benefits

- **Maintainability**: Changes in one layer don't cascade to other layers
- **Testability**: Business logic can be tested independently of external dependencies
- **Flexibility**: Easy to change UI, database, or frameworks without affecting core business logic
- **Scalability**: Well-organized code is easier to scale and extend
- **Technology Agnostic**: Not locked into specific technologies or frameworks

### Implementation Guidelines

1. **Define clear boundaries** between layers using interfaces
2. **Keep business logic pure** - no framework or infrastructure dependencies in the core
3. **Use dependency injection** to invert dependencies and point them inward
4. **Separate concerns** - each layer has a single, well-defined purpose
5. **Think in terms of use cases** - organize code around what the application does, not around technical details

### Common Patterns in Clean Architecture

- **Repository Pattern**: Abstracts data access, allowing business logic to remain independent of data storage details
- **Dependency Injection**: Allows outer layers to provide implementations that inner layers depend on through abstractions
- **Use Case/Interactor Pattern**: Encapsulates application-specific business rules
- **DTO (Data Transfer Objects)**: Carries data between layers without exposing internal structure

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

## Domain Driven Design (DDD)

Domain-Driven Design (DDD) is a software development philosophy that emphasizes the importance of understanding and modeling the business domain. It is a strategy aimed at improving the quality of software by aligning it more closely with the business needs it serves.

At its core, DDD is about navigating complexity by placing the focus of software development on the 'domain', or the specific business context within which the software operates. It advocates for the use of a 'ubiquitous language', a common language that is shared by both the software developers and the business stakeholders. This language, used in the design and implementation of the software, ensures that everyone involved has a shared understanding of the problem space and solution.

### Core Concepts

#### Ubiquitous Language

A common, rigorous language shared between developers and domain experts. This language should be used in:
- Code (class names, method names, variables)
- Conversations between team members
- Documentation
- User stories and requirements

**Why it matters**: Eliminates translation errors between business terminology and technical implementation. When a business expert says "Order" they mean the same thing as the `Order` class in your code.

#### Bounded Context

A boundary within which a particular domain model is defined and applicable. Different parts of a system may have different models of the same concept.

**Example**: In an e-commerce system:
- **Sales Context**: "Customer" has shopping cart, order history, payment methods
- **Support Context**: "Customer" has support tickets, interaction history, satisfaction ratings
- **Shipping Context**: "Customer" has delivery addresses, shipping preferences

Each context has its own model of "Customer" appropriate to its needs.

#### Entities

Objects that have a distinct identity that runs through time and different states. Two entities with the same attributes are still different if they have different identities.

**Characteristics**:
- Have unique identifiers (ID)
- Mutable - their attributes can change
- Identity matters more than attributes

**Example**: Two customers named "John Smith" are different people (different CustomerID)

#### Value Objects

Objects that represent a descriptive aspect of the domain with no conceptual identity. They are defined by their attributes.

**Characteristics**:
- No unique identifier
- Immutable - cannot be changed after creation
- Defined by their values, not identity
- Can be shared and replaced freely

**Example**: `Address`, `Money`, `DateRange`, `EmailAddress` - two `Money` objects with value $100 are interchangeable

#### Aggregates

A cluster of entities and value objects that are treated as a single unit for data changes. Each aggregate has a root entity (Aggregate Root) that is the only entry point for modifications.

**Rules**:
- External objects can only reference the Aggregate Root
- Aggregate boundaries ensure consistency
- Transactions should not cross aggregate boundaries

**Example**: An `Order` aggregate might contain `OrderLines`, `ShippingAddress`, and `BillingAddress`. You access everything through the `Order` root.

#### Repositories

Mechanisms for encapsulating storage, retrieval, and search behavior which emulates a collection of objects. Provide the illusion of an in-memory collection of all objects of a certain type.

**Purpose**:
- Abstract away data access details
- Provide a collection-like interface
- Work with aggregate roots only

**Example**: `IOrderRepository` with methods like `GetById(orderId)`, `Save(order)`, `FindByCustomer(customerId)`

#### Domain Services

Business logic that doesn't fit naturally within an entity or value object should be extracted into a Domain Service. These are stateless operations that represent important domain concepts but aren't the responsibility of any single object. Domain Services are defined as standalone interfaces or classes in the domain layer.

**Use when**:
- Operation involves multiple entities
- Operation doesn't naturally belong to any single entity
- Operation represents an important domain concept

**Example**: `PaymentProcessingService`, `TaxCalculationService`, `ShippingCostCalculator`

#### Domain Events

Something that happened in the domain that domain experts care about. They represent state changes or important occurrences.

**Characteristics**:
- Named in past tense (OrderPlaced, PaymentReceived, CustomerRegistered)
- Immutable
- Contain data relevant to the event
- Can trigger side effects in other parts of the system

**Example**: `OrderShippedEvent` might trigger sending a notification email and updating inventory

### Benefits of DDD

- **Better Understanding**: Forces deep understanding of the business domain
- **Improved Communication**: Ubiquitous language bridges the gap between technical and business teams
- **Flexibility**: Bounded contexts allow different parts of the system to evolve independently
- **Maintainability**: Domain logic is isolated and well-organized
- **Focus on Core Domain**: Identifies and prioritizes the most valuable parts of the system

### When to Use DDD

**DDD is beneficial when:**
- The domain is complex with rich business rules
- The project will be long-lived and needs to be maintainable
- You have access to domain experts
- The business logic is the core value of the system

**DDD may be overkill when:**
- The domain is simple (CRUD operations)
- The project is short-lived or a prototype
- The application is primarily data-driven with little business logic
- The team is small and the system is straightforward

## Design Patterns

Design patterns are reusable solutions to commonly occurring problems in software design. They represent best practices evolved over time and provide a shared vocabulary for developers to communicate complex ideas efficiently. Design patterns are not finished code that can be directly copied - they are templates for how to solve problems in different situations.

### Why Use Design Patterns?

- **Proven Solutions**: Patterns provide tested and proven development paradigms
- **Reusability**: They can be reused across multiple projects
- **Communication**: They provide a common vocabulary for developers
- **Best Practices**: They encapsulate best practices and expert knowledge
- **Maintainability**: Well-structured code following patterns is easier to maintain and extend

### Categories of Design Patterns

Design patterns are typically divided into three main categories:

#### 1. Creational Patterns

These patterns deal with object creation mechanisms, trying to create objects in a manner suitable to the situation.

**Singleton**
- Ensures a class has only one instance and provides a global point of access to it
- **Use case**: Database connections, logging, configuration managers
- **Example**: A single configuration object shared throughout the application

**Factory Method**
- Defines an interface for creating an object, but lets subclasses decide which class to instantiate
- **Use case**: When you don't know beforehand the exact types of objects your code will work with
- **Example**: A document creator that can create different types of documents (PDF, Word, Excel)

**Abstract Factory**
- Provides an interface for creating families of related objects without specifying their concrete classes
- **Use case**: When your system needs to work with multiple families of related products
- **Example**: UI components that need different implementations for Windows, Mac, and Linux

**Builder**
- Separates the construction of a complex object from its representation
- **Use case**: Creating objects with many optional parameters or complex initialization
- **Example**: Building a complex SQL query or constructing a detailed report with many optional sections

**Prototype**
- Creates new objects by copying existing objects (cloning)
- **Use case**: When object creation is expensive and you want to copy existing instances
- **Example**: Cloning game characters with predefined attributes

#### 2. Structural Patterns

These patterns deal with object composition and typically identify simple ways to realize relationships between entities.

**Adapter**
- Allows incompatible interfaces to work together by wrapping an object with a new interface
- **Use case**: When you want to use an existing class but its interface doesn't match what you need
- **Example**: Making a third-party payment API work with your application's payment interface

**Decorator**
- Adds new functionality to objects dynamically without altering their structure
- **Use case**: When you need to add responsibilities to objects at runtime
- **Example**: Adding scrollbars, borders, or shadows to UI components; adding compression or encryption to data streams

**Facade**
- Provides a simplified interface to a complex subsystem
- **Use case**: When you want to provide a simple interface to a complex library or framework
- **Example**: A unified interface for home automation that hides the complexity of controlling lights, temperature, and security

**Proxy**
- Provides a surrogate or placeholder for another object to control access to it
- **Use case**: Lazy initialization, access control, logging, caching
- **Example**: Virtual proxy for loading large images only when needed; protection proxy for access control

**Composite**
- Composes objects into tree structures to represent part-whole hierarchies
- **Use case**: When you want to treat individual objects and compositions uniformly
- **Example**: File system (files and folders), UI component trees, organizational hierarchies

#### 3. Behavioral Patterns

These patterns are concerned with algorithms and the assignment of responsibilities between objects.

**Strategy**
- Defines a family of algorithms, encapsulates each one, and makes them interchangeable
- **Use case**: When you have multiple algorithms for a task and want to switch between them
- **Example**: Different sorting algorithms, payment methods, compression algorithms

**Observer**
- Defines a one-to-many dependency so that when one object changes state, all dependents are notified
- **Use case**: Event handling systems, implementing distributed event-driven systems
- **Example**: Newsletter subscriptions, stock price monitoring, UI updates when model data changes (MVC pattern)

**Command**
- Encapsulates a request as an object, allowing you to parameterize clients with different requests
- **Use case**: Implementing undo/redo, queuing operations, logging changes
- **Example**: Text editor commands (cut, copy, paste), transaction systems, macro recording

**Template Method**
- Defines the skeleton of an algorithm in a base class, letting subclasses override specific steps
- **Use case**: When you have an algorithm with invariant steps but some steps need customization
- **Example**: Data processing pipeline with hooks for custom parsing and validation

**State**
- Allows an object to alter its behavior when its internal state changes
- **Use case**: When an object's behavior depends on its state and must change at runtime
- **Example**: Document workflow (draft, under review, approved), TCP connection states, vending machine states

**Iterator**
- Provides a way to access elements of a collection sequentially without exposing its underlying representation
- **Use case**: Traversing different types of collections uniformly
- **Example**: Iterating through lists, trees, graphs; database result sets

**Chain of Responsibility**
- Passes requests along a chain of handlers, where each handler decides to process or pass it to the next
- **Use case**: When multiple objects can handle a request and the handler isn't known beforehand
- **Example**: Event bubbling in UI frameworks, logging frameworks with multiple log levels, approval workflows

### When to Use Design Patterns

**Do use patterns when:**
- You recognize a recurring problem that matches a pattern's solution
- You need to communicate design intent clearly to other developers
- You want to ensure code flexibility and maintainability

**Don't use patterns when:**
- The problem is simple and doesn't need the complexity
- You're forcing a pattern where it doesn't fit naturally
- It would over-engineer the solution

### Important Considerations

- **Patterns are not laws**: They're guidelines, not strict rules
- **Context matters**: Choose patterns based on your specific problem and constraints
- **Don't over-pattern**: Not every problem needs a design pattern
- **Learn by doing**: Understanding comes from applying patterns in real projects
- **Combine patterns**: Many real-world solutions use multiple patterns together

