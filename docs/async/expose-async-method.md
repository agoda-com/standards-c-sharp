## Do not expose sync and async versions of methods

- When writing an API, only expose asynchronous versions of methods that perform I/O or long running CPU intensive tasks. 
- Suffix them with `Async`.
- Do not provide an inferior synchronous option, because then people will use it.
- If it can be async, then it must be. If it can't be async, then it should not be. It cannot be both.

#### Don't

```c#
public byte[] DownloadFile(string url) { ... }
public async Task<byte[]> DownloadFileAsync(string url) { ... }
```

#### Do
```c#
public async Task<byte[]> DownloadFileAsync(string url) { ... }
```
