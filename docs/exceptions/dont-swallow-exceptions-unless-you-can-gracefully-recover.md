## Don't swallow exceptions unless you can gracefully recover

- When you catch an exception but don't rethrow it, you have _swallowed_ the exception. As far as the rest of the system is concerned, the exception never happened.
- The hardest exceptions to troubleshoot are the ones that don't even exist, because someone upstream swallowed it.
- To swallow an exception implies that you have completely recovered from the error, and the user can continue as if nothing ever happened. This is known as _graceful recovery_.
  - For example, if an exception is thrown when writing to a readonly file, then you can recover gracefully by removing the readonly attribute and retrying. In this case, the user doesn't need to know that anything went wrong, because you were able to completely recover from error condition.
- It is useful to differentiate between [critical and non-critical exceptions](critical-vs-non-critical-exceptions.md) when generating your page.
  - Do not catch critical exceptions. As your page cannot be used, you can leave the exception to the global exception handler, which will show a generic 500.
  - Consider swallowing (and logging) non-critical exceptions. Adjust the UI to indicate the unavailable data, but still allow the user to interact. 
- Logging an exception alone does not count as graceful recovery. If in doubt, always rethrow, or at least return something meaningful to indicate the error.

### Do

```c#
var file = File.Open("myfile.txt");
try
{
    AppenData(file);
}
catch (System.UnauthorizedAccessException) // file is ReadOnly
{
    FileInfo fileInfo = new FileInfo(pathToAFile);
    fileInfo.IsReadOnly = false;
    AppendData(file);
    // we gracefully recovered, so the exception can be swallowed
}

```
