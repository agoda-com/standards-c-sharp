## How to rethrow an exception from a catch block

- When _rethrowing_ an exception simply `throw;` it. Do not `throw ex;` as this loses the original call stack information, making it look like the exception originated inside your `catch` block.
- The exception to this rule is if you have wrapped your exception to be more specific.

### Don't

```c#
try
{
    return myService.GetData();
}
catch (MyServiceException ex)
{
    // do something useful here, and then:
    throw ex; // the `ex` makes it look like the exception originated from _this_ line we have lost the original context
}

```

### Do

```c#
try
{
    return myService.GetData();
}
catch (MyServiceException)
{
    // do something useful here, and then:
    throw; // note no `ex`: now at least the exception originates from the original line 
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
    // Wrap the exception to give the consumer more information. Remember to set the InnerException property.
    var betterEx = new MyApplicationException("Something bad happened with myService")
    {
         InnerException = ex, // we preserve the context of our original exception here
         UserInfo = userInfo
    }
    throw betterEx;
}

```
