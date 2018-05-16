# Static Dictionaries

From the msdn documentation

```
A Dictionary can support multiple readers concurrently, as long as the collection is not modified. Even so, enumerating through a collection is intrinsically not a thread-safe procedure. In the rare case where an enumeration contends with write accesses, the collection must be locked during the entire enumeration. To allow the collection to be accessed by multiple threads for reading and writing, you must implement your own synchronization.
```

In other words only use read only implementations, or better yet try the immutable types that were release with dot net 4.5

A good blog post with some details is [here](https://blogs.msdn.microsoft.com/tess/2009/12/21/high-cpu-in-net-app-using-a-static-generic-dictionary/), the result you will get in production is high CPU.

# When a readonly static dictionary is not readonly

There is an instance where you can write code that might not seem like its safe but its not.

In the example below the dictionary is read only but it is accessed from the constructor of an object that is instantiated. So as multiple objects of type InternalDeserializer are created in separate threads, they all share the same _mapping objects. So we run into the same high CPU issues in production as above.

In short avoid using static objects unless you know what you are doing.

```csharp
internal partial class InternalDeserializer
{
        private static readonly Dictionary<Type, Func<Stream, object>> _mapping;
        private static readonly Dictionary<Type, Func<byte[], object>> _bufferMapping;
        private static readonly ModelAdapter Adapter = new ModelAdapter();

        public InternalDeserializer()
        {
            // For GeoLocationInfoList
            if (_mapping.ContainsKey(typeof(GeoLocationInfoListProto)))
            {
                _mapping.Remove(typeof(GeoLocationInfoListProto));
                _mapping.Add(typeof(GeoLocationInfoListProto),
                    s =>
                    {
                        var parser = GeoLocationInfoListProto.Parser;
                        using (var codedInputStream = CodedInputStream.CreateWithLimits(s, int.MaxValue, int.MaxValue))
                        {
                            return parser.ParseFrom(codedInputStream);
                        }   
                    });
            }
        }
    }
```

# How and when to use a parallel for loop

Be careful when using these as they can take over a servers CPU resources fast. Out of the box they will optimise of a number executing threads per core, and they make good use of async, so will be handling back and waiting threads and starting new ones. Below is from MSDN doc:

```
For efficient use of hardware resources, the number of tasks is often greater than the number of available cores. For example, parallel loops may use additional tasks in cases where there are blocking I/O operations that do not require processor resources to run. The degree of parallelism is automatically managed by the underlying components of the system; the implementation of the Parallel class, the default task scheduler, and the .NET thread pool all play a role in optimizing throughput under a wide range of conditions.

You can limit the maximum number of tasks used concurrently by specifying the MaxDegreeOfParallelism property of a ParallelOptions object.
```

The best practice is to limit the number of threads. You should be aware of how many cores your production machines are roughly, so limit your for loops to a fraction fo these. i.e. if you prod boxes have 16 cores, then limit these to 4 threads might be a good option. It depends as well on the number of users you have. So be aware that you can potentially kill the server capacity when using these.

```csharp
var n = ...
var options = new ParallelOptions() 
                      { MaxDegreeOfParallelism = 4};
Parallel.For(0, n, options, i =>
{
  // ... 
});
```

