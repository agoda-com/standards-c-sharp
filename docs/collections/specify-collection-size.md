## Specify a collection size when it is know

- Most of .NET common collections like `List<>` use arrays internally with a default capacity of 4.
- When you add more items, the `List` creates a new array with double items count and copies the old array to the new one.
- If the list is large and you can predict the size of the collection at compile time, then you may be able to prevent unnecessary allocations and GCs by specifying the capacity up front.

#### Don't

```c#
var endpoints = new List<string>();
endpoints.Add("192.168.1.1");
endpoints.Add("192.168.1.2");
endpoints.Add("192.168.1.3");
// ...
endpoints.Add("192.168.1.253");
endpoints.Add("192.168.1.254");
endpoints.Add("192.168.1.255");
```

#### Do

```c#
var endpoints = new List<string>(255); // we know up front there will be 255 endpoints specified
endpoints.Add("192.168.1.1");
endpoints.Add("192.168.1.2");
endpoints.Add("192.168.1.3");
// ...
endpoints.Add("192.168.1.253");
endpoints.Add("192.168.1.254");
endpoints.Add("192.168.1.255");
```