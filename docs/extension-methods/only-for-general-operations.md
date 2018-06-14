## Use only for general operations

An extension method should only be used for very general operations - something that can reasonably apply to any instance of the type it is extending. This avoids polluting the namespace and adding noise to Intellisense.

#### Don't
		
```c#
// not all decimals represent currencies
public static string FormatCurrency(this decimal amount, string currencyCode) 
{ ... }
// here, we would prefer to create a Currency class, and add methods for formatting there

// not all strings represent HTML
public static string StripHtmlTags(this string html) 
{ ... }
```

#### Do

```c#
// all ints can be even
public static bool IsEven(this int num) => 
{ ... }

// all (async) tasks can be run synchronously
public static TResult RunSync<TResult>(this Func<Task<TResult>> func) 
{ ... }
```
