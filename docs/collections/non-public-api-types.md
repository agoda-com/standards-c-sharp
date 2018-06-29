## Collections on non-public API methods

We define a non-public API method as:
- Any non-`public` method in a class library solution
- Any method that is not a controller action / endpoint in a WCF / WebApi project
                    
Do not use `IEnumerable<T>`, except if one of the following is true:

- The items must be generated on-demand, for instance an infinite mathematical sequences (eg. Fibonacci).
- The length of the sequence is otherwise unknown.
- The items must be streamed from another source, for instance from a DB cursor.
- Performance is critical and can benefit by exposing lazy enumeration to the caller.
- There is no richer interface that better describes the semantics of the return value.

When none of the above are true:

- Expose a more specific interface, or the concrete type if no suitable interface exists. There is little advantage of returning an `IEnumerable<T>` when you have a `List<T>`, as you will likely to need to `.ToList()` it as some point anyway. Expose `IList<T>` instead.
- This is code you completely control, so you don't need to worry about breaking clients.

#### Don't

```c#
// unnecessary downcast on private method hides a bunch of functionality
private IEnumerable<string> GetStrings()
{
    return new List<string>{ ... };    
}
```

#### Do
```c#
// expose the original functionality, but through an interface to make it
// easier to change the implementation later
private IList<string> GetStrings()
{
    return new List<string>{ ... };    
}
```