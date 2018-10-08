## Consider making a method static:

- When it it pure - ie. operates in-process, has no side effects, and always returns the same result for a given input.
- When it _does not_ require any dependencies.
- When it _does_ require dependencies, but they can be reasonably be passed as method parameter(s). We don't want to pass an irrelevant dependency through 10 methods just so the 11th can access it - use judgement.
- When injecting a dependency just to call a single operation on it (eg. `dependency.IsX()`). If the caller already takes this dependency, consider making the callee static, and passing the result of `dependency.IsX()` as a parameter. 
    - Reconsider this if it would bloat the parameter list. (Though perhaps the method could be broken down into smaller methods?)
    - Yes, this creates a tension between constructor bloat and parameter bloat.
- When the method is relatively simple and does not require extensive test setup. (Though extensive test setup might indicate a code smell in the method itself).


#### Reconsider

```c#
public class MyClass : IMyClass
{
    private readonly IDependecy _dependency;
    
    public MyClass(IDependecy dependency)
    {
        _dependency = dependency;
    }
    
    public void MyMethod()
    {
        // We passed in the entire dependency just to call one method on it.
        if (_dependency.IsX())
        {
            // do something
        }
        else
        {
            // do something else
        }
    }
}
```

#### Consider

```c#
public static class MyClass
{
    public static void MyMethod(bool isX)
    {
        if (isX)
        {
            // do something
        }
        else
        {
            // do something else
        }
    }
}

// then from the caller:
MyClass.MyMethod(_dependency.IsX());

```