## General

### Put extension methods in their own namespaces

This allows consumers to easily opt in or out of using your extensions and seeing them in Intellisense.

#### Do
```c#
namespace Agoda.MyApp.MyExtensions
{
   public static class Extensions
   {
       ...
   }
}
```

### Prefer to extend interfaces over classes

Where possible, prefer to extend an interface rather than a class. If appropriate, your extension method will be a lot more useful on `IEnumerable` than `List`.