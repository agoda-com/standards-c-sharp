## Make use of the global exception handler

- This exception handler wraps your entire application and catches any unhandled exceptions.
- At a minimum it should probably:
  - log the exception,
  - show a 500 error page.
- If this is your desired behavior in case of exception, _do not catch the exception_ - keep your code clean and allow the exception to bubble up to the global handler.

### Don't

```c#
try
{
    MyCriticalOperation();
}
catch (AgodaIsDownException ex)
{
    Log.Error(ex); // the global exception handler will do this anyway
    throw;
}
```

### Do

```c#
// simply:
MyCriticalOperation();

```