### Using reference types as keys

Note that any object used as a key (eg. in `HashSet<T>.Add()`) must have a suitable implementation of `Object.GetHashCode()` and should override `Object.Equals()` as described [here](https://stackoverflow.com/a/371348/289319).
  - If you're using .NET Core then you can easily generate reliable hashcodes using [`System.HashCode`](https://docs.microsoft.com/en-us/dotnet/api/system.hashcode?view=netcore-2.1).
  - If not, then you can copy it into your project from the [.NET Core source](https://github.com/dotnet/corefx/blob/master/src/Common/src/CoreLib/System/HashCode.cs).