## Put extension methods in their own namespaces

This allows consumers to easily opt in or out of using your extensions and seeing them in Intellisense.

#### Do
```c#
namespace Agoda.MyApp.MyExtensions
{
   ...    
}
```