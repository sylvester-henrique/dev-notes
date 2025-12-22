# 📚 Table of Contents

- [.NET Core and .NET Framework](#net-core-and-net-framework)
- [Main advantages of .NET Core](#main-advantages-of-net-core)
- [.NET Core runtime components](#net-core-runtime-components)
- [Program.cs and Startup.cs files](#programcs-and-startupcs-files)
- [Middleware pipeline](#middleware-pipeline)
- [Dependency Injection (DI)](#dependency-injection-di)
- [Kestrel web server](#kestrel-web-server)
- [Health check](#health-check)
- [IQuerable vs IEnumerable](#iquerable-vs-ienumerable)
- [.NET Core Aspects](#net-core-aspects)
- [How do you enable CORS in a .NET Core API project?](#how-do-you-enable-cors-in-a-net-core-api-project)
- [How do you handle versioning in a Web API?](#how-do-you-handle-versioning-in-a-web-api)
- [How can you improve the performance of a .NET Core application?](#how-can-you-improve-the-performance-of-a-net-core-application)
- [What is response caching, and how do you implement it in .NET Core?](#what-is-response-caching-and-how-do-you-implement-it-in-net-core)
- [What is garbage collection, and how does it work in .NET Core?](#what-is-garbage-collection-and-how-does-it-work-in-net-core)
- [How do you implement authentication and authorization in .NET Core?](#how-do-you-implement-authentication-and-authorization-in-net-core)
- [When to Use Task.Run in .NET](#when-to-use-taskrun-in-net)

## .NET Core and .NET Framework

.NET core is a cross-platform, open-source framework for building applications. It differs from .NET framework in several ways:

- It is cross platform, runs on Windows, Linux and macOS, while .NET framework is Windows-only. This flexibility allow developers to create applications that run on multiple operating systems without needing significant changes
- It is open source, which means it has a large community contributing to its development and improvement
- Has better performance and scalability, making it suitable for modern cloud-based applications

## Main advantages of .NET Core

- Cross-platform support for Windows, macOS, and Linux
- High performance and scalability for modern applications
- Microservices support, enabling the creation of modular, independent deployable components
- Unified programming model for web, desktop, cloud, IoT, and mobile applications
- Active development and community support through open-source contributions.
- Supported by a big company which is Microsoft, giving great credibility that the platform will be supported and evolved in the future.

## .NET Core runtime components

- The Common Language Runtime (CLR): Manages memory, handles exceptions, and executes code.
- A Just-In-Time (JIT) compiler: Compiles intermediate language (IL) code into machine code
- Libraries and APIs: Provide functionality for tasks like file I/O, networking, and collections

## Program.cs and Startup.cs files

- Program.cs: Contains the Main method, the entry point of the application. It builds and configures the application's host. For example, it reads the configurations in the appsettings.json file, configures the log providers, initializes the dependency injection container
- Startup.cs: Configures services and middleware for the application. It typically contains two methods:
    - ConfigureServices: Registers services for Dependency Injection
    - Configure: Defines the middleware pipeline for handling HTTP requests

- Starting from .NET 6 all of this can be done in the Program.cs file, which can do the host creation, service registrations and middleware pipeline configuration.

## Middleware pipeline

Middleware is code that is executed within a request or response pipeline in an ASP.NET Core application. When to use: when you need to execute an operation on every request or/and every response. Key points

- Request processing: logic added to the pipeline, to inspect, modify or handle HTTP requests. Example: (i) an authentication middleware to check if the user is authenticated; (ii) error handling logic, (iii) any modification necessary to the request.
- Response processing: logic added to the response pipeline, to modify the response before it is sent back to the client. Example: adding custom header to the response, such as cache headers, (ii) compress response data before sending it to the client.
- Chaining: the middleware components are executed in the order they are registered in the pipeline, providing a way to interrupt the pipeline at any point.
- Built-in middleware: middleware provided by the framework, to handle common tasks, such as routing, authentication, serving static files and logging. A typical order is: exception handler, routing, authentication, authorization.
- Custom middleware: it is possible to create your own middleware for specific needs, for error handling  or any request modification

## Dependency Injection (DI)

- Dependency Injection (DI) is a design pattern used to achieve Inversion of Control (IoC). This principle says that objects should receive their dependencies from an external source rather than creating them internally, this improves modularity and testability.
    - Testability: this way it's possible to inject mocked classes, to make isolated unit tests.
    - Separation of concerns: this allows components to focus on single responsibility, without worrying about dependency creation, instead receiving them from an external source, making the code cleaner.
    - Flexibility and extensibility: it makes it easy to change the implementation of a dependency without altering the consumer. So, the dependency can be extended or replaced without affecting other parts of the program.
    - Reusability: since dependencies are injected, they can be used across different parts of the system, causing reusability, without needing to duplicate the code.
    - Maintainability: it reduces the coupling between dependency and consumer, making the code easier to maintain. Changes made in one class have less chance of affecting other classes.

### Dependency Injection (DI) lifetimes

- Singleton: a single instance is created once, is shared throughout the application and lives during the application lifetime. When to use: when there is a service that is expensive to create, thread safe and needs to be accessed consistently across the application. Examples: caching, logging and configuration.
- Scoped: an instance is created for each HTTP request. It maintains state during the HTTP request and is disposed at the end of it. Here, each scope or logical operation gets its own instance, preventing interference between different contexts. When to use: when you have resources that need to be shared within a specific context. Examples: to handle database transactions or work with user-specific data.
- Transient: a new instance is created every time it is requested. When to use: for lightweight stateless services that don't maintain any internal state or shared resources. Example: services that perform simple calculations or provide general utility functions.

It is crucial to choose the appropriate lifetime for a service, in order to manage resources efficiently, to minimize memory usage.

## Kestrel web server

The default web server for ASP.NET Core to process HTTP requests. Key points:

- High performance: design to handle large numbers of simultaneous connections with low overhead. 
- Cross-platform: works on Windows, Linux, and macOS
- Security: Supports HTTP but does not have advanced security features, like request filtering. So is often used with a reverse proxy like Nginx, Apache or IIS for public-facing applications

When to use

- for microservices or internal APIs that are not exposed directly to the public.
- for developing and testing.
- behind a proxy server like Nginx, Apache and IIS

## Health check

TODO

## IQuerable vs IEnumerable

- Use IEnumerable for in-memory collections.
- Use IQueryable for querying external data sources like databases—because the query can be optimized and run remotely.

## .NET Core Aspects

TODO

## How do you enable CORS in a .NET Core API project?

## How do you handle versioning in a Web API?

## How can you improve the performance of a .NET Core application?

## What is response caching, and how do you implement it in .NET Core?

## What is garbage collection, and how does it work in .NET Core?

## How do you implement authentication and authorization in .NET Core?

## What is the difference between cookie-based authentication and JWT in .NET Core?

## How do you secure sensitive information in configuration files (e.g., appsettings.json)?

## What is the difference between framework-dependent and self-contained deployments?

# When to Use Task.Run in .NET

`Task.Run` in .NET is used to offload work to a background thread from the thread pool.

## When to use

### 1. CPU-Bound Work in UI Applications
```csharp
// WPF, WinForms, MAUI, etc. 
private async void Button_Click(object sender, EventArgs e)
{
    var result = await Task.Run(() => PerformHeavyCalculation());
    labelResult.Text = result.ToString();
}
```
This prevents blocking the UI thread and keeps your application responsive.

### 2. CPU-Bound Work in ASP. NET Core (Rarely)
```csharp
// Only if you have genuinely CPU-intensive work
public async Task<IActionResult> GenerateReport()
{
    var report = await Task.Run(() => GenerateComplexReport());
    return Ok(report);
}
```
**Caution**: This is controversial and often not recommended because it just moves the thread blocking from the request thread to a thread pool thread.  

### 3. Making Synchronous Code Asynchronous
```csharp
// Legacy library with only sync methods
public async Task<Data> GetDataAsync()
{
    return await Task.Run(() => legacyLibrary.GetDataSync());
}
```
Useful when wrapping old synchronous APIs for use in async contexts.

### 4. Parallel Processing
```csharp
var tasks = items.Select(item => Task.Run(() => ProcessItem(item)));
await Task.WhenAll(tasks);
```

## When NOT to use

### 1. I/O-Bound Operations
```csharp
// ❌ BAD - Wastes a thread
await Task.Run(async () => await httpClient.GetAsync(url));

// ✅ GOOD - Just await directly
await httpClient.GetAsync(url);
```

### 2. Already Asynchronous Methods
```csharp
// ❌ BAD - Unnecessary wrapper
await Task.Run(async () => await SomeAsyncMethod());

// ✅ GOOD
await SomeAsyncMethod();
```
