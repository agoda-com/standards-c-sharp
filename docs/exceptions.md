## Errors and exceptions

_Fail as quickly and as loudly as possible._

We can only fix the problems we know about. Therefore, don't be tempted to hide exceptions from the user simply to "improve" the UX. This just leads to long-term difficult-to-diagnose inconsistencies and weirdness.

### Throwing exceptions

- Only throw exceptions in exceptional situations.
  - **Exceptional**: the database went down. This is exceptional because in general we expect databases to stay up.
  - **Not exceptional**: the user entered an incorrect password. This is not exceptional because users are expected to typo their password.
- Do use exceptions to control flow.

### Catching exceptions

_Only catch what you can handle._

- When catching an exception, be as specific as possible to the type of exception you are handling. Avoid catching `System.Exception` if you only plan to handle a `System.IO.FileNotFoundException`.
- Do not catch certain type of exception unless you have good reason to believe it may be thrown to you.
- Do not catch an exception unless you have a good reason to do so. Such reasons might be:
   - recovering from the error
   - enriching the error message / wrapping the exception
   - retrying the operation
   - logging the error
- There should always be a global exception handler that logs any unhandled exceptions and shows a "sorry, something went wrong" message. If all your catch block does it to log and rethrow, then don't bother - keep your code clean and leave the logging to the global exception handler.

### Swallowing exceptions

_Don't. Unless you know what you are doing. But even then, probably don't._

- When you catch an exception but don't rethrow it, you have _swallowed_ the exception. As far as the rest of the system is concerned, the exception never happened.
- The hardest exceptions to troubleshoot are the ones that don't even exist, because someone upstream swallowed it.
- To swallow an exception implies that you have completely recovered from the error, and the user can continue as if nothing ever happened. This is known as _graceful recovery_.
  - For example, if an exception is thrown when writing to a readonly file, then you can recover gracefully by removing the readonly attribute and retrying. In this case, the user doesn't need to know that anything went wrong, because you were able to completely recover from error condition.
- It is useful to differentiate between critical and non-critical parts of your page. Rule of thumb: without a critical part, the page is useless. Without a non-critical part, the page can still be used, though potentially with a degraded UX.
  - Do not catch exceptions that occur during generation of critical parts. Leave them to the global exception handler and show a generic 500 page.
  - Consider swallowing exceptions when generating non-critical parts. Adjust the UI to indicate the missing data.

### Rethrowing exceptions

_It's not your problem._

- If you cannot gracefully recover from an exception, _it's totally fine_! Exceptions should be, by nature, _exceptional_, so you can't be expected to handle every potential edge case. Someone forgot to deploy the config file? The network went down? The datacenter was destroyed by a hurricane? [Christopher Hitchens](https://en.wikipedia.org/wiki/Christopher_Hitchens) rose from the dead? _It's not your problem._
  - Either: don't catch the exception in the first place.
  - Else: catch, handle (log?) and _rethrow_. Give someone further up the callstack the chance to handle it more appropriately.
- When _rethrowing_ an exception simply `throw;` it. Do not `throw ex;` as this loses the original call stack information, making it look like the exception originated inside your `catch` block.
- **Note that simply logging an exception does not count as graceful recovery. If in doubt, always rethrow.**

#### Don't

```c#
try
{
    return myService.GetData();
}
catch (Exception ex)
{
    Log.Error(ex.ToString());
    return null;
    // Now the caller has to deal with a myserious null value, with no 
    // indication that something went wrong.
}

```

#### Do

```c#
try
{
    return myService.GetData();
}
catch (MyServiceException ex)
{
    Log.Error(ex.ToString());
    throw; // note: *NOT* throw ex;
    // This time, the caller is notified that something went wrong, and can take 
    // steps to recover, or allow the exception to propagate up the stack. 
}

```

#### Even better

```c#
// Just leave the logging to the global exception handler.
return myService.GetData();
````
