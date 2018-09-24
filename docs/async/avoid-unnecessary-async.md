## Avoid unnecessary use of `async` modifier

`async` does not come for free, so only add the modifier when `await` is necessary.

- Under the hood, `async` causes the compiler to generate a state-machine helper class with a bunch of boilerplate.
- This can prevent certain runtime optimizations, and cause the garbage collector to hold on to objects for longer than it otherwise might.

Consider the following service:

```c#
public interface IService
{
    Task<int> DoWorkAsync(int input);
}

```

#### Don't
```c#

private IService _service;

public async Task<int> MyMethodAsync(int input) 
{
    // ...
    
    // unnecessarily wraps the already asynchronous call in a new async context
    return await _service.DoWorkAsync(input); 
}
```

#### Do
```c#
private IService _service;

public Task<int> MyMethodAsync(int input)
{
    // ...
    
    // service already returns Task<int>, so just return the result directly, and remove the async modifier
    return _service.DoWorkAsync(input); 
}
```

[Further reading](https://msdn.microsoft.com/en-us/magazine/hh456402.aspx)