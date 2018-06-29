## When to write a static method (eg. in a helper class)

- When it it pure - ie. operates in-process, has no side effects, and always returns the same result for a given input.
- When it _does not_ require any dependencies.
- When it _does_ require dependencies, but they can be reasonably* be passed as input arguments(s) - use judgement. (*define reasonably)
- When the method is relatively simple and does not require extensive test setup (though extensive test setup may indicate a code smell in the method itself).
- When your only dependency is the ExperimentManager. Instead of the EM, pass the result of DetermineVariant as a parameter to your static method. When your experiment is (de)integrated, you will remove any dependency on ExperimentManager anyway, so don't add it in the first place.

#### Don't

```c#
public class MyClass
{
    private IExperimentManager _experimentManager;
    
    public MyClass(IExperimentManager experimentManager)
    {
        _experimentManager = experimentManager;
    }
    
    public void MyMethod()
    {
        if (_experimentManager.DetermineVariant(ExpId.EXP))
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

#### Do

```c#
public static class MyClass
{
    public static void MyMethod(bool isExpB)
    {
        if (isExpB)
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