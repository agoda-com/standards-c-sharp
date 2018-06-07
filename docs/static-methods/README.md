## Static methods

### Why make a method static

- They are easier to reason about as all state is passed in through arguments.
- They cannot be injected, so we reduce constructor dependencies and container registrations.
- They simplify the code as we don't require an instance to be created.
- They are often easier to test because fewer mocks need to be configured.

### When to make a static method (eg. in a helper class)

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

### When to make an instance method (eg. in an injected component)

- When your method is impure - ie. makes an external call, has side effects like writing to the disk. These parts will need to be mocked for unit tests.
- When it requires one or more (impure) dependencies. These will need to be passed into the constructor for mocking.
- When setting up test cases is time consuming and difficult. As mentioned above, this may indicate a code smell, but there are legitimate cases where this may be required. In this case, an instance method with mock dependencies may ease testing, though do consider the builder pattern to help create the test data first.