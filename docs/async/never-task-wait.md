## Never use `Task.Wait()`

By design, `Task.Wait` blocks the current thread until the task completes. This defeats the purpose of writing async code. It also has the potential to cause deadlocks.

#### Don't
```c#
task.Wait();
var result = task.Result;
```

#### Do
```c#
var result = await task;
```

[Further reading](https://blog.stephencleary.com/2014/10/a-tour-of-task-part-5-wait.html)