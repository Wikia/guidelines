# Introduction

This document describes the consul usage and service discovery guidelines. It
should be considered a work in progress that will evolve as we gain more
experience with running consul in production. If you have any questions please
use the Wikia #consul chat channel.

## Introduction to Consul and Service Discovery

For an introduction to consul please read the [documentation
provided](https://www.consul.io/intro/) by the creators of consul.

Services can be "discovered" via DNS or the HTTP API. As an example, consider a
query for a discussion service node. When using the DNS interface one would
query for `discussion.service.sjc.consul`. Below is an example:

```
dig @localhost -p 8600 prod.discussion.service.sjc.consul
```

The answer section of the above query will provide the IP addresses of instances
that have registered themselves as `discussion` "services", have the tag
"prod", and are healthy.

If you want to also query for the port that the service is listening one
add `SRV` to the end of the above `dig` query.

Consul DNS addresses take the form `TAG.SERVICE.service.DATACENTER.consul`.

A similar request for "healthy" instances of the discussion service could query
the HTTP API using the following:

```
curl 'http://consul.service.sjc.consul:8500/v1/health/service/discussion?passing&tag=prod'
```

The `passing` query parameter will limit the output to "healthy" services. The
`tag` parameter can be used to filter by a tag.

# Guidelines

## Configuration Schema

In this section DNS is used for illustration purposes. However, the guidelines
apply generally for all services configured in consul.

### `TAG`

Tags are reserved for attributes that are specific to a set of registered
instances of a service.

Services should expose at least one tag and clients of that service should
*ALWAYS* use that tag when requesting the service. For example, to query the
production discussion service you would use
`prod.discussion.service.consul`.

### `SERVICE`

Service names should be descriptive and consistent with the deployed instances
of the service. For example, if the service is called `user-preference` then the
`SERVICE` portion of the address should be `user-preference`.

Service dependencies (e.g. a database cluster) should use the form
`TAG.{type}-{service}` where `{type}` is a string that clearly defines the
dependency provided by the service. For example the database cluster (shortened
to `db`) used by the discussion service would be identified by
`prod.db-discussion.service.consul.`

### `DATACENTER` and Environment

The `DATACENTER` portion of the consul address referrers to the _physical_
datacenter where the service resides. There may _not_ be a 1:1 relationship
between `DATACENTER` and environment.

Note also that the `DATACENTER` portion of an address can also be omitted.
The datacenter defaults to the location where the local consul agent is bound.

In practice the `DATACENTER` should normally be omitted to ensure that requests
are contained within the same datacenter where the running service.

### Service Registries

Service registries should be provided using the [consul key
value data](https://www.consul.io/intro/getting-started/kv.html). For example,
to create a registry of services to be included in the
[API gateway](https://github.com/Wikia/api-gateway) the following key-value tree
should be created:

```
/DATACENTER/kv/registry/prod/api-gateway
  discussion => prod.discussion
  user-preference => prod.user-preference
  auth => prod.helios
  ...
```

In production environments there should be one *and only one* entry per service.
Aliases are ok (see the `auth` entry above).

In a development environment this might look like the following:

```
/DATACENTER/kv/registry/dev/api-gateway
  discussion => dev.discussion
  artur-helios => artur.helios          # a dev box registered for the service
```

In environments other than production the restriction of one entry per service
can be relaxed.

The API gateway gateway can then traverse this tree to create mappings between
the service names and the physical `address:port` tuples that identify each
instance in the desired environment.

#### Internal-only Resources

If a service needs to separate internal and external resources they can do so by
running a server on a separate port and registering it explicitly with consul
using the `admin.prod` tag (as an example).
