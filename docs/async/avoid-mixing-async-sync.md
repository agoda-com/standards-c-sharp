## Avoid mixing synchronous and asynchronous code

Avoid mixing synchronous and asynchronous code. Doing so can lead to confusing deadlocks and may hurt performance / scalability. Code should be asynchronous "all the way down", ie. to the application entrypoint or controller action.

#### Don't
```c#
public int MyMethod(int input) 
{
    // ...
    var task = _dependency.DoWorkAsync(input);
    return task.Result; // blocks thread until task completes
}
```

#### Do
```c#
public async Task<int> MyMethodAsync(int input)
{
    // ...
    return await _dependency.DoWorkAsync(input); // frees thread to do other stuff while task completes
}
```