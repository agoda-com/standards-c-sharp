---
name: csharp-code-style
description: >-
  C# code style, naming conventions, and general coding standards. Covers
  private field naming, constants, dynamics, monads, regions, string comparison
  culture rules, reflection hard-coded strings, attribute-based routing. Use
  when writing or reviewing C# code style, naming, string operations, routing,
  or reflection usage.
---

# C# Code Style Standards

## Naming conventions

Follow Resharper defaults for consistency.

### Private fields

Use `_myPrivateField` not `this.myPrivateField`.

```c#
// Don't
public class MyClass
{
    private int myInt;
    public MyClass(int myInt) { this.myInt = myInt; }
}

// Do
public class MyClass
{
    private int _myInt;
    public MyClass(int myInt) { _myInt = myInt; }
}
```

### Constants

Use `MY_CONSTANT` not `MyConstant`. Distinguishes from properties/methods and draws attention to important values.

```c#
// Don't
public const int AgeOfEarthInYears = 6000;

// Do
public const int AGE_OF_EARTH_IN_YEARS = 6000;
```

## Do not use `dynamic`

Dynamics skip compile-time type-checking. We work with statically typed languages (C#, Scala, TypeScript) — no use case for dynamics.

## Do not use monads

Don't define custom `Option<T>`, bind functions, etc. Built-in monadic types like `Nullable<T>` are fine.

## Do not use `#region`

If you need regions to organize your code, your class is too big — refactor it. Regions hide code and lead to poor maintenance decisions.

## String operations: always specify comparison/culture

Default overloads are inconsistent and can cause subtle bugs across machines.

### `StringComparison.Ordinal`

For non-linguistic strings (protocols, error codes, identifiers):

```c#
// Don't
var isHttp = protocol.Equals("http");

// Do
var isHttp = protocol.Equals("http", StringComparison.OrdinalIgnoreCase);
```

### `StringComparison.CurrentCulture`

For natural language strings where sort order may vary by culture:

```c#
// Don't
var result = string.Compare("able", "ångström");

// Do
var result = string.Compare("able", "ångström", StringComparison.CurrentCulture);
```

### `StringComparison.InvariantCulture`

Rarely needed — only for persisting linguistically meaningful but culturally agnostic data.

## Reflection: no hard-coded strings for types

Use `typeof()` instead of string-based type resolution. Failures are caught at compile time.

```c#
// Don't — fails at runtime after namespace change
var type = Type.GetType("Agoda.MyType");

// Do — caught at compile time
var type = typeof(Agoda.MyType);
```

## Use attribute-based routing

Convention-based routing couples code to URLs. Attribute routing decouples them and is easier to understand.

```c#
// Don't — convention-based
routes.MapRoute("Default", "{controller}/{action}/{id}", ...);

// Do — attribute-based
[Route("{department}/employees/{employeeId?}")]
public string Employee(string department, int? employeeId) { ... }
```
