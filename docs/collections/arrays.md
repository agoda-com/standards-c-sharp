## Don't use `Array`... almost ever

- No `public` method may return an `Array`, with the exception of a `byte[]` when used to represent arbitary binary data.
- There is [almost always a better choice of collection](choosing-collection-implementation.md) than `Array`.

> Arrays simply do not model any problem that I have at all well – I rarely need a collection which has the rather contradictory properties of being completely mutable, and at the same time, fixed in size. If I want to mutate a collection it is almost always to add something to it or remove something from it, not to change what value an index maps to. 
>
>  We have a class or interface for everything I need. If I need a sequence I’ll use `IEnumerable<T>`, if I need a mapping from contiguous numbers to data I’ll use a `List<T>`, if I need a mapping across arbitrary data I’ll use a `Dictionary<K,V>`, if I need a set I’ll use a `HashSet<T>`. I simply don’t need arrays for anything, so I almost never use them. They don’t solve a problem I have better than the other tools at my disposal.
> 
> [Eric Lippert - Arrays considered somewhat harmful](https://blogs.msdn.microsoft.com/ericlippert/2008/09/22/arrays-considered-somewhat-harmful/)

- Only use Arrays internally, and only then for performance critical code that requires it, eg. probably never:

> I make a **lot** of use of arrays in my [protobuf-net](https://github.com/mgravell/protobuf-net) project; entirely for performance:
>
> - it does a lot of bit-shifting, so a `byte[]` is pretty much essential for encoding;
> - I use a local rolling `byte[]` buffer which I fill before sending down to the 
> underlying stream (and v.v.); quicker than `BufferedStream` etc;
> - it internally uses an array-based model of objects (`Foo[]` rather than `List<Foo>`), since the size is fixed once built, and needs to be very fast.
>
> But this is definitely an exception; for general line-of-business processing, a `List<T>` wins every time.
>
> [Marc Gravell - StackOverflow](https://stackoverflow.com/a/434765/289319)