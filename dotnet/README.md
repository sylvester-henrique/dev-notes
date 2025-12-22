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
- [Web API versioning](#web-api-versioning)
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

Health checks are endpoints that provide information about the health status of an application and its dependencies. They are essential for monitoring and ensuring system reliability, especially in distributed systems and microservices architectures.

### Why use health checks?

- Monitoring: allows monitoring tools to track application health and detect issues early
- Load balancing: load balancers can route traffic away from unhealthy instances
- Orchestration: container orchestrators like Kubernetes use health checks to restart or replace unhealthy containers
- Dependencies validation: verify that critical dependencies (databases, APIs, external services) are available and responding

### Types of health checks

**Liveness Probe**
- **Purpose**: Determines if the application is running and should continue to run
- **When it fails**: The orchestrator (like Kubernetes) should kill and restart the container/application
- **What it checks**: Basic application responsiveness - "Is the app alive or deadlocked?"
- **Example scenarios**: 
  - Application has deadlocked and can't process requests
  - Application has entered an unrecoverable state
  - Memory leak has made the application unresponsive
- **Best practice**: Keep checks simple and fast. Don't check dependencies - just verify the application itself is responsive

**Readiness Probe**
- **Purpose**: Determines if the application is ready to accept and process requests
- **When it fails**: The orchestrator should stop routing traffic to the instance but keep it running. Traffic resumes once the check passes again
- **What it checks**: Application and its critical dependencies - "Can the app handle requests right now?"
- **Example scenarios**:
  - Database connection is temporarily unavailable
  - Required external API is down
  - Application is warming up caches
  - Application is under heavy load and needs to temporarily stop accepting new requests
- **Best practice**: Check all critical dependencies needed to serve requests. The instance can recover without restart

**Startup Probe**
- **Purpose**: Determines if the application has completed its initialization and is ready to be tested by liveness/readiness probes
- **When it fails**: After a configured timeout, the orchestrator will kill and restart the container
- **What it checks**: Whether the application has finished starting up - "Has the app finished loading?"
- **Example scenarios**:
  - Large applications that need 30+ seconds to start
  - Applications that load large datasets or models at startup
  - Applications with complex initialization routines
- **Best practice**: Use for slow-starting applications. While startup probe is running, liveness and readiness probes are disabled. Once it succeeds, liveness/readiness probes take over

### Implementation in .NET Core

```csharp
// Program.cs or Startup.cs
public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("self", () => HealthCheckResult.Healthy())
        .AddSqlServer(connectionString, name: "database")
        .AddUrlGroup(new Uri("https://api.example.com"), name: "external-api")
        .AddCheck<CustomHealthCheck>("custom-check");
    
    services.AddHealthChecksUI().AddInMemoryStorage();
}

public void Configure(IApplicationBuilder app)
{
    app.UseRouting();
    
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHealthChecks("/health");
        endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions
        {
            Predicate = check => check.Tags.Contains("ready")
        });
        endpoints.MapHealthChecks("/health/live", new HealthCheckOptions
        {
            Predicate = _ => false // Only basic liveness
        });
        
        // Detailed health check with JSON response
        endpoints.MapHealthChecks("/health/details", new HealthCheckOptions
        {
            ResponseWriter = async (context, report) =>
            {
                context.Response.ContentType = "application/json";
                var result = JsonSerializer.Serialize(new
                {
                    status = report.Status.ToString(),
                    checks = report.Entries.Select(e => new
                    {
                        name = e.Key,
                        status = e.Value.Status.ToString(),
                        description = e.Value.Description,
                        duration = e.Value.Duration.TotalMilliseconds
                    })
                });
                await context.Response.WriteAsync(result);
            }
        });
        
        endpoints.MapHealthChecksUI();
    });
}
```

### Custom health check

```csharp
public class CustomHealthCheck : IHealthCheck
{
    private readonly IMyService _myService;
    
    public CustomHealthCheck(IMyService myService)
    {
        _myService = myService;
    }
    
    public async Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default)
    {
        try
        {
            var isHealthy = await _myService.IsAvailableAsync(cancellationToken);
            
            if (isHealthy)
            {
                return HealthCheckResult.Healthy("Service is available");
            }
            
            return HealthCheckResult.Degraded("Service is slow");
        }
        catch (Exception ex)
        {
            return HealthCheckResult.Unhealthy("Service is unavailable", ex);
        }
    }
}
```

## IQuerable vs IEnumerable

- Use IEnumerable for in-memory collections.
- Use IQueryable for querying external data sources like databases—because the query can be optimized and run remotely.

## How do you enable CORS in a .NET Core API project?

## Web API versioning

API versioning allows you to evolve your API over time while maintaining backward compatibility for existing clients. .NET Core provides multiple strategies for implementing API versioning.

### Why version your API?

- **Backward compatibility**: Allow old clients to continue working while introducing new features
- **Gradual migration**: Give clients time to migrate to new versions
- **Breaking changes**: Introduce breaking changes without disrupting existing users
- **Multiple clients**: Support different client versions simultaneously

### Versioning Strategies

**1. URL Path Versioning** (Most Common)
```csharp
// api/v1/products
// api/v2/products
[Route("api/v{version:apiVersion}/[controller]")]
[ApiVersion("1.0")]
[ApiController]
public class ProductsV1Controller : ControllerBase
{
    [HttpGet]
    public IActionResult Get() => Ok("Version 1.0");
}

[Route("api/v{version:apiVersion}/[controller]")]
[ApiVersion("2.0")]
[ApiController]
public class ProductsV2Controller : ControllerBase
{
    [HttpGet]
    public IActionResult Get() => Ok("Version 2.0");
}
```

**2. Query String Versioning**
```csharp
// api/products?api-version=1.0
// api/products?api-version=2.0
[Route("api/[controller]")]
[ApiVersion("1.0")]
[ApiVersion("2.0")]
[ApiController]
public class ProductsController : ControllerBase
{
    [HttpGet]
    [MapToApiVersion("1.0")]
    public IActionResult GetV1() => Ok("Version 1.0");

    [HttpGet]
    [MapToApiVersion("2.0")]
    public IActionResult GetV2() => Ok("Version 2.0");
}
```

**3. Header Versioning**
```csharp
// Header: api-version: 1.0
// Header: api-version: 2.0
[Route("api/[controller]")]
[ApiVersion("1.0")]
[ApiVersion("2.0")]
[ApiController]
public class ProductsController : ControllerBase
{
    [HttpGet]
    [MapToApiVersion("1.0")]
    public IActionResult GetV1() => Ok("Version 1.0");

    [HttpGet]
    [MapToApiVersion("2.0")]
    public IActionResult GetV2() => Ok("Version 2.0");
}
```

**4. Media Type (Accept Header) Versioning**
```csharp
// Accept: application/json; version=1.0
// Accept: application/json; version=2.0
[Route("api/[controller]")]
[ApiVersion("1.0")]
[ApiVersion("2.0")]
[ApiController]
public class ProductsController : ControllerBase
{
    [HttpGet]
    [MapToApiVersion("1.0")]
    public IActionResult GetV1() => Ok("Version 1.0");

    [HttpGet]
    [MapToApiVersion("2.0")]
    public IActionResult GetV2() => Ok("Version 2.0");
}
```

### Best Practices

- **Use URL path versioning** for public APIs - it's the most visible and discoverable
- **Start with v1**, not v0 or v1.0.0 (keep it simple)
- **Don't over-version** - only create a new version for breaking changes
- **Document deprecation** - clearly communicate when a version will be sunset
- **Support at least 2 versions** simultaneously to give clients time to migrate
- **Version at the API level**, not individual endpoints (unless necessary)
- **Use ApiVersion attribute** on controllers to be explicit about supported versions
- **Set a default version** for clients that don't specify one

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
