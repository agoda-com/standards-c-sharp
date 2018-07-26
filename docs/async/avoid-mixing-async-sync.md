## Avoid mixing synchronous and asynchronous code

Avoid mixing synchronous and asynchronous code. Doing so can lead to confusing deadlocks and may hurt performance / scalability. Code should be asynchronous "all the way down", ie. to the application entrypoint or controller action.

#### Don't
```c#
public int MyMethod() 
{
    var task = MyAsyncMethod();
    return task.Result; // blocks thread until task completes
}
```

#### Do
```c#
public async Task<int> MyMethod()
{
    return await MyAsyncMethod(); // returns thread to threadpool while task completes
}
```