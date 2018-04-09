## Only catch exceptions with good reason

- Do not catch certain type of exception unless you have good reason to believe it may be thrown to you. This relates to [Only catch what you can handle](only-catch-what-you-can-handle.md).
- Do not catch an exception unless you have a good reason to do so. Such reasons might be:
   - gracefully recovering from the error
   - enriching the error message / wrapping the exception
   - retrying the operation
   - logging the error

### Don't

```c#
try
{
    return myService.GetData();
}
catch (Exception ex)
{
    Log.Error(ex.ToString()); 
    throw; // this will be caught by the global exception handler anyway, 
           // so we just end up logging the exception twice
}

```

### Do

```c#
try
{
    return myService.GetData();
}
catch (MyServiceException ex)
{
    var betterEx = new MyApplicationException("Something bad happened with myService")
    {
         InnerException = ex,
         UserInfo = userInfo
    }
    throw betterEx; 
}

```