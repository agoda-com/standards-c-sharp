## There should always be a global exception handler

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
    Log.Error(ex); 
    throw;
}
```

### Do

```c#
// Don't handle the exception at all since the global exception handler will give you the same hebavior as above.
MyCriticalOperation();

```