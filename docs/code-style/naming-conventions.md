## Naming conventions

Naming conventions are pretty arbitrary, so long debates over them are not productive. The important thing is consistency across the code base. If our chosen conventions down t align with your favorites, then we are truly sorry, but it is impossible to please everyone. We have to pick something, and there are much more important things to worry about.

We follow the default naming conventions suggested by Resharper. This way, out of the box all developers can be warned when a convention is violated, and provided with a quick-fix. Resharper's naming conventions are pretty uncontroversial, except perhaps for the following:

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