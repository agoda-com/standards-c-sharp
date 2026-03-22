---
name: csharp-extension-methods
description: >-
  C# extension method design guidelines. Covers when to create extension
  methods, namespace organization, SRP promotion, breaking change avoidance,
  hiding advanced functionality. Use when writing, reviewing, or deciding
  whether to create C# extension methods.
---

# C# Extension Method Standards

## When to write an extension method

Only when at least one is true:

- The operation is very general (applies to any instance of the extended type)
- To promote Single Responsibility Principle
- To hide advanced functionality from typical users
- You don't control the source code you wish to extend
- To avoid heavy breaking changes to an interface

Otherwise, create a standard method on the interface/class itself.

## Put extension methods in their own namespaces

Allows consumers to opt in/out via `using` statements and controls Intellisense pollution.

```c#
namespace Agoda.MyApp.MyExtensions
{
    public static class Extensions { ... }
}
```

## Prefer to extend interfaces over classes

Extending `IEnumerable` is far more useful than extending `List`.

## Only for general operations

Must reasonably apply to any instance of the extended type.

```c#
// Don't — not all decimals are currencies
public static string FormatCurrency(this decimal amount, string currencyCode) { ... }

// Don't — not all strings are HTML
public static string StripHtmlTags(this string html) { ... }

// Do — all ints can be even
public static bool IsEven(this int num) { ... }
```

## Use to promote SRP

Move unrelated conversion/utility behavior out of core types.

```c#
// Don't — Int32 shouldn't know about decimal conversion
public struct Int32 { public decimal ConvertToDecimal() { ... } }

// Do — separate concern via extension
namespace System.ConversionExtensions
{
    public static class Int32ConversionExtensions
    {
        public static decimal ToDecimal(this int num) { ... }
    }
}
```

## Use to hide advanced functionality

Put advanced/esoteric methods in a sub-namespace (e.g., `Advanced`) so typical users don't see them in Intellisense.

## Use to avoid breaking changes

When changing an interface would break too many implementations or consumers, add new functionality as an extension instead.

## Use when you lack control of source

Augment third-party or framework classes via extensions instead of forking.
