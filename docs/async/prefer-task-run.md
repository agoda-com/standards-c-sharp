## Prefer to start tasks with `Task.Run`

- Prefer `Task.Run` over `Task.Factory.StartNew` over `new Task()`.

### When to use `Task.Factory.StartNew`

- `Task.Factory.StartNew` is [considered dangerous](https://blog.stephencleary.com/2013/08/startnew-is-dangerous.html) and should only be used in very specific circumstances.
- The most common use-case is to specify `TaskCreationOptions.LongRunning`. In the current implementation this creates a new thread for your task, instead of running it on the threadpool. This could change between implementations / platforms, so should not be relied upon. Only do this if you have profiled your application and found it to be necessary. If you really need a new thread, consider good old [`Thread.Start`](https://msdn.microsoft.com/en-us/library/6x4c42hc(v=vs.110).aspx). 

#### Don't
```c#
Task.Factory.StartNew(MyMethod, CancellationToken.None, TaskCreationOptions.DenyChildAttach, TaskScheduler.Default);
```

#### Do
```c#
Task.Run(() => MyMethod()); // equivalent to the above
```

### When to use `new Task()`
- Only construct the task and `.Start()` it manually if you need to separate the task's creation from its execution, such as when you conditionally execute tasks that you've created.

#### Don't
```c#
var task = new Task(MyMethod, CancellationToken.None, TaskCreationOptions.DenyChildAttach);
task.Start(TaskScheduler.Default);
```

#### Do
```c#
Task.Run(() => MyMethod()); // equivalent to the above
```

