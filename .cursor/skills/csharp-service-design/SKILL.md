---
name: csharp-service-design
description: >-
  C# service architecture, dependency injection, and static vs instance method
  standards. Covers service design, interface creation, constructor injection,
  avoiding over-injection, facade service refactoring, DI attribute-based
  registration, framework abstraction avoidance, pure static methods. Use when
  designing services, configuring dependency injection, deciding between static
  and instance methods, or refactoring constructors with too many dependencies
  in C#.
---

# C# Service Design Standards

## Interfaces

Only create an interface if:

- Multiple implementations exist in application code, or
- The component needs to be mocked for testability

Unnecessary interfaces pollute the codebase.

## Injection and mocking

Only mock **impure** components (performs I/O, non-deterministic). Pure functions don't need mocking, injection, or interfaces.

## Prefer static methods

Pure functions (in-memory, no side effects, deterministic) should be static. Benefits:

- Easier to reason about — all state is in parameters
- Often easier to test — no mocking needed
- Reduces constructor dependency bloat

### When to use static

- Method is pure with no dependencies
- Dependencies can be reasonably passed as parameters
- Injecting a dependency just to call one operation on it

```c#
// Reconsider — injecting entire dependency for one call
public class MyClass : IMyClass
{
    private readonly IDependency _dependency;
    public MyClass(IDependency dependency) { _dependency = dependency; }
    public void MyMethod() { if (_dependency.IsX()) { ... } }
}

// Consider — pass the result instead
public static class MyClass
{
    public static void MyMethod(bool isX) { if (isX) { ... } }
}
// Caller: MyClass.MyMethod(_dependency.IsX());
```

### When to use instance

- Method is impure (I/O, side effects)
- Requires injected dependencies for mockability
- Static version would require excessive test setup

## Avoid constructor over-injection

Max ~7 dependencies, absolute maximum 10. High count indicates SRP violations.

### Don't use service sets

Wrapping dependencies in a container class just hides the problem — the service still does too much.

### Refactor to Facade Services

Extract clusters of related dependencies into their own focused service:

1. Identify dependency clusters used together
2. Extract an interface (the Facade)
3. Move implementation to a new class
4. Inject the facade instead of individual dependencies
5. Remove redundant dependencies

```c#
// Before: 5 dependencies
public class OrderProcessor
{
    public OrderProcessor(IOrderValidator validator, IOrderShipper shipper,
        IAccountsReceivable receivable, IRateExchange exchange, IUserContext userContext) { }
}

// After: extract OrderCollector facade, down to 3 dependencies
public class OrderProcessor
{
    public OrderProcessor(IOrderValidator validator, IOrderShipper shipper,
        IOrderCollector collector) { }
}
```

## Don't depend on framework abstractions

Don't depend on `HttpContext` etc. Create environment-agnostic abstractions. Avoid "trainwrecks" (`a.b().c.d`).

```c#
// Don't
entity.CreatedBy = this.accessor.HttpContext.User.Identity.Name;

// Do — create an abstraction
public interface IUserContext { string Username { get; } }
entity.CreatedBy = userContext.Username;
```

## Dependency injection registration

### Use attribute-based registration

```c#
// Don't
container.RegisterType<MyClass, MyInterface>(new ContainerControlledLifetimeManager());

// Do
[RegisterSingleton]
public class MyClass : MyInterface { }
```

Available attributes: `[RegisterSingleton]`, `[RegisterPerRequest]`, `[RegisterTransient]`

### No stereotype registrations

Don't use stereotype-based registration (e.g. `RegisterRepository`). Explicitly describe each component's characteristics.

## Other rules

- Declare dependencies explicitly in the constructor — no property injection
- Each injectable component: single public constructor only
- Use null object pattern for truly optional dependencies
- **Never use `DependencyResolver.Current`** — it's the Service Locator anti-pattern
