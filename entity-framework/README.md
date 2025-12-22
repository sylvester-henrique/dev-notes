# 📚 Table of Contents

- [Performance considerations](#performance-considerations)
  - [DbContext Lifetime](#dbcontext-lifetime)
  - [AsNoTracking for Read-Only Queries](#asnotracking-for-read-only-queries)
  - [Explicit Loading vs Lazy Loading](#explicit-loading-vs-lazy-loading)
  - [Batch Operations](#batch-operations)
  - [Projections Instead of Full Entities](#projections-instead-of-full-entities)
  - [Pagination](#pagination)
  - [Asynchronous Operations](#asynchronous-operations)

## What is Entity Framework Core, and how does it integrate with .NET Core?

TODO

## Explain Code-First vs. Database-First approaches.

TODO

## What are migrations, and how are they used in EF Core?

TODO

# Performance considerations

## DbContext Lifetime

**Use the correct lifetime based on your application type:**

```csharp
// ASP. NET Core - Use Scoped (RECOMMENDED)
services.AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(connectionString),
    ServiceLifetime.Scoped); // One instance per HTTP request

// NOT Singleton - DbContext is NOT thread-safe
// NOT Transient - Creates too many instances
```

**Why Scoped? **
- ✅ One DbContext instance per HTTP request
- ✅ Automatic disposal after request completes
- ✅ Thread-safe within request scope
- ❌ Singleton causes concurrency issues
- ❌ Transient creates unnecessary overhead

---

## AsNoTracking for Read-Only Queries

```csharp
// 30-40% faster for read-only operations
var users = await context.Users
    .AsNoTracking()
    .ToListAsync();
```

**When to use:**
- Read-only queries (no updates needed)
- Displaying data in lists or reports
- API GET endpoints that only retrieve data

**Benefits:**
- Reduced memory usage
- Faster query execution
- No change tracking overhead

---

## Explicit Loading vs Lazy Loading

```csharp
// Disable lazy loading for better control
services.AddDbContext<AppDbContext>(options => 
    options.UseSqlServer(connectionString)
           .UseLazyLoadingProxies(false)); // Disable lazy loading

// Use explicit eager loading instead
var orders = await context.Orders
    .Include(o => o.Customer)
    .Include(o => o.OrderItems)
    .ToListAsync();
```

**Why disable lazy loading? **
- Prevents N+1 query problems
- Gives explicit control over data loading
- Avoids unexpected database calls
- Better performance predictability

---

## Batch Operations

```csharp
// Batch inserts
context.Users.AddRange(userList);
await context.SaveChangesAsync();

// Instead of multiple SaveChanges calls
```

## Projections Instead of Full Entities

```csharp
// Better - Only select needed fields
var userNames = await context.Users
    . Select(u => new { u. Id, u.Name })
    .ToListAsync();

// Avoid - Loading entire entity
var users = await context.Users. ToListAsync();
```

## Pagination

```csharp
// Always paginate large result sets
var pagedUsers = await context.Users
    .OrderBy(u => u.Id)
    .Skip(pageSize * pageNumber)
    .Take(pageSize)
    .AsNoTracking()
    .ToListAsync();
```

## Asynchronous Operations

```csharp
// Always use async methods
await context.Users.ToListAsync();
await context.SaveChangesAsync();

// Instead of synchronous
// context.Users.ToList();
// context.SaveChanges();
```
