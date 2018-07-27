## Use `await task` instead of `task.Result`

- To get the result of a task, prefer `await task` over `task.Result`.
- `await` does not wrap the exception in an `AggregateException`, whereas `Task.Result` does. Ideally, we should never have to deal with an `AggregateException` at all.
- `Task.Result` blocks until the task has completed. `await` never blocks.
- `Task.Result` [can cause deadlocks](http://blog.stephencleary.com/2012/07/dont-block-on-async-code.html) in certain situations.
- Another method to retrieve the result is `Task.GetAwaiter().GetResult()`. This does not wrap the exception, but still suffers from blocking behavior and potential for deadlocks, so should not be used.

#### Don't
```c#
var task1 = DownloadFileAsync("...");
var task2 = DownloadFileAsync("...");
await Task.WhenAll(task1, task2);
var result1 = task1.Result; // not a problem, as we already awaited task completion...
```

#### Do
```c#
var task1 = DownloadFileAsync("...");
var task2 = DownloadFileAsync("...");
await Task.WhenAll(task1, task2);
var result1 = await task1; // ... but we want to standardize on this safer style
```

[Further reading](https://blog.stephencleary.com/2014/12/a-tour-of-task-part-6-results.html)