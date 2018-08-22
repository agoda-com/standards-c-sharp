## Config files should only be used for Consul client settings

- For new projects, you web/app.config file should only contain the basic settings to allow your application to connect to Consul. All other settings must be stored in Consul itself.
- For existing projects, consider migrating settings to Consul.
- [What kind of values should I make configurable?](unlikely-to-change.md) 