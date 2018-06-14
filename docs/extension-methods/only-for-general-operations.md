## Use only for general operations

An extension method should only be used for very general operations - something that can reasonably apply to any instance of the extended type. This avoids polluting the namespace with mainly useless methods and adding noise to Intellisense.

#### Don't
		
```c#
// not all decimals represent currencies
public static string FormatCurrency(this decimal amount, string currencyCode) 
{ ... }
// Here ^^^, we might prefer to create a Currency class, and add methods for formatting inside.

// not all strings are HTML
public static string StripHtmlTags(this string html) 
{ ... }
```

#### Do

```c#
// all ints can be even
public static bool IsEven(this int num) 
{ ... }

// all async tasks can be run synchronously
public static TResult RunSync<TResult>(this Func<Task<TResult>> func) 
{ ... }
```
