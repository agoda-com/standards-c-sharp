## Choosing the correct general collection implementation

The following chart shows how to decide which general collection type to use for your implementation, [internal  exposure](non-public-api-types.md) and [public API exposure](public-api-types.md):

![Collection type flowchart](https://drive.google.com/uc?id=17-qROxVsHI4MTV5tBdil9NyhXnjcNVkz)
<!-- draw.io source in https://drive.google.com/open?id=1YnYEfisi7DYOT3IwYQwWbyCtzFP7prK3 -->

### Using objects as keys

Note that any object used as a key (eg. in `HashSet<T>.Add()`) must have a suitable implementation of `Object.GetHashCode()` and should override `Object.Equals()` as described [here](https://stackoverflow.com/a/371348/289319).
  - If you're using .NET Core then you can easily generate reliable hashcodes using [`System.HashCode`](https://docs.microsoft.com/en-us/dotnet/api/system.hashcode?view=netcore-2.1).
  - If not, then you can copy it into your project from the [.NET Core source](https://github.com/dotnet/corefx/blob/master/src/Common/src/CoreLib/System/HashCode.cs).
  
### Thread safety

The concrete types above can be have multiple readers, but are not thread-safe for mutations. Some have thread-safe analogs.

- `List<T>` => [`SynnchronizedCollection<T>`](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.synchronizedcollection-1?view=netframework-4.7.1)
- `Dictionary<K,V>` => [`ConcurrentDictionary<K,V>`](https://docs.microsoft.com/en-us/dotnet/api/system.collections.concurrent.concurrentdictionary-2?view=netframework-4.7.1)
- `HashSet<T>` => No direct analog. You could [create your own](https://stackoverflow.com/a/11034999/289319).
- `SortedSet<T>` => No direct analog. You could try [`SortedList.Synchronized(myUnsynchronizedSortedList)`](https://docs.microsoft.com/en-us/dotnet/api/system.collections.sortedlist.synchronized?view=netframework-4.7.1#System_Collections_SortedList_Synchronized_System_Collections_SortedList_).
- `SortedDictionary<T>` => No direct analog. Write your own synchronized wrapper.
- `KeyedCollection<T>` => No direct analog. Write your own synchronized wrapper.