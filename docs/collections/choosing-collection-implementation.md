## Choosing the correct type for an enumerable

### Supported types

**Only** the following enumerable types may be returned from public methods:

- `IEnumerable<T>`
- `ISet<T>`
- `IList<T>`
- `IDictionary<K, V>`
- `IReadOnlyDictionary<K, V>`
- `KeyedCollection<T>`
- `byte[]` (special case for [raw binary data](arrays.md))

### Which one should I use?

The following chart shows how to decide which general collection type to use for your implementation, [internal  exposure](non-public-api-types.md) and [public API exposure](public-api-types.md):

![Collection type flowchart](https://drive.google.com/uc?id=17-qROxVsHI4MTV5tBdil9NyhXnjcNVkz)
<!-- draw.io source in https://drive.google.com/open?id=1YnYEfisi7DYOT3IwYQwWbyCtzFP7prK3 -->
  
### Thread safety

The concrete types above can be have multiple readers, but are not thread-safe for mutations. Some have thread-safe analogs.

- `List<T>` => [`SynchronizedCollection<T>`](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.synchronizedcollection-1?view=netframework-4.7.1)
- `Dictionary<K,V>` => [`ConcurrentDictionary<K,V>`](https://docs.microsoft.com/en-us/dotnet/api/system.collections.concurrent.concurrentdictionary-2?view=netframework-4.7.1)
- `HashSet<T>` => No direct analog. You could [create your own](https://stackoverflow.com/a/11034999/289319).
- `SortedSet<T>` => No direct analog. You could try [`SortedList.Synchronized(myUnsynchronizedSortedList)`](https://docs.microsoft.com/en-us/dotnet/api/system.collections.sortedlist.synchronized?view=netframework-4.7.1#System_Collections_SortedList_Synchronized_System_Collections_SortedList_).
- `SortedDictionary<T>` => No direct analog. Write your own synchronized wrapper.
- `KeyedCollection<T>` => No direct analog. Write your own synchronized wrapper.