## Return `Task` instead of `void` for async methods with no return value

- For asynchronous methods with no return value, return `Task` instead of `void`. Returning void makes it difficult/impossible to handle exceptions, test, and write continuations.
- The only exception is for asynchronous event handlers, which must return void.

#### Don't

```c#
public async void DoSomethingAsync() { ... }
```

#### Do
```c#
public async Task DoSomethingAsync() { ... }
```