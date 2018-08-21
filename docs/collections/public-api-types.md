## Collections types for public APIs

We define a public API as:
- Any `public` method in a class library
- Any controller action in a WebApi

### Input parameters for public APIs

- Accept interfaces instead of concrete implementations where possible.
- Input parameters should be of a type as general as possible, but still support the operations you need to perform on the object.

#### Don't

```c#
public class MyPublicService
{
    public string DoWork(List<string> ss)
    {
        // we do not use any of List<T>'s methods...    
        return ss.FirstOrDefault()?.ToUpper() ?? "";
    }
}
```

#### Do

```c#
public class MyPublicService
{
    // ...so let's accept an IEnumerable<T> instead to be nice to the client
    public string DoWork(IEnumerable<string> ss)
    {   
        return ss.FirstOrDefault()?.ToUpper() ?? "";
    }
```

### Return types for public APIs

- Return interfaces instead of concrete implementations public API collections. This gives more freedom to change the implementation without breaking client code that relies on it.
- Return the most general collection interface suitable for your use-case. Usually this is `IEnumerable<T>` for collection- or sequence-like structures. It is immutable, and keeps the API implementation as flexible as possible.
- Prefer to expose the readonly interfaces over the standard mutable ones. This expresses more semantically that the consumer received a snapshot rather than a live collection. The consumer can always convert to a mutable collection if required. This is a cheap operation, except for very large collections, which should be streamed as `IEnumerable<T>` anyway.

#### Don't

```c#
public class MyDto
{
    public List<string> MyStrings;
}
```
#### Do

```c#
public class MyDto
{
    // now we are free to change the implementation to 
    // almost anything without breaking the client
    public IEnumerable<string> MyStrings;
}
```