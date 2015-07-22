# Introduction

This document describes the consul usage and service discovery guidelines. This
document is sure to evolve as we gain more experience with running consul in
production. If you have any questions please use the Wikia #consul chat channel.

## Introduction to Consul and Service Discovery

For an introduction to consul please read the [documentation
provided](https://www.consul.io/intro/) by the creators of consul. Read that
first.

Services can be "discovered" via DNS or the HTTP API. As an example, consider a
query for a discussion service node. When using the DNS interface one would
query for `discussion.service.sjc.consul`. Below is an example:

```
dig @localhost -p 8600 discussion.service.sjc.consul
```

The answer section of the above query will provide the IP addresses of instances
that have registered themselves as `discussion` "services" and are healthy. 

If you want to also query for the port that the service is listening one
add `SRV` to the end of the above `dig` query.

Consul DNS addresses take the form `TAG.SERVICE.service.DATACENTER.consul`.

A similar request for "healthy" instances of the discussion service could query
the HTTP API using the following:

```
curl 'http://consul.service.sjc.consul:8500/v1/health/service/discussion?passing'
```

The `passing` query parameter will limit the output to "healthy" services.

# Guidelines

## DNS Schema

### `TAG`

Tags are reserved for attributes that are specific to a set of registered
instances of a service. Below are some examples of how tags might be used to
distinguish between instances.

 * `api` this instance of the service provides an HTTP API to access the
   service.
 * `admin.api` [note that the tag includes the `.`] identifies an administrative
   API that is not exposed to the public.
 * `db` this instance provides persistence for the service.
 * `task` this instance of the service provides task queue workers.

Tags are *NOT* to be used to specify the environment or the datacenter. 

### `SERVICE`

Service names should be descriptive and consistent with the deployed instances
of the service. For example, if the service is called `user-preference` then the
`SERVICE` portion of the address should be `user-preference`.

### `DATACENTER` == Environment

The `DATACENTER` portion of the consul address is equivalent to the
environment and should be of the form `<REGION>-<ENVIRONMENT>` (e.g. `sf-dev`
where `sf` is the region and `dev` is the environment). If the environment is
omitted `prod` is assumed.

Note also that the `DATACENTER` portion of an address can also be omitted. In
which case the datacenter the local agent is bound to is assumed.

In practice the `DATACENTER` should normally be omitted to ensure that requests
are contained within the same environment as the running service.

### Service Registries

Service registries should be provided using the [consul key
value data](https://www.consul.io/intro/getting-started/kv.html). For example,
to create a registry of services to be included in the
[API gateway](https://github.com/Wikia/api-gateway) the following key-value tree
should be created:

```
/DATACENTER/kv/registry/api-gateway
  discussion => api.discussion
  user-preference => api.user-preference
  helios => api.helios
  ...
```

In production environments there should be one *and only one* entry per service.
Aliases are ok (see the `auth` entry above).

In a development environment this might look like the following:

```
/DATACENTER-dev/kv/registry/api-gateway
  discussion => api.discussion
  artur-helios => artur.helios          # a dev box registered for the service
```

In environments other than production the restriction of one entry per service
can be relaxed.

The API gateway gateway can then traverse this tree to create mappings between
the service names and the physical `address:port` tuples that identify each
instance.

#### Internal-only Resources

If a service needs to separate internal and external resources they can do so by
running a server on a separate port and registering it explicitly with consul
using the `admin.api` tag (as an example).
