## Avoid race conditions with proper synchronization

Be careful to avoid race conditions where state might be mutated by multiple threads. Since you cannot `await` from inside a `lock` statement, either use a thread safe datatype, or synchronize access manually with a `SemaphoreSlim`:

#### Don't
```c#
int value;

Task<int> GetNextValueAsync(int current) { ... }

async Task UpdateValueAsync()
{
    value = await GetNextValueAsync(value);
}
```

#### Do
```c#
var mutex = new SemaphoreSlim(1); // max number of concurrent threads

int value;

Task<int> GetNextValueAsync(int current) { ... }

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
