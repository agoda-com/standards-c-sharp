---
name: csharp-configuration
description: >-
  C# application configuration management standards using Consul. Covers config
  files, Consul key/value store, service discovery, machine name usage, what to
  make configurable. Use when setting up application configuration, deciding
  where to store settings, working with Consul, or managing environment-specific
  config in C#/.NET projects.
---

# C# Configuration Standards

## Config files: only for Consul bootstrap and secrets

For new projects, `web/app.config` should only contain:

- Settings to connect to Consul
- Sensitive data not allowed in Consul (e.g., production passwords)

All other settings go in Consul. Existing projects should consider migrating.

## Use Consul for configuration

Consul is deployed in all datacenters for application settings. Use it for all new applications. Use the `config-consul` library for simplified bootstrapping, configuration hierarchy, audit trail, and resilience features.

## Consul Key/Value store

Use for anything that is not:

- A server IP/hostname (use service discovery instead)
- Sensitive data like production passwords (use config files)

## Use Consul service discovery for server addresses

Server IPs and DNS entries must use Consul's service discovery feature, not the Key/Value store.

## Do not use `MachineName`

`MachineName` couples code to infrastructure naming which can change. Code should be environment/DC/cluster/server agnostic. Use Consul service discovery for environment-specific configuration.

**Exception**: logging, where machine name helps identify the exact server.

## If unlikely to change, don't make it configurable

Values that won't change across environments or time should be constants, not config.

```c#
// Don't — in config file
<add key="ssrPageUrl" value="/{0}pages/agoda/default/DestinationSearchResult.aspx?asq={1}" />

// Do — as constant
public const string SSR_PAGE_URL = "/{0}pages/agoda/default/DestinationSearchResult.aspx?asq={1}";
```
