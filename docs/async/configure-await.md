## Correct use of `ConfigureAwait(false)`

- In legacy ASP.NET, if you _must_ block on an asynchronous call, use `ConfigureAwait(false)` to avoid deadlocks.
- In .NET Core, do not use `ConfigureAwait(false)` as it has no effect.
- To emphasize: using `ConfigureAwait(false)` to avoid deadlocks should be considered a hack. If possible, rewrite the code so it doesn't block.
- Further reading:
  - [https://medium.com/bynder-tech/c-why-you-should-use-configureawait-false-in-your-library-code-d7837dce3d7f](https://medium.com/bynder-tech/c-why-you-should-use-configureawait-false-in-your-library-code-d7837dce3d7f)
  - [https://stackoverflow.com/questions/13489065/best-practice-to-call-configureawait-for-all-server-side-code](https://stackoverflow.com/questions/13489065/best-practice-to-call-configureawait-for-all-server-side-code)