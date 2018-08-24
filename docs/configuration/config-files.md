## Use config files only for Consul client settings and sensitive data

- For new projects, you web/app.config file should only contain:
   - The basic settings to allow your application to connect to Consul.
   - Sensitive data which we do not allow to be stored in Consul, such as production passwords.
- All other settings should be stored in Consul itself.
- For existing projects, consider migrating to Consul.
- [What kind of values should I make configurable?](unlikely-to-change.md) 