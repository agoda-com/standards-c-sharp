---
name: csharp-async-patterns
description: >-
  C# async/await and Task usage standards. Covers avoiding blocking, mixing
  sync/async, race conditions, Task.Run, ConfigureAwait, Task.Result,
  ContinueWith, WhenAll/WhenAny, async void returns. Use when writing,
  reviewing, or modifying asynchronous C# code, Task-based APIs, or async
  methods.
---

# C# Async/Await Standards

## Always use async API versions

Prefer the async version of any method when it exists. No downsides.

```c#
// Don't
var bytes = DownloadFile("http://...");

// Do
var bytes = await DownloadFileAsync("http://...");
```

## Avoid blocking

Use `await Task.Delay()` not `Thread.Sleep()`. Use async I/O methods (`WriteLineAsync`, etc.).

```c#
// Don't
Thread.Sleep(5000);

// Do
await Task.Delay(5000);
```

## Don't mix sync and async

Code must be async "all the way down" to the entrypoint or controller action. Never call `.Result` from synchronous code.

## Use `await task` not `task.Result`

- `.Result` wraps exceptions in `AggregateException`
- `.Result` blocks the thread
- `.Result` can cause deadlocks
- `.GetAwaiter().GetResult()` also blocks — don't use it

```c#
// Don't
var result = task.Result;

// Do
var result = await task;
```

## Return `Task` not `void`

`async void` makes exception handling and testing impossible. Only exception: async event handlers.

```c#
// Don't
public async void DoSomethingAsync() { ... }

// Do
public async Task DoSomethingAsync() { ... }
```

## Only expose async version

Don't provide both sync and async versions. Only expose async, suffixed with `Async`.

```c#
// Don't
interface IFileDownloader
{
    byte[] DownloadFile(string url);
    Task<byte[]> DownloadFileAsync(string url);
}

// Do
interface IFileDownloader
{
    Task<byte[]> DownloadFileAsync(string url);
}
```

## Avoid unnecessary `async` modifier

If just returning a Task without needing `await`, skip `async` to avoid state machine overhead.

```c#
// Don't — unnecessary async wrapper
public async Task<int> MyMethodAsync(int input)
{
    return await _service.DoWorkAsync(input);
}

// Do — pass through directly
public Task<int> MyMethodAsync(int input)
{
    return _service.DoWorkAsync(input);
}
```

## Avoid race conditions

Use `SemaphoreSlim` for async synchronization (`lock` doesn't support `await`).

```c#
var mutex = new SemaphoreSlim(1);

async Task UpdateValueAsync()
{
    await mutex.WaitAsync();
    try
    {
        value = await GetNextValueAsync(value);
    }
    finally
    {
        mutex.Release();
    }
}
```

## Never use `Task.Wait()`

Blocks the thread, defeats async purpose, potential deadlocks.

## Never use `Task.ContinueWith`

Use `await` instead. Exception: dynamic task parallelism (rare).

```c#
// Don't
await downloadTask.ContinueWith(async t => await SaveFileAsync(t.Result));

// Do
await SaveFileAsync(await downloadTask);
```

## Use `Task.WhenAll/WhenAny` not `Task.WaitAll/WaitAny`

`Wait` versions block. `When` versions are async-friendly.

```c#
// Don't
Task.WaitAll(task1, task2);

// Do
await Task.WhenAll(task1, task2);
```

## Prefer `Task.Run`

Prefer `Task.Run` over `Task.Factory.StartNew` (dangerous) over `new Task()`. Only use `Task.Factory.StartNew` for `TaskCreationOptions.LongRunning`.

## `ConfigureAwait(false)`

- **.NET Core**: don't use (no effect)
- **Library code (legacy ASP.NET)**: always use
- **Legacy ASP.NET blocking on async**: use to avoid deadlocks (but prefer rewriting)
