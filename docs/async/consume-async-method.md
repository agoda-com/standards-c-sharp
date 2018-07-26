## Always use the asynchronous version of a method when it exists

When consuming an API, always prefer the asynchronous version of a method if it exists. Performance and scalability may improve. There are no downsides.

#### Don't
```c#
var bytes = DownloadFile("http://...");
```

#### Do
```c#
var bytes = await DownloadFileAsync("http://...");
```