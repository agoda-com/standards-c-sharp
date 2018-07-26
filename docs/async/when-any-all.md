## Use `await Task.WhenAny/All` instead of `Task.WaitAny/All`

To wait for tasks to finish, use `await Task.WhenAll/Any` instead of `Task.WaitAll/Any` or `Task.Result`. The latter blocks the thread while the tasks complete, defeating the purpose of using async.

#### Don't
```c#
var task1 = DownloadFileAsync("...");
var task2 = DownloadFileAsync("...");
Task.WaitAll(task1, task2); // blocks
```

#### Do

```c#
var task1 = DownloadFileAsync("...");
var task2 = DownloadFileAsync("...");
await Task.WhenAll(task1, task2); // does not block
```
