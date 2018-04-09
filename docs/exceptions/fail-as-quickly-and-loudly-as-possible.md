## Fail as quickly and loudly as possible

- We can only fix the problems we know about. Therefore, don't be tempted to swallow exceptions just to keep the site limping along. This can lead to long-term difficult-to-diagnose inconsistency and weirdness.

### Don't

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

### Do

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

### Even better (for critical data)

```c#
// If the data is critical to the page - in other words, the page is useless without it, just bubble it up to the global exception handler which will log it and show a generic 500 page.
return myService.GetData();
````