# TL;DR

* REST APIs are not explicitly versioned.
* Clients are responsible for ensuring forwards compatibility with future versions of services.
* Services are responsible for maintaining backwards compatibility of published APIs.
* Follow Postel’s Law. Be conservative in what you send, and liberal in what you accept from others.

# Table of Contents

* [Goals](#goals)
* [API Versioning](#api-versioning)
* [Responsibilities of Third-Party Clients](#responsibilities-of-third-party-clients)
* [Responsibilities of Wikia-Authored Clients](#responsibilities-of-wikia-authored-clients)
* [Responsibilities of API Developers](#responsibiltiies-of-api-developers)
* [A Note on HATEOAS](#a-note-on-hateoas)

# Goals

We want to ensure the following:

* Services can be updated independently, at any time
* Multiple versions of the same service can run side-by-side (during upgrades, downgrades or during A/B testing)
* API Clients are easy to develop and maintain

We want to avoid the following:

* Production outages due to API changes
* Dependencies between upgrades
* Breaking external clients, both official and unofficial

# API Versioning

APIs should not put any version information in their URI. The only exception to this rule is if the API is a transparent proxy for a third-party API that already does not follow this rule.

Placing the version number in the API encourages backwards-incompatible changes through the draconic measure of copying the entire API, _including all the resources that weren't changed_ to a new location. This in turn creates the additional development, testing and support burden of having to maintain independently a stack of redundant APIs. Eventually the decision is made to limit maintenance costs by sunsetting old versions, and clients that would never have broken because the APIs they relied on were not the ones that caused the version bump, cease to work for no good reason.

This complexity only increases when an API is composed from a number of cooperating services, and we would have to choose between clients having to track independent versions both of multiple sub-APIs (and the versions of dependencies between those APIs), or us having to revise the global version of _every_ API whenever an individual service changes.

(All of this is also applies to performing version negotiation in HTTP headers.)

A much better approach is to do your best to ensure APIs are backwards compatible, and if it becomes necessary necessary, create new resources at new locations only for those things that actually changed.

# Responsibilities of Third-Party Clients

Many of the clients for our APIs will be developed outside our control. We define a reasonable minimum set of expectations for third-party clients to place an upper bound on the effort required to support them as we evolve our APIs. These requirements should be included as part of any external API documentation.

## Clients should conform to the HTTP Spec

While the whole spec is important, it is worth calling out specific common cases where we expect clients to conform: 

* Clients should follow HTTP-level redirects when a resource has moved to a new location.
* Clients should obey any caching directives returned with a resource
* If a client actively engages in content or language negotiation, it should account for the possibility that a new representation of the resource will become available in the future that is a better match.

## Clients should use appropriate parsers for the content they receive.

[Do not parse XHTML with regular expressions](http://stackoverflow.com/questions/1732348/regex-match-open-tags-except-xhtml-self-contained-tags/1732454#1732454).

## Clients should ignore parts of a response or representation they do not explicitly expect

When a REST resource returns structured information, new elements may be added to that structure at any time. Clients should expect and ignore this, and constrain themselves to looking for the information they expect.

## Clients should handle unexpected values in data gracefully, so long as the structure and type of that data remains the same.

For example, say a REST resource returns:

```json
    {color: "red"}
```

Even if the only (currently) documented colours are "red", "green" and "blue", the client should anticipate the possibility of the value being _any_ legal JSON string, including "purple", "taupe" or "My Cousin Vinny".

## External clients will more often than not be somebody's quick hack

While we necessarily make the above assumptions about third-party clients, we recognise that they're not going to be perfect:

* They will generate URIs using string concatenation even when the URI structure is not documented
* They will not be updated while they still work
* If they break, they are just as likely to be abandoned as updated

# Responsibilities of Wikia-Authored Clients

While we can not control the quality of third-party clients, we _can_ control the quality of our own code. By holding ourselves to higher standards we give ourselves more flexibility to evolve APIs and services that are only used internally.

## All "should" requirements for third-party clients are "MUST" requirements for internal clients.

While third-party developers shouldn’t write code that breaks when a conforming service is upgraded, we mustn't.

## Clients should not make assumptions about an API's URI structure

Clients should not engage in "URI guessing" by using manual URI generation to reach resources unless doing so is explicitly documented as how the API is intended to be used. Whereever possible, clients should discover resource by following links from within the API itself (see the section below on HATEOAS).

# Responsibilities of API Developers

Where an API is made available to the public, developers are responsible for ensuring any client that obeys the rules for third-party clients will continue to function indefinitely.

Where an API is only made available internally, developers are responsible for ensuring any client that obeys the rules for first-party clients will continue to function unless it can be demonstrated that such clients have been decomissioned.

Note that currently, _all_ APIs available beyond the firewall are technically available to the public.

## Backwards Compatibility Guidelines

This is not an exhaustive list, but should give developers a reasonable idea of what is, or is not permitted.

### Resource URIs and methods must remain stable

If a resource is moved from one URI to another, the old URI should return appropriate redirects for as long as clients are expected to access it. However, API authors should consider the additional latency cost to clients of following these redirects before moving a resource. 

If a resource accepts particular HTTP methods, these methods should continue to be supported, and perform the same actions for as long as clients are expected to access the resource.

### Do not create new required parameters or post/put data for existing resources

Adding new mandatory query parameters to a request, or adding new mandatory data requirements to a PUT or POSTed representation, will break existing clients that are not aware of the mandatory information. When adding new data or query parameters to a request, API developers should assume sensible defaults for clients that are not aware of the required information.

### Do not change the names or types of elements within structured data

When returning structured data to the client, the type of any element within that data must not change. For example, replacing a String with an Integer, or replacing a single element with a list.

### Do not change the meaning of elements within structured data

Consider the example:

```json
    {
        name: "George",
        color: "red"
    }
```

Adding a new color possibility of "blue" would not be changing the meaning of the color element. 

Reclassifying resources that used to be "red" as "reddish-brown" could be changing its meaning, as clients would be affected by existing resources being reclassified, and should only be done if the aim of the change is to stop those clients mis-classifying the resource as being red when it isn't.

Changing the value of color to "#FF0000" would be changing the meaning of the color element, as clients that were previously looking for "red" would start misrepresenting resources that were red as not being red.

Removing the color element entirely, even for resources that are red, would be changing its meaning. This is true even if the color element was optional in the response, as clients would assume such an entity was colorless.

# A Note on HATEOAS

HATEOAS is the unwieldy acronym for "Hypertext As The Engine Of Application State": a standard for writing dynamically discoverable REST API where every resource exposed by the API must be reachable by retrieving the API's root resource, then following a chain of hypertext links from there. It is _highly encouraged_ that REST APIs follow HATEOAS principles, because doing so allows for more resilient APIs and more manageable API migration, and frees clients from having to hard-code the URL structure of the service.

Internal clients of HATEOAS APIs must not assume any knowledge of the service's API structure that is not derived from the API itself. If a service moves resources to new locations, it should be able to expect that clients will adapt once their cached resources expire.

If the API is publicly availabe, services must not expect that third-party clients will follow HATEOAS principles.