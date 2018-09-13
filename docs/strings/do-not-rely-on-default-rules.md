## For string operations, always explicitly specify comparison/culture rules

For minimum ambiguity and maximum readability, always use overloads that explicitly specify `StringComparison` or `CultureInfo` parameters.

- When performing string operations (sorting, comparison, case-conversions etc), relying on the default overloads can lead to subtle bugs, or at least produce code that is not obvious in its intent. 
- .NET's string methods are not consistent in the default rules they assume, which makes it difficult to remember which method uses which defaults, and easy to confuse overloads.
- Even if it obvious to you which rules will be applied by the default overloads, it may not be obvious to the person who has to maintain your code in the future.
- The few extra keystrokes required to be explicit in your intent will pay off long-term.  
- See [Best Practices for Using Strings in .NET](https://docs.microsoft.com/en-us/dotnet/standard/base-types/best-practices-strings) for a full list of recommendations.

### For non-linguistic processing

Use `StringComparison.Ordinal` or `StringComparison.OrdinalIgnoreCase` for non-linguistic processing or strings.

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

### For linguistic processing

Use `StringComparison.CurrentCulture` or a specific `CultureInfo` when processing linguistic data.

#### Don't

```c#
var result = string.Compare("able", "ångström");
```

#### Do

```c#
var result = string.Compare("able", "ångström", StringComparison.CurrentCulture);
// or
var result = string.Compare("able", "ångström", new CultureInfo("sv-SE"), CompareOptions.IgnoreNonSpace);

```