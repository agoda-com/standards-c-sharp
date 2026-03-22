---
name: csharp-exception-handling
description: >-
  C# exception handling, throwing, catching, and rethrowing standards. Covers
  catch specificity, rethrowing with stack trace preservation, exception
  wrapping, control flow anti-patterns, critical vs non-critical exceptions.
  Use when writing try/catch blocks, throwing exceptions, handling errors, or
  designing error handling strategies in C#.
---

# C# Exception Handling Standards

## Be specific in what you catch

Catch the most specific exception type possible. Never catch `System.Exception` when you only handle a specific scenario.

```c#
// Don't
catch (System.Exception) { }

// Do
catch (System.IO.FileNotFoundException) { }
catch (System.IO.DriveNotFoundException) { }
```

## Only catch exceptions with good reason

Valid reasons to catch:

- Gracefully recovering from the error
- Enriching the error message / wrapping the exception
- Retrying the operation
- Logging (only if the global handler won't already log it)

Don't catch just to log and rethrow — the global exception handler already logs.

## Only throw in exceptional circumstances

- **Exceptional**: the database went down
- **Not exceptional**: user entered wrong password

Don't use exceptions for control flow — they are effectively `GOTO` statements.

```c#
// Don't — exceptions as control flow
try { _authService.LogUserIn(username, password); }
catch (InvalidCredentialsException) { /* show message */ }

// Do — use return values
var result = _authService.LogUserIn(username, password);
if (!result.IsAuthenticated) { /* show message */ }
```

## How to rethrow

Use `throw;` not `throw ex;` — the latter loses the original stack trace.

```c#
// Don't
catch (MyServiceException ex) { throw ex; }

// Do
catch (MyServiceException) { throw; }

// Do — when wrapping with a more specific exception
catch (MyServiceException ex)
{
    throw new MyApplicationException("Context message")
    {
        InnerException = ex
    };
}
```

## Rethrow when you cannot gracefully recover

If you can't handle it, either don't catch it or catch, handle partially (e.g. log), and rethrow.

## Don't swallow exceptions

Never catch an exception and silently ignore it unless you can genuinely recover and the failure is non-critical. An empty catch block hides bugs.

## Fail fast and loudly

Invalid state or configuration errors should surface immediately at startup, not silently degrade at runtime.

## Critical vs non-critical exceptions

- **Critical**: page is useless without this feature (Google homepage without the search box)
- **Non-critical**: degraded but usable experience (Google homepage without "I'm Feeling Lucky")

Use a global exception handler to catch unhandled exceptions and return appropriate error responses.
