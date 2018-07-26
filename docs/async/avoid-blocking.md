## Avoid blocking when possible

- Increase performance and scalability of your code by avoiding blocking calls.
- A blocking call ties up the thread while it waits for a response. During this time, it could have been doing something more useful, like serving other requests, or redrawing the GUI.
- A non-blocking call returns the thread to the threadpool, or keeps the GUI thread responsive, while the task completes.

#### Don't
```c#
Task.Delay(5000); // thread is blocked for 5 seconds
```

#### Do
```c#
await Task.Delay(5000); // thread can do other stuff for 5 seconds.
```

#### Don't
```c#
public void CreateCsv()
{
    using(var writer = File.CreateText("myfile.csv"))
    {
        writer.WriteLine("...");
    }
}
```

#### Do
```c#
public async Task CreateCsv()
{
    using(var writer = File.CreateText("myfile.csv"))
    {
        await writer.WriteLineAsync("...");
    }
}
```