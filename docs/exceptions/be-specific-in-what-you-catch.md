## Be specific in what you catch

- When catching an exception, be as specific as possible to the type of exception you are handling. Avoid catching `System.Exception` if you only plan to handle a `System.IO.FileNotFoundException`.

### Don't

```c#
try
{
    OpenFile("myfile.txt");
}
catch (System.Exception)
{
    // some generic handling code
}

```

### Do

```c#
try
{
    OpenFile("myfile.txt);
}
catch (System.IO.DriveNotFoundException)
{
    // inform the user they specified an invalid drive
}
catch (System.IO.FileNotFoundException)
{
    // create the file and retry
}
// etc.

```