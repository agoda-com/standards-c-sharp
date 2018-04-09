## Only throw exceptions in exceptional circumstances

- **Exceptional**: the database went down. This is exceptional because in general we expect databases to stay up.
- **Not exceptional**: the user entered an incorrect password. This is not exceptional because users are expected to typo their password at least 23 times a day.
- Or, to put it another way, do not use exceptions for control flow. When used like this, they are effectively `GOTO` statements and make the code harder to read.
  
### Don't

```c#
try
{
    _authenticationService.LogUserIn(username, password);
}
catch (InvalidCredentialsException)
{
    // show "Invalid credentials" message
}
```

### Do

```c#
var authResult = _authenticationService.LogUserIn(username, password);
// instead of using an exception to indicate (expected) failure, we use a control statement
if (!authResult.IsAuthenticated) 
{
    // show "Invalid credentials" message
}
```