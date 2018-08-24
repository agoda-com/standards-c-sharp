## Using Consul

[https://www.consul.io/](https://www.consul.io/)

- Agoda has deployed Consul in all datacenters to manage application settings.
- It should be used for all new applications that require configuration.
- Existing applications should consider migrating.
- [What kind of values should I make configurable?](unlikely-to-change.md)

### Use Agoda's config-consul library to interact with Consul

[https://github.agodadev.io/agoda-front-end/config-consul](https://github.agodadev.io/agoda-front-end/config-consul)

Agoda has a convenience library that wraps and extends Consul's functionality. Its features include:
- Simplified bootstrapping and configuration
- Opiniated configuration hierarchy that corresponds Agoda's infrastructure
- Audit trail
- Resilience and fallback features