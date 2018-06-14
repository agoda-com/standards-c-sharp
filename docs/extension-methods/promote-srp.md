## Use extension methods to promote SRP

You can promote the Single Resposibility Principle with extension methods. For this example, forget we have `System.Convert` in the BCL:

#### Don't

```c#
namespace System
{
    public struct Int32
    {
        ...
        
        // an int should know nothing about a decimal or how to convert to it
        public decimal ConvertToDecimal() 
        { ... }
    }
}
```

#### Do

```c#
namespace System
{
    public struct Int32
    { ... }
}

namespace System.ConversionExtensions
{
    public static class Int32ConversionExtensions
    {
        public static decimal ToDecimal(this int num) 
        { ... }
    }
}
```
