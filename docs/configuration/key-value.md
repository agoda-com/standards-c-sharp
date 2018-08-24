## Use Consul's Key/Value store for anything else

- Use Consul's Key/Value store for anything that is not:
  - a server IP address or hostname (use its [service discovery](service-discovery.md) feature instead)
  - sensitive data such as a production password - these should be stored in a [config file](config-files.md)
- [What kind of values should I make configurable?](unlikely-to-change.md)