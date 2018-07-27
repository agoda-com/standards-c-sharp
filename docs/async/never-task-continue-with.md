## Use `await` instead of `Task.ContinueWith`

- `Task.ContinueWith` has some [surprising and non-intuitive behaviors](https://blog.stephencleary.com/2015/01/a-tour-of-task-part-7-continuations.html), so must be used with care.
- `await` has none of these problems. It is also more readable.
- Therefore, `Task.ContinueWith` should not be used unless you are doing [dynamic task parallelism](https://docs.microsoft.com/en-us/previous-versions/msp-n-p/ff963551(v=pandp.10)), which is rare.

#### Don't
```c#
await downloadTask.ContinueWith(async t => await SaveFileAsync(t.Result)); 
```

#### Do
```c#
await SaveFileAsync(await downloadTask);
```