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
- [CORS (Cross-Origin Resource Sharing)](#cors-cross-origin-resource-sharing)
- [Web API versioning](#web-api-versioning)
- [How can you improve the performance of a .NET Core application?](#how-can-you-improve-the-performance-of-a-net-core-application)
- [Response caching](#response-caching)
- [Authentication and authorization](#authentication-and-authorization)
- [Securing sensitive information in configuration files](#securing-sensitive-information-in-configuration-files)
- [Framework-dependent and self-contained deployments](#framework-dependent-and-self-contained-deployments)
- [Garbage collection](#garbage-collection)
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

`IEnumerable<T>` and `IQueryable<T>` are both used for querying collections in .NET, but they work very differently and are suited for different scenarios.

### Key Differences

| Feature | IEnumerable<T> | IQueryable<T> |
|---------|----------------|---------------|
| **Namespace** | System.Collections.Generic | System.Linq |
| **Execution** | Client-side (in-memory) | Server-side (remote) |
| **Query Provider** | LINQ to Objects | LINQ to SQL/EF/etc |
| **Best For** | In-memory collections | Database queries |
| **Filtering** | After data is loaded | Before data is loaded |
| **Expression Trees** | No | Yes |
| **Inherits From** | - | IEnumerable<T> |

### How They Work

**IEnumerable<T>**
- Executes queries **in-memory** using LINQ to Objects
- All data is loaded into memory first, then filtering/sorting happens
- Query logic is represented as **delegates** (Func<T>)
- Best for small datasets already in memory

**IQueryable<T>**
- Executes queries **at the data source** (database, web service, etc.)
- Only loads the filtered/sorted results into memory
- Query logic is represented as **expression trees** that can be translated to SQL, REST calls, etc.
- Best for large datasets stored remotely

### Practical Example: Database Query

```csharp
// IEnumerable - BAD for database queries
public IEnumerable<Product> GetExpensiveProducts_Bad()
{
    IEnumerable<Product> products = dbContext.Products; // Loads ALL products into memory
    return products.Where(p => p.Price > 100); // Filtering happens in-memory
}
// SQL Generated: SELECT * FROM Products
// Problem: All records are loaded, then filtered in memory!

// IQueryable - GOOD for database queries
public IQueryable<Product> GetExpensiveProducts_Good()
{
    IQueryable<Product> products = dbContext.Products; // No data loaded yet
    return products.Where(p => p.Price > 100); // Filter is part of the query
}
// SQL Generated: SELECT * FROM Products WHERE Price > 100
// Benefit: Only matching records are loaded!
```

### Deferred Execution

Both `IEnumerable` and `IQueryable` use **deferred execution** - the query doesn't execute until you iterate over it.

```csharp
// Query is defined but not executed
IQueryable<Product> query = dbContext.Products.Where(p => p.Price > 100);

// Nothing happens yet
query = query.OrderBy(p => p.Name);

// NOW the query executes (database is hit)
var results = query.ToList(); // or foreach, Count(), First(), etc.
```

### Common Pitfalls

**1. Breaking the query with AsEnumerable()**
```csharp
// ❌ BAD - Loads all data then filters
var result = dbContext.Products
    .AsEnumerable() // Breaks IQueryable chain!
    .Where(p => p.Price > 100)
    .ToList();

// ✅ GOOD - Filter happens in database
var result = dbContext.Products
    .Where(p => p.Price > 100)
    .ToList();
```

**2. Using non-translatable methods**
```csharp
// ❌ BAD - Method can't be translated to SQL
var result = dbContext.Products
    .Where(p => MyCustomMethod(p.Name)) // Exception!
    .ToList();

// ✅ GOOD - Load data first, then use custom method
var result = dbContext.Products
    .ToList() // Execute query
    .Where(p => MyCustomMethod(p.Name)) // Filter in memory
    .ToList();
```

**3. Multiple enumeration of IQueryable**
```csharp
// ❌ BAD - Query executes multiple times
IQueryable<Product> query = dbContext.Products.Where(p => p.Price > 100);
int count = query.Count(); // Database hit #1
var list = query.ToList(); // Database hit #2

// ✅ GOOD - Execute once and materialize
var list = dbContext.Products
    .Where(p => p.Price > 100)
    .ToList(); // Database hit once
int count = list.Count; // In-memory count
```

### Summary

- **IEnumerable**: Use for in-memory collections. Filtering happens client-side after data is loaded.
- **IQueryable**: Use for external data sources (databases). Filtering happens server-side before data is loaded.
- **Performance**: IQueryable is crucial for database queries to avoid loading unnecessary data.
- **Rule of thumb**: Keep queries as IQueryable as long as possible, materialize with ToList() only when needed.

## CORS (Cross-Origin Resource Sharing)

CORS (Cross-Origin Resource Sharing) is a security feature that allows or restricts web applications running at one origin to access resources from a different origin. By default, browsers block cross-origin requests for security reasons.

### Why CORS is needed?

- **Browser security**: Browsers enforce the Same-Origin Policy, which prevents JavaScript from making requests to a different domain than the one serving the page
- **API consumption**: Modern applications often have frontend (React, Angular, Vue) hosted on one domain and API on another
- **Microservices**: Different services might be hosted on different domains or ports
- **Third-party integrations**: Allow trusted external applications to consume your API

### CORS concepts

- **Origin**: Combination of protocol (http/https), domain, and port. `https://example.com:3000` is different from `https://example.com:8080`
- **Preflight request**: Browser sends an OPTIONS request before the actual request to check if the server allows the cross-origin request
- **CORS headers**: Server responds with headers indicating which origins, methods, and headers are allowed

### Enabling CORS in .NET Core

**1. Basic Setup - Allow All Origins (Development Only)**
```csharp
// Program.cs
var builder = WebApplication.CreateBuilder(args);

// Add CORS services
builder.Services.AddCors(options =>
{
    options.AddDefaultPolicy(policy =>
    {
        policy.AllowAnyOrigin()
              .AllowAnyMethod()
              .AllowAnyHeader();
    });
});

builder.Services.AddControllers();

var app = builder.Build();

// Enable CORS middleware - must be before UseAuthorization
app.UseCors();

app.UseAuthorization();
app.MapControllers();
app.Run();
```

**2. Named Policy - Recommended for Production**
```csharp
// Program.cs
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddCors(options =>
{
    options.AddPolicy("MyAllowedOrigins", policy =>
    {
        policy.WithOrigins(
                "https://example.com",
                "https://www.example.com",
                "https://app.example.com"
            )
            .AllowAnyMethod()
            .AllowAnyHeader()
            .AllowCredentials(); // If you need cookies/auth headers
    });
});

builder.Services.AddControllers();

var app = builder.Build();

// Use the named policy
app.UseCors("MyAllowedOrigins");

app.UseAuthorization();
app.MapControllers();
app.Run();
```

**3. Advanced Configuration**
```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("Advanced", policy =>
    {
        policy.WithOrigins("https://example.com")
              .WithMethods("GET", "POST", "PUT", "DELETE")
              .WithHeaders("Content-Type", "Authorization", "X-Custom-Header")
              .WithExposedHeaders("X-Total-Count", "X-Pagination")
              .SetPreflightMaxAge(TimeSpan.FromMinutes(10))
              .AllowCredentials()
              .SetIsOriginAllowed(origin => 
              {
                  // Custom logic to validate origin
                  return origin.EndsWith(".example.com") || 
                         origin == "https://localhost:3000";
              });
    });
});
```

### Important CORS Headers

- **Access-Control-Allow-Origin**: Specifies which origins can access the resource
- **Access-Control-Allow-Methods**: Specifies which HTTP methods are allowed
- **Access-Control-Allow-Headers**: Specifies which headers can be used
- **Access-Control-Allow-Credentials**: Indicates whether cookies/auth can be sent
- **Access-Control-Expose-Headers**: Headers that the browser should expose to the client
- **Access-Control-Max-Age**: How long preflight results can be cached

### Best Practices

- **Never use AllowAnyOrigin() in production** - always specify allowed origins
- **Use HTTPS origins** for production environments
- **Be specific with methods and headers** - only allow what's necessary
- **Consider environment-specific configurations** - different origins for dev/staging/prod
- **Use SetPreflightMaxAge()** to reduce preflight requests


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

## Response caching

Response caching stores HTTP responses to serve subsequent identical requests directly from cache instead of re-executing the request pipeline. This significantly improves performance and reduces server load.

### Benefits of Response Caching

- **Reduced server load**: Fewer requests reach the application logic
- **Faster response times**: Cached responses are served immediately
- **Lower bandwidth usage**: Content is served from cache (client or server)
- **Better scalability**: Server can handle more concurrent users
- **Reduced database calls**: Prevents repeated queries for the same data

### Types of Caching in .NET Core

**1. Response Caching (HTTP Caching)**
- Uses HTTP cache headers to control caching behavior
- Can be cached on client (browser), proxy servers, or server
- Follows HTTP caching standards

**2. In-Memory Caching**
- Stores data in server memory
- Fast but not shared across multiple server instances
- Data is lost on application restart

**3. Distributed Caching**
- Shared cache across multiple servers (Redis, SQL Server, NCache)
- Survives application restarts
- Suitable for scaled-out applications

### Response Caching Implementation

**1. Basic Setup**

```csharp
// Program.cs
var builder = WebApplication.CreateBuilder(args);

// Add response caching service
builder.Services.AddResponseCaching();

builder.Services.AddControllers();

var app = builder.Build();

// Add response caching middleware - must be early in the pipeline
app.UseResponseCaching();

// Important: Place before controllers
app.UseAuthorization();
app.MapControllers();
app.Run();
```

**2. Using ResponseCache Attribute**

```csharp
[ApiController]
[Route("api/[controller]")]
public class ProductsController : ControllerBase
{
    // Cache for 60 seconds
    [HttpGet]
    [ResponseCache(Duration = 60)]
    public IActionResult GetAll()
    {
        var products = GetProducts(); // Expensive operation
        return Ok(products);
    }

    // Cache for 5 minutes with specific location
    [HttpGet("{id}")]
    [ResponseCache(Duration = 300, Location = ResponseCacheLocation.Client)]
    public IActionResult Get(int id)
    {
        var product = GetProduct(id);
        return Ok(product);
    }

    // Don't cache this endpoint
    [HttpPost]
    [ResponseCache(NoStore = true, Location = ResponseCacheLocation.None)]
    public IActionResult Create(Product product)
    {
        // Create logic
        return Ok(product);
    }

    // Cache and vary by query parameters
    [HttpGet("search")]
    [ResponseCache(Duration = 120, VaryByQueryKeys = new[] { "category", "price" })]
    public IActionResult Search(string category, decimal? price)
    {
        var results = SearchProducts(category, price);
        return Ok(results);
    }
}
```

**3. Custom Cache Key Logic**

```csharp
// Custom middleware for advanced caching logic
public class CustomResponseCachingMiddleware
{
    private readonly RequestDelegate _next;

    public CustomResponseCachingMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        // Add custom cache key based on user role
        var userRole = context.User.FindFirst("role")?.Value;
        context.Response.Headers["Vary"] = "Role";
        
        await _next(context);
    }
}
```

### Important HTTP Cache Headers

- **Cache-Control**: Primary directive for caching behavior
  - `public`: Can be cached by any cache
  - `private`: Only cached by client (browser)
  - `no-cache`: Must revalidate with server before using cached copy
  - `no-store`: Never cache the response
  - `max-age=X`: Cache for X seconds
- **ETag**: Entity tag for conditional requests (validation)
- **Vary**: Specifies which request headers affect the cache key
- **Expires**: Legacy header for cache expiration (prefer Cache-Control)
- **Last-Modified**: When the resource was last changed

### When NOT to Cache

```csharp
// User-specific data
[ResponseCache(NoStore = true, Location = ResponseCacheLocation.None)]
public IActionResult GetUserProfile() => Ok(currentUser);

// Frequently changing data
[ResponseCache(NoStore = true)]
public IActionResult GetStockPrices() => Ok(prices);

// Sensitive data
[ResponseCache(NoStore = true, Location = ResponseCacheLocation.None)]
public IActionResult GetBankingInfo() => Ok(bankData);

// POST/PUT/DELETE operations
[HttpPost]
[ResponseCache(NoStore = true)]
public IActionResult CreateOrder(Order order) => Ok();
```

### Best Practices

- **Cache GET requests only** - POST, PUT, DELETE should not be cached
- **Use appropriate durations** - balance freshness vs performance
- **Consider user authentication** - don't cache user-specific data publicly
- **Set appropriate Cache-Control** - use `private` for user-specific, `public` for shared
- **Monitor cache hit ratio** - track effectiveness of caching strategy
- **Combine with distributed cache** - for application-level caching needs
- **Test cache behavior** - verify headers and caching with browser dev tools
- **Consider CDN integration** - for static assets and public APIs

### Distributed Caching (Bonus)

For application-level caching across servers:

```csharp
// Program.cs - Redis distributed cache
builder.Services.AddStackExchangeRedisCache(options =>
{
    options.Configuration = "localhost:6379";
    options.InstanceName = "MyApp_";
});

// Controller - Use distributed cache
public class ProductsController : ControllerBase
{
    private readonly IDistributedCache _cache;
    
    public ProductsController(IDistributedCache cache)
    {
        _cache = cache;
    }
    
    [HttpGet("{id}")]
    public async Task<IActionResult> Get(int id)
    {
        var cacheKey = $"product_{id}";
        var cachedData = await _cache.GetStringAsync(cacheKey);
        
        if (cachedData != null)
        {
            return Ok(JsonSerializer.Deserialize<Product>(cachedData));
        }
        
        var product = await GetProductFromDatabase(id);
        
        await _cache.SetStringAsync(
            cacheKey,
            JsonSerializer.Serialize(product),
            new DistributedCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = TimeSpan.FromMinutes(10)
            }
        );
        
        return Ok(product);
    }
}
```

## Authentication and authorization

Authentication and authorization are critical security mechanisms in .NET Core applications. Authentication verifies who the user is, while authorization determines what the user can do.

### Key Concepts

**Authentication**: The process of verifying the identity of a user or service
- Confirms "who you are"
- Uses credentials like username/password, tokens, certificates, or biometrics
- Results in a ClaimsPrincipal representing the authenticated user

**Authorization**: The process of determining what an authenticated user can access
- Confirms "what you can do"
- Checks permissions, roles, policies, or claims
- Happens after authentication

### Authentication Types in .NET Core

**1. Cookie Authentication**
- Session-based authentication
- Server stores session data
- Best for traditional web applications (MVC, Razor Pages)
- Credentials are stored in encrypted cookies

**2. JWT (JSON Web Token) Authentication**
- Token-based authentication
- Stateless - server doesn't store session data
- Best for APIs, SPAs, and mobile applications
- Tokens contain encoded claims and are self-contained

**3. OAuth 2.0 / OpenID Connect**
- Industry-standard protocols for authorization
- Delegates authentication to external providers (Google, Microsoft, Facebook)
- OpenID Connect adds identity layer on top of OAuth 2.0

**4. Certificate Authentication**
- Uses client certificates for authentication
- Common in enterprise and B2B scenarios
- Provides strong mutual authentication

### Implementing Cookie Authentication

```csharp
// Program.cs
var builder = WebApplication.CreateBuilder(args);

// Add authentication services
builder.Services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.LoginPath = "/Account/Login";
        options.LogoutPath = "/Account/Logout";
        options.AccessDeniedPath = "/Account/AccessDenied";
        options.ExpireTimeSpan = TimeSpan.FromMinutes(60);
        options.SlidingExpiration = true; // Renew cookie on activity
        options.Cookie.HttpOnly = true; // Prevent JavaScript access
        options.Cookie.SecurePolicy = CookieSecurePolicy.Always; // HTTPS only
        options.Cookie.SameSite = SameSiteMode.Strict; // CSRF protection
    });

builder.Services.AddAuthorization();
builder.Services.AddControllersWithViews();

var app = builder.Build();

app.UseAuthentication(); // Must be before UseAuthorization
app.UseAuthorization();

app.MapControllers();
app.Run();
```

```csharp
// AccountController.cs
[ApiController]
[Route("[controller]")]
public class AccountController : ControllerBase
{
    [HttpPost("login")]
    public async Task<IActionResult> Login([FromBody] LoginModel model)
    {
        // Validate credentials (check against database)
        if (!ValidateCredentials(model.Username, model.Password))
        {
            return Unauthorized("Invalid credentials");
        }

        // Create claims
        var claims = new List<Claim>
        {
            new Claim(ClaimTypes.Name, model.Username),
            new Claim(ClaimTypes.Email, "user@example.com"),
            new Claim(ClaimTypes.Role, "Admin"),
            new Claim("CustomClaim", "CustomValue")
        };

        // Create identity
        var claimsIdentity = new ClaimsIdentity(
            claims, 
            CookieAuthenticationDefaults.AuthenticationScheme);

        // Create authentication properties
        var authProperties = new AuthenticationProperties
        {
            IsPersistent = model.RememberMe,
            ExpiresUtc = DateTimeOffset.UtcNow.AddMinutes(60)
        };

        // Sign in the user
        await HttpContext.SignInAsync(
            CookieAuthenticationDefaults.AuthenticationScheme,
            new ClaimsPrincipal(claimsIdentity),
            authProperties);

        return Ok("Login successful");
    }

    [HttpPost("logout")]
    public async Task<IActionResult> Logout()
    {
        await HttpContext.SignOutAsync(CookieAuthenticationDefaults.AuthenticationScheme);
        return Ok("Logged out");
    }
}
```

### Implementing JWT Authentication

```csharp
// Program.cs
var builder = WebApplication.CreateBuilder(args);

// Add JWT authentication
builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = true,
            ValidateAudience = true,
            ValidateLifetime = true,
            ValidateIssuerSigningKey = true,
            ValidIssuer = builder.Configuration["Jwt:Issuer"],
            ValidAudience = builder.Configuration["Jwt:Audience"],
            IssuerSigningKey = new SymmetricSecurityKey(
                Encoding.UTF8.GetBytes(builder.Configuration["Jwt:Key"]))
        };
    });

builder.Services.AddAuthorization();
builder.Services.AddControllers();

var app = builder.Build();

app.UseAuthentication();
app.UseAuthorization();

app.MapControllers();
app.Run();
```

```json
// appsettings.json
{
  "Jwt": {
    "Key": "YourSuperSecretKeyThatIsAtLeast32CharactersLong!",
    "Issuer": "https://yourapi.com",
    "Audience": "https://yourapi.com",
    "ExpiryMinutes": 60
  }
}
```

```csharp
// TokenService.cs
public class TokenService
{
    private readonly IConfiguration _configuration;

    public TokenService(IConfiguration configuration)
    {
        _configuration = configuration;
    }

    public string GenerateToken(string username, string[] roles)
    {
        var securityKey = new SymmetricSecurityKey(
            Encoding.UTF8.GetBytes(_configuration["Jwt:Key"]));
        var credentials = new SigningCredentials(
            securityKey, 
            SecurityAlgorithms.HmacSha256);

        var claims = new List<Claim>
        {
            new Claim(ClaimTypes.Name, username),
            new Claim(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString())
        };

        // Add roles as claims
        foreach (var role in roles)
        {
            claims.Add(new Claim(ClaimTypes.Role, role));
        }

        var token = new JwtSecurityToken(
            issuer: _configuration["Jwt:Issuer"],
            audience: _configuration["Jwt:Audience"],
            claims: claims,
            expires: DateTime.Now.AddMinutes(
                Convert.ToDouble(_configuration["Jwt:ExpiryMinutes"])),
            signingCredentials: credentials
        );

        return new JwtSecurityTokenHandler().WriteToken(token);
    }
}
```

```csharp
// AuthController.cs
[ApiController]
[Route("api/[controller]")]
public class AuthController : ControllerBase
{
    private readonly TokenService _tokenService;

    public AuthController(TokenService tokenService)
    {
        _tokenService = tokenService;
    }

    [HttpPost("login")]
    public IActionResult Login([FromBody] LoginModel model)
    {
        // Validate credentials
        if (!ValidateCredentials(model.Username, model.Password))
        {
            return Unauthorized("Invalid credentials");
        }

        // Get user roles from database
        var roles = new[] { "Admin", "User" };

        // Generate token
        var token = _tokenService.GenerateToken(model.Username, roles);

        return Ok(new { Token = token });
    }
}

// Client usage: Add to request header
// Authorization: Bearer <token>
```

### Authorization Techniques

**1. Role-Based Authorization**
```csharp
// Simple role check
[Authorize(Roles = "Admin")]
[HttpGet("admin-only")]
public IActionResult AdminOnly()
{
    return Ok("Admin access granted");
}

// Multiple roles (OR logic)
[Authorize(Roles = "Admin,Manager")]
[HttpGet("admin-or-manager")]
public IActionResult AdminOrManager()
{
    return Ok("Access granted");
}

// Multiple role attributes (AND logic)
[Authorize(Roles = "Admin")]
[Authorize(Roles = "Manager")]
[HttpGet("admin-and-manager")]
public IActionResult AdminAndManager()
{
    return Ok("Access granted");
}
```

**2. Claims-Based Authorization**
```csharp
// Program.cs - Configure claims-based policy
builder.Services.AddAuthorization(options =>
{
    options.AddPolicy("EmployeeOnly", policy =>
        policy.RequireClaim("EmployeeNumber"));

    options.AddPolicy("SeniorEmployee", policy =>
        policy.RequireClaim("YearsOfService", "5", "10", "15"));
});

// Controller
[Authorize(Policy = "EmployeeOnly")]
[HttpGet("employee-data")]
public IActionResult GetEmployeeData()
{
    return Ok("Employee data");
}
```

**3. Policy-Based Authorization (Most Flexible)**
```csharp
// Custom requirement
public class MinimumAgeRequirement : IAuthorizationRequirement
{
    public int MinimumAge { get; }

    public MinimumAgeRequirement(int minimumAge)
    {
        MinimumAge = minimumAge;
    }
}

// Custom handler
public class MinimumAgeHandler : AuthorizationHandler<MinimumAgeRequirement>
{
    protected override Task HandleRequirementAsync(
        AuthorizationHandlerContext context,
        MinimumAgeRequirement requirement)
    {
        var dateOfBirthClaim = context.User.FindFirst(c => c.Type == "DateOfBirth");

        if (dateOfBirthClaim == null)
        {
            return Task.CompletedTask;
        }

        var dateOfBirth = Convert.ToDateTime(dateOfBirthClaim.Value);
        var age = DateTime.Today.Year - dateOfBirth.Year;

        if (dateOfBirth > DateTime.Today.AddYears(-age))
        {
            age--;
        }

        if (age >= requirement.MinimumAge)
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }
}

// Program.cs - Register policy and handler
builder.Services.AddAuthorization(options =>
{
    options.AddPolicy("AtLeast21", policy =>
        policy.Requirements.Add(new MinimumAgeRequirement(21)));
});

builder.Services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();

// Controller
[Authorize(Policy = "AtLeast21")]
[HttpGet("adult-content")]
public IActionResult GetAdultContent()
{
    return Ok("Adult content");
}
```

**4. Resource-Based Authorization**
```csharp
// Authorization handler for resource
public class DocumentAuthorizationHandler :
    AuthorizationHandler<SameAuthorRequirement, Document>
{
    protected override Task HandleRequirementAsync(
        AuthorizationHandlerContext context,
        SameAuthorRequirement requirement,
        Document resource)
    {
        if (context.User.Identity?.Name == resource.Author)
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }
}

public class SameAuthorRequirement : IAuthorizationRequirement { }

// Controller - Check authorization imperatively
[ApiController]
[Route("api/[controller]")]
public class DocumentsController : ControllerBase
{
    private readonly IAuthorizationService _authorizationService;

    public DocumentsController(IAuthorizationService authorizationService)
    {
        _authorizationService = authorizationService;
    }

    [HttpGet("{id}")]
    public async Task<IActionResult> GetDocument(int id)
    {
        var document = await GetDocumentFromDatabase(id);

        var authorizationResult = await _authorizationService
            .AuthorizeAsync(User, document, "SameAuthorPolicy");

        if (!authorizationResult.Succeeded)
        {
            return Forbid();
        }

        return Ok(document);
    }
}
```

### Best Practices

**Authentication:**
- Use HTTPS always for authentication endpoints
- Store passwords using strong hashing (bcrypt, Argon2, or PBKDF2)
- Never store plain text passwords
- Implement account lockout after failed login attempts
- Use secure cookie settings: HttpOnly, Secure, SameSite
- For JWT: Keep secrets in secure configuration (Azure Key Vault, AWS Secrets Manager)
- Use short token expiration times with refresh tokens
- Validate all JWT claims (issuer, audience, expiration)

**Authorization:**
- Follow principle of least privilege
- Use policy-based authorization for complex scenarios
- Combine authorization techniques when needed
- Test authorization logic thoroughly
- Log authorization failures for security monitoring
- Use resource-based authorization for fine-grained control
- Don't rely solely on client-side authorization checks

**Common Authentication Flow:**
1. User submits credentials
2. Server validates credentials
3. Server generates authentication token/cookie
4. Client stores token/cookie
5. Client includes token/cookie in subsequent requests
6. Server validates token/cookie
7. Server checks authorization for requested resource
8. Server returns response or 401/403 status

### Difference between cookie-based authentication and JWT

Cookie-based and JWT authentication are two fundamentally different approaches to maintaining user authentication state in web applications. Each has distinct characteristics, advantages, and use cases.

### Key Differences

| Feature | Cookie-Based Authentication | JWT Authentication |
|---------|----------------------------|---------------------|
| **Storage Location** | Server (session store) | Client (local storage, memory) |
| **State** | Stateful (server maintains session) | Stateless (token contains all info) |
| **Transport** | Automatically sent by browser | Manually added to headers |
| **Format** | Session ID (opaque identifier) | Encoded JSON with claims |
| **Size** | Small cookie (~100 bytes) | Larger token (500-1000 bytes) |
| **Scalability** | Requires session sharing across servers | Easily scales horizontally |
| **Revocation** | Easy (delete server session) | Difficult (requires token blacklist) |
| **Best For** | Traditional web apps (MVC, Razor) | APIs, SPAs, mobile apps |
| **Cross-Domain** | Limited (same domain) | Works across domains |
| **Expiration** | Server-controlled | Token-controlled |
| **Security Storage** | HttpOnly cookies (safer) | Vulnerable if in localStorage |

### Cookie-Based Authentication Deep Dive

**How It Works:**
1. User logs in with credentials
2. Server validates credentials
3. Server creates session and stores user data
4. Server sends session ID in encrypted cookie
5. Browser automatically includes cookie in subsequent requests
6. Server looks up session using session ID
7. Server retrieves user information from session store

**Advantages:**
- **Automatic handling**: Browser manages cookies automatically
- **More secure**: HttpOnly cookies can't be accessed by JavaScript (XSS protection)
- **Easy revocation**: Delete session on server to immediately invalidate
- **Smaller payload**: Only session ID is transmitted
- **Server control**: Full control over session lifetime and data
- **Built-in CSRF protection**: Can use SameSite cookie attribute

**Disadvantages:**
- **Stateful**: Requires server-side session storage (memory, Redis, database)
- **Scalability challenges**: Sessions must be shared across multiple servers
- **Cross-domain limitations**: Cookies don't work well across different domains
- **Not ideal for APIs**: Requires special handling for non-browser clients
- **Memory overhead**: Server must maintain all active sessions

**When to Use Cookie Authentication:**
- Traditional server-rendered web applications (MVC, Razor Pages)
- Applications where all pages are on the same domain
- When you need immediate session revocation
- When browser is the primary client
- Enterprise intranet applications
- When you want built-in CSRF protection

### JWT Authentication Deep Dive

**How It Works:**
1. User logs in with credentials
2. Server validates credentials
3. Server generates JWT containing user claims
4. Server sends JWT to client
5. Client stores token (localStorage, sessionStorage, memory)
6. Client includes token in Authorization header for each request
7. Server validates token signature and expiration
8. Server extracts claims from token (no database lookup needed)

**JWT Structure:**
```
Header.Payload.Signature

// Decoded example:
{
  "alg": "HS256",
  "typ": "JWT"
}
.
{
  "sub": "user123",
  "name": "John Doe",
  "role": "Admin",
  "exp": 1735824000
}
.
SIGNATURE
```

**Advantages:**
- **Stateless**: No server-side session storage needed
- **Scalability**: Easy to scale horizontally (no session sharing)
- **Cross-domain**: Works across different domains and services
- **Microservices friendly**: Token can be validated by any service
- **Mobile/SPA friendly**: Perfect for modern client applications
- **Decentralized**: Services can validate tokens independently
- **Rich payload**: Contains user claims without database lookup

**Disadvantages:**
- **Difficult revocation**: Can't invalidate token before expiration (must use blacklist)
- **Token size**: Larger than cookies (affects bandwidth)
- **XSS vulnerability**: If stored in localStorage, vulnerable to XSS attacks
- **No automatic handling**: Must manually add to requests
- **Token bloat**: Adding too many claims increases size
- **Cannot update claims**: Must issue new token to change user info

**When to Use JWT Authentication:**
- RESTful APIs consumed by multiple client types
- Single Page Applications (React, Angular, Vue)
- Mobile applications
- Microservices architectures
- Cross-domain authentication
- When scaling horizontally across multiple servers
- When you don't want server-side session management

## Securing sensitive information in configuration files

Storing sensitive information like connection strings, API keys, passwords, and certificates directly in configuration files poses a significant security risk. .NET Core provides several secure approaches to manage sensitive data.

### The Problem with appsettings.json

**Never commit sensitive data to source control:**
```json
// ❌ BAD - Sensitive data in appsettings.json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=myserver;Database=mydb;User Id=admin;Password=P@ssw0rd123;"
  },
  "ApiKeys": {
    "OpenAI": "sk-abc123xyz789",
    "Stripe": "sk_live_51ABC123"
  },
  "JwtSecret": "MySecretKey12345"
}
```

**Problems:**
- Visible in source control history (even if deleted later)
- Accessible to anyone with repository access
- Difficult to change without code deployment
- Same credentials used across all environments
- Violates security best practices and compliance requirements

### Solution 1: User Secrets (Development Only)

User Secrets store sensitive data outside the project directory, preventing accidental commits to source control.

**Important Notes:**
- ⚠️ **Development only** - User Secrets are NOT encrypted and should never be used in production
- ⚠️ Not suitable for team sharing - each developer manages their own secrets
- ✅ Prevents accidental commits to source control
- ✅ Works seamlessly with IConfiguration

### Solution 2: Environment Variables

Environment variables are accessible to the application at runtime and can be set per environment.

**Advantages:**
- ✅ Separate from code and configuration files
- ✅ Different values per environment
- ✅ Supported by all hosting platforms
- ✅ No code changes needed

**Disadvantages:**
- ⚠️ Not encrypted by default
- ⚠️ Visible in process listings
- ⚠️ Difficult to manage at scale

### Solution 3: Azure Key Vault (Recommended for Production)

Azure Key Vault is a cloud service for securely storing and accessing secrets, keys, and certificates.

**Advantages:**
- ✅ Encrypted at rest and in transit
- ✅ Centralized secret management
- ✅ Access control and auditing
- ✅ Automatic rotation support
- ✅ Integrated with Azure services
- ✅ Compliance with security standards

**Disadvantages:**
- ⚠️ Azure-specific (vendor lock-in)
- ⚠️ Additional cost
- ⚠️ Requires network connectivity

### Solution 4: HashiCorp Vault

HashiCorp Vault is an open-source, enterprise-grade secrets management tool that provides a unified interface to store, access, and distribute secrets across any infrastructure.

**What is HashiCorp Vault?**

HashiCorp Vault is designed to securely store and tightly control access to tokens, passwords, certificates, API keys, and other secrets in modern computing. It handles leasing, key revocation, key rolling, and auditing. Through a unified API, users can access an encrypted Key/Value store and network encryption-as-a-service, or generate AWS IAM/STS credentials, SQL/NoSQL databases, X.509 certificates, SSH credentials, and more.

**Key Features:**

1. **Platform-Agnostic**: Works with any cloud provider, on-premises, or hybrid infrastructure
2. **Secure Secret Storage**: Encrypts data at rest using 256-bit AES with GCM
3. **Dynamic Secrets**: Generates secrets on-demand for supported systems (databases, AWS, SSH, etc.)
4. **Data Encryption**: Provides encryption as a service without storing data
5. **Leasing and Renewal**: All secrets have a lease associated with them
6. **Revocation**: Built-in support for secret revocation
7. **Audit Logging**: Detailed audit logs of all access to secrets
8. **Multiple Authentication Methods**: Token, LDAP, GitHub, AWS, Azure, Kubernetes, and more
9. **Access Control Policies**: Fine-grained access control using policies
10. **High Availability**: Supports clustering for high availability

**Architecture:**

```
┌─────────────────────────────────────────┐
│         Application/Client              │
└───────────────┬─────────────────────────┘
                │ HTTPS/API
                ▼
┌─────────────────────────────────────────┐
│         HashiCorp Vault Server          │
│  ┌───────────────────────────────────┐  │
│  │      Authentication Methods       │  │
│  │  (Token, LDAP, AWS, Azure, etc.)  │  │
│  └───────────────────────────────────┘  │
│  ┌───────────────────────────────────┐  │
│  │        Secrets Engines            │  │
│  │  (KV, Database, AWS, PKI, etc.)   │  │
│  └───────────────────────────────────┘  │
│  ┌───────────────────────────────────┐  │
│  │       Encryption (Barrier)        │  │
│  └───────────────────────────────────┘  │
└─────────────┬───────────────────────────┘
              │ Encrypted Storage
              ▼
┌─────────────────────────────────────────┐
│    Storage Backend (File, Consul, etc.) │
└─────────────────────────────────────────┘
```

**Advantages of HashiCorp Vault:**

- ✅ **Platform-agnostic**: Works with any infrastructure (AWS, Azure, GCP, on-premises)
- ✅ **Open source**: Free to use with enterprise version available
- ✅ **Dynamic secrets**: Generates credentials on-demand, reducing exposure
- ✅ **Automatic rotation**: Secrets can be automatically rotated
- ✅ **Comprehensive audit logs**: Every secret access is logged
- ✅ **Multiple authentication methods**: Flexible authentication options
- ✅ **Encryption as a service**: Can encrypt/decrypt data without storing it
- ✅ **Policy-based access control**: Fine-grained control over who can access what
- ✅ **High availability**: Clustering support for production environments
- ✅ **Extensive integrations**: Works with databases, cloud providers, SSH, PKI, etc.

**Disadvantages:**

- ⚠️ **Complexity**: Requires infrastructure setup and maintenance
- ⚠️ **Learning curve**: More complex than simple environment variables
- ⚠️ **Operational overhead**: Need to manage Vault servers, unsealing, backups
- ⚠️ **Network dependency**: Application must be able to reach Vault server
- ⚠️ **Cost**: Enterprise features require paid license

**When to Use HashiCorp Vault:**

- Multi-cloud or hybrid infrastructure
- Need platform-agnostic secret management
- Require dynamic secret generation
- Want fine-grained access control and auditing
- Need to manage secrets across many applications
- Want to avoid vendor lock-in
- Have compliance requirements for secret management
- Need encryption as a service
- Building microservices that need to share secrets

### Best Practices Summary

**Development:**
1. ✅ Use User Secrets for local development
2. ✅ Use `.gitignore` to exclude `appsettings.Development.json` if it contains secrets
3. ✅ Document required secrets in README without exposing actual values
4. ✅ Use placeholder values in committed config files

**Production:**
1. ✅ Use Azure Key Vault, AWS Secrets Manager, or HashiCorp Vault
2. ✅ Implement Managed Identity / IAM roles (no hard-coded credentials)
3. ✅ Rotate secrets regularly
4. ✅ Use separate secrets for each environment (dev, staging, production)
5. ✅ Enable auditing and monitoring
6. ✅ Implement least privilege access
7. ✅ Never log sensitive values
8. ✅ Use encrypted connections (HTTPS, SSL)

### Configuration Priority Order

.NET Core loads configuration in this order (later sources override earlier ones):
1. appsettings.json
2. appsettings.{Environment}.json
3. User Secrets (Development only)
4. Environment Variables
5. Command-line arguments
6. Azure Key Vault / Cloud providers

## Framework-dependent and self-contained deployments

When deploying a .NET Core application, you must choose between two deployment models: **framework-dependent** and **self-contained**. This choice affects how your application is packaged, deployed, and run on target machines.

### Framework-Dependent Deployment (FDD)

Framework-dependent deployment relies on the .NET runtime being pre-installed on the target system. The deployment package contains only your application code and third-party dependencies, not the .NET runtime itself.

**How It Works:**
1. Package contains only application DLLs and dependencies
2. Target machine must have compatible .NET runtime installed
3. Application launched using `dotnet myapp.dll` command
4. Runtime is shared across all .NET applications on the system

**Characteristics:**

| Aspect | Details |
|--------|---------|
| **Size** | Small (~100 KB for simple apps) |
| **Runtime** | Must be pre-installed on target |
| **Updates** | Runtime updates apply automatically |
| **Compatibility** | Works on any OS with .NET runtime |
| **Launch** | Requires `dotnet` CLI |

**Advantages:**
- ✅ **Small deployment size**: Only application binaries, no runtime
- ✅ **Easy updates**: Runtime updates apply to all apps automatically
- ✅ **Shared runtime**: Multiple apps share the same runtime installation
- ✅ **Security patches**: Runtime vulnerabilities fixed by updating once
- ✅ **Faster deployment**: Less data to transfer
- ✅ **Lower storage requirements**: Multiple apps don't duplicate runtime

**Disadvantages:**
- ⚠️ **Runtime dependency**: Target machine must have .NET runtime installed
- ⚠️ **Version conflicts**: Apps may require different runtime versions
- ⚠️ **No control**: Can't guarantee exact runtime version
- ⚠️ **Deployment complexity**: Must ensure runtime is installed first
- ⚠️ **Breaking changes**: Runtime updates might break compatibility

**Use Cases:**
- Internal enterprise applications where runtime is managed centrally
- Microservices in containerized environments with base runtime images
- Development and testing environments
- Applications deployed to known environments (you control the infrastructure)
- When minimizing deployment size is critical

### Self-Contained Deployment (SCD)

Self-contained deployment packages the entire .NET runtime with your application. The deployment is a complete, standalone package that doesn't require any pre-installed runtime.

**How It Works:**
1. Package contains application, dependencies, AND .NET runtime
2. No runtime installation needed on target machine
3. Application runs as a native executable
4. Each application has its own isolated runtime

**Characteristics:**

| Aspect | Details |
|--------|---------|
| **Size** | Large (~70-100 MB for simple apps) |
| **Runtime** | Included in deployment |
| **Updates** | Must redeploy app for runtime updates |
| **Compatibility** | Platform-specific (must specify RID) |
| **Launch** | Direct executable (myapp.exe) |

**Advantages:**
- ✅ **No runtime dependency**: Works on machines without .NET installed
- ✅ **Version control**: Exact runtime version bundled with app
- ✅ **Isolation**: Each app has its own runtime (no conflicts)
- ✅ **Simple deployment**: Single package, no prerequisites
- ✅ **Predictable behavior**: Runtime can't change unexpectedly
- ✅ **Native executable**: Can run directly without `dotnet` command

**Disadvantages:**
- ⚠️ **Large deployment size**: 70-100+ MB even for simple apps
- ⚠️ **Platform-specific**: Must build separate packages for each OS
- ⚠️ **Update burden**: Must redeploy app for runtime security patches
- ⚠️ **Storage overhead**: Each app duplicates the runtime
- ⚠️ **Slower deployment**: More data to transfer
- ⚠️ **Maintenance**: More packages to manage and update

**Use Cases:**
- Distributing to end users (desktop applications)
- Environments where you can't install runtime (restricted systems)
- Applications with specific runtime version requirements
- Containers where you want minimal base images
- Offline or air-gapped environments
- When you need native executables
- Maximum portability and independence

## Garbage collection

Garbage Collection (GC) is an automatic memory management feature in .NET Core that reclaims memory occupied by objects that are no longer in use by the application. It eliminates the need for manual memory management and prevents common memory-related bugs.

### What is Garbage Collection?

**Purpose**: Automatically manages the allocation and release of memory for your application.

**Key Responsibilities:**
1. Allocates memory for new objects
2. Determines which objects are no longer being used
3. Reclaims memory from unreachable objects
4. Compacts memory to reduce fragmentation
5. Manages the heap to optimize performance

**Benefits:**
- ✅ Prevents memory leaks
- ✅ Eliminates dangling pointers
- ✅ Prevents double-free errors
- ✅ Automatic memory management
- ✅ Improves developer productivity

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

## How can you improve the performance of a .NET Core application?