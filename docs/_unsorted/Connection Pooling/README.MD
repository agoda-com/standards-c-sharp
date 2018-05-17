#SQL Connection Pooling in dotnet

Out of the box SQL connection object in dot net will persist a series of connections to a SQL server. So the Connection.Close() method you are using isn't actually closing a connection, its just handing it back to a pool to be reused. This can be disabled but I would not recommend this as its great for performance, but it does come with a few pit falls.

Full article on MSD with more details [here](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/sql-server-connection-pooling)

The pitfalls are that the connections in the pool persist the transaction isolation level, so if you use 

```sql
SET TRANSACTION ISOLATION LEVEL Serializable
```

Then the next sql command to pull this connection from the pool will also use the same isolation level, which may cause you issues if you don't want locking. Stack Overflow post with details [here](https://stackoverflow.com/questions/9851415/sql-server-isolation-level-leaks-across-pooled-connections)

This was fixed in 2014 then unfixed in sp1 for 2014, and is still current.

# HTTP Client and connection Pooling

The Dotnet HttpClient object persists connections in the background to optimise performance. But sometimes it gets things wrong so to use it correctly you need to take more control over it.

A complete article ont eh issues is [here](https://aspnetmonsters.com/2016/08/2016-08-27-httpclientwrong/)

But the basics of it is this, simply disposing of the client liek this

```csharp
using(var client = new HttpClient())
{
    //do something with http client
}
```

Will NOT close any open connections, instead they will remain open as under the hood the HttpClient is reentrant. So recreating the client like the above can end you up with a lot of open connections as sometimes they dont get reused correctly.

The correct way to use it is to create a client that you can share between operations. But you should still dispose it after use. 

Recommendations from Microsoft on the use are [here](https://blogs.msdn.microsoft.com/shacorn/2016/10/21/best-practices-for-using-httpclient-on-services/)

However this guys will not respect [DNS changes](http://www.nimaara.com/2016/11/01/beware-of-the-net-httpclient/), so if you are using DNS for fail over you should not do this.



# Default limits for threading, connections, etc

In framework 2.0 dotnet/ASP.NET and a lot of limits place on threads, connections, size, etc. mainly in WCF but also on the framework itself to try to prevent bad programing. A lot of this was removed out with the 4.0 release. 

## HTTP

The HttpClient in dotnet underlying uses service point manager, which had a default connection limit property of [2 per domain](https://docs.microsoft.com/en-us/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit?view=netframework-4.7) or 10 in an ASP.NET environment as of framework 4.7. 

However based on the version and patch level this has been been going up and down since 4.5.

If you are using a service that is sending a lot of small requests back and forth you may want to consider disabling [Nagle Algorithm](https://alexandrebrisebois.wordpress.com/2013/03/24/why-are-webrequests-throttled-i-want-more-throughput/)


## Threads

On framework 2.x and below (Including 3.5) IIS/ASP.NET had a thread limit set by a registry setting (which defaulted to 12) times the number of cores in the machine. In 4.0 and IIS >7.0 this was changed to a flat 5,000 threads per core.

## IIS

The default limits to be aware of in IIS are

- Connection Time out 2minutes
- Max connections 4,294,967,295
- Queue Length 1000 - The Queue Length is the maximum number of requests that will be queued, when exceeded new requests will receive a 503 “Service Unavailable” response.


Note: if you are using IIS on Windows Desktop or IIS Express you are using a cut down version of IIS that has far lower limits, please do not do any form of capacity testing or load testing on you development workstation as teh results will be useless.
