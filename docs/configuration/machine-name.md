## Do not use `MachineName`


- Use of `MachineName` tightly couples your code to the our infrastructure and its naming scheme, which can and will change over time.
- Your code should be agnostic of environment, data center, cluster and server. Having different code paths for different environments can lead to bugs that can only be caught in production.
- Such environmental variations are usually only required when calling external services, as you will want to call the service running in your local data center. For this, use [Consul's service discovery](service-discovery.md). It automatically supplies the correct configuration based on your environment.

One exception is logging, where it can be useful to see the exact DC / cluster / server that made the request. Here, it makes sense to log the machine name.