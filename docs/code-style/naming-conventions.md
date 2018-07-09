## Naming conventions

Naming conventions are pretty arbitrary, so long debates over them are not productive. The important thing is consistency across the code base. Therefore, we have fairly arbitrarily chosen the following conventions. If we haven't chosen your ones, then we are truly sorry. But we have to pick something, and really there are much more important things to worry about.

All standard .NET naming conventions are to be followed, except for those listed here, which have never really been standardized, or whose standards have not been used consistently even within Microsoft's own code.

### Private fields

Use `_myPrivateField` rather than `this.myPrivateField`.

- It is fewer characters and less noisy.
- It prevents the silly mistake of assigning a variable to itself because of a missing `this.`. 

#### Don't

```c#
public class MyClass
{
    private int myInt;
    
    public MyClass(int myInt)
    {
        this.myInt = myInt;
    }
}

```

#### Do

```c#
public class MyClass
{
    private int _myInt;
    
    public MyClass(int myInt)
    {
        _myInt = myInt;
    }
}
```

### Constants

Use `MY_CONSTANT` rather than `MyConstant`.

- It destinguishes it from a public field / property / method group.
- It looks a little shouty, so draws attention to a value that - being declared as a constant - is probably quite important in your application. This can aid in understanding the code.

#### Don't

```c#
public const int AgeOfEarthInYears = 6000;
```

#### Do

```c#
public const int AGE_OF_EARTH_IN_YEARS = 6000;
```