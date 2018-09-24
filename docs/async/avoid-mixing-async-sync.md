## Avoid mixing synchronous and asynchronous code

Avoid mixing synchronous and asynchronous code. Doing so can lead to confusing deadlocks and may hurt performance / scalability. Code should be asynchronous "all the way down", ie. to the application entrypoint or controller action.

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

public string MyMethod() 
{
    // ...
    
    var task = _service.DoWorkAsync(input);
    return task.Result.ToString(); // blocks thread until task completes
}
```

#### Do
```c#
private IService _service;

public Task<string> MyMethodAsync()
{
    // ...
    
    var result = await _service.DoWorkAsync(input); // frees thread to do other stuff while task completes 
    return result.ToString(); 
}
```