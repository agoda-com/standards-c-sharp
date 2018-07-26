## Do not expose both sync and async versions of methods

- When writing an API that performs I/O or long running CPU intensive work, only expose an asynchronous version of the method. 
- Suffix it with `Async`.
- Do not provide an inferior synchronous option, because then people will use it.
- If it can be async, then it must be. If it can't be async, then it should not be. It cannot be both.

#### Don't

```c#
interface IFileDownloader 
{
    byte[] DownloadFile(string url);
    Task<byte[]> DownloadFileAsync(string url);
}
```

#### Do
```c#
interface IFileDownloader 
{
    Task<byte[]> DownloadFileAsync(string url);
}
```
