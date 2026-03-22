---
name: csharp-collections
description: >-
  C# collection type selection, usage patterns, and API design standards.
  Covers arrays, IEnumerable, IList, IDictionary, IQueryable, thread-safe
  collections, null/empty enumerables, public vs non-public API return types,
  immutable collections, reference types as keys. Use when choosing collection
  types, designing APIs that return or accept collections, or working with
  enumerables in C#.
---

# C# Collection Standards

## Do not use Arrays

No public method may return an Array (except `byte[]` for binary data). Use `List<T>`, `IEnumerable<T>`, `Dictionary<K,V>`, `HashSet<T>` instead. Arrays are only acceptable internally for performance-critical or raw binary scenarios.

## Allowed public return types

Only these may be returned from public methods:

- `IEnumerable<T>`
- `ISet<T>`
- `IList<T>`
- `IDictionary<K, V>`
- `IReadOnlyDictionary<K, V>`
- `KeyedCollection<T>`
- `byte[]` (binary data only)

## Public API input parameters

Accept the most general interface that supports needed operations.

```c#
// Don't
public string DoWork(List<string> ss) { ... }

// Do
public string DoWork(IEnumerable<string> ss) { ... }
```

## Public API return types

- Return interfaces, not concrete types
- Return the most general suitable interface (usually `IEnumerable<T>`)
- Prefer readonly interfaces to express snapshot semantics

```c#
// Don't
public class MyDto { public List<string> MyStrings; }

// Do
public class MyDto { public IEnumerable<string> MyStrings; }
```

## Non-public API return types

For internal methods, expose more specific interfaces. Only return `IEnumerable<T>` when lazy/streamed/infinite evaluation is needed or no richer interface fits.

```c#
// Don't
private IEnumerable<string> GetStrings() => new List<string> { ... };

// Do
private IList<string> GetStrings() => new List<string> { ... };
```

## Do not return null for empty enumerables

Return `Enumerable.Empty<T>()` instead. Better yet, fix upstream to never return null.

```c#
// Don't
if (properties == null) return null;

// Do
if (properties == null) return Enumerable.Empty<int>();
```

## Do not check collection size before enumerating

`foreach` on an empty collection simply doesn't execute — no guard check needed.

## Thread-safe collections

- `List<T>` → `SynchronizedCollection<T>`
- `Dictionary<K,V>` → `ConcurrentDictionary<K,V>`
- FIFO: `Queue<T>` / `ConcurrentQueue<T>`
- LIFO: `Stack<T>` / `ConcurrentStack<T>`

## Immutable collections

No established use-case. Prefer synchronized collections for thread safety.

## Reference types as keys

Objects used as dictionary/set keys must implement `GetHashCode()` and override `Equals()`. Use `System.HashCode` in .NET Core.

## `IQueryable<T>`

- **WebApi**: return when enabling arbitrary client-side OData filtering/paging
- **Class libraries**: only when exposing a dataset queryable via expression trees (e.g., LINQ to SQL)
