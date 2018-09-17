## For string operations, always explicitly specify comparison/culture rules

For minimum ambiguity and maximum readability, always use overloads that explicitly specify `StringComparison` and/or `CultureInfo` parameters.

- When performing string operations (sorting, comparison, case-conversions etc), relying on the default overloads can:
  - lead to subtle bugs
  - produce code that is not obvious in its intent
  - may work correctly on one machine but fail when run on another
- .NET's string methods are not consistent in the default rules they assume, which makes them difficult to remember
 and easily confused. 
- Even if the default behaviors are obvious to you as the programmer, they may not be to the person who maintains your code.
- The few extra keystrokes required to be explicit in your intent will pay off long-term.  
- See [Best Practices for Using Strings in .NET](https://docs.microsoft.com/en-us/dotnet/standard/base-types/best-practices-strings) for a full list of recommendations.

### `StringComparison.Ordinal`

Use `StringComparison.Ordinal` or `StringComparison.OrdinalIgnoreCase` for non-linguistic ("computery") type strings,
such as protocols or error codes.

#### Don't

```c#
var protocol = GetProtocol(uri);
var isHttp = protocol.Equals("http"); 
```

#### Do

```c#
var protocol = GetProtocol(uri);
var isHttp = protocol.Equals("http", StringComparison.OrdinalIgnoreCase); 
```

### `StringComparison.CurrentCulture`

Use `StringComparison.CurrentCulture`, `StringComparison.CurrentCultureIgnoreCase`, or supply a specific `CultureInfo` 
when the string contains natural language. In the following example, the expected sort order of the two strings 
may change between cultures.

#### Don't

```c#
var result = string.Compare("able", "ångström");
```

#### Do

```c#
var result = string.Compare("able", "ångström", StringComparison.CurrentCulture);
// or
var result = string.Compare("able", "ångström", new CultureInfo("sv-SE"), CompareOptions.None);

```

### `StringComparison.InvariantCulture`

> Do not use string operations based on `StringComparison.InvariantCulture` in most cases. One of the few exceptions is 
when you are persisting linguistically meaningful but culturally agnostic data. - [Best practices for using strings in .NET](https://docs.microsoft.com/en-us/dotnet/standard/base-types/best-practices-strings)

