# API Design Guide

## Contents

- [API Design Guide](#api-design-guide)
    - [Contents](#contents)
    - [Introduction](#introduction)
    - [On RESTfulness](#on-restfulness)
    - [Guidelines](#guidelines)
        - [Resources and Naming](#resources-and-naming)
        - [Representations and Media Types](#representations-and-media-types)
        - [Use Appropriate HTTP Status Codes](#use-appropriate-http-status-codes)
        - [Use HTTP Verbs to Manipulate Resources](#use-http-verbs-to-manipulate-resources)
        - [Use HTTP Headers for Additional Application Semantics](#use-http-headers-for-additional-application-semantics)
        - [Documentation](#documentation)
    - [Common Patterns](#common-patterns)
        - [Pagination](#pagination)
        - [Versioning](#versioning)
        - [Timestamps](#timestamps)
        - [Error Handling](#error-handling)
        - [Transclusion](#transclusion)
    - [Request Tagging](#request-tagging)
    - [Additional Resources](#additional-resources)
        - [Articles](#articles)
        - [API Design Guides](#api-design-guides)
        - [Books](#books)
    - [Notes](#notes)

## Introduction

This document is an API design guide for use at Wikia. The goal of this guide
is the creation of consistent, pragmatic APIs and reduced developer overhead.
The main focus of this guide will be HTTP APIs.

It should be noted that this guide may be based upon invalid or untested
assumptions. However until specific issues are surfaced as this is applied to
production the recommendations made below should be followed when building APIs.

## On RESTfulness

REST is the architecture of the web as described in the [PhD
dissertation](http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm) by Roy
Fielding in 2001. It informs much of the best practice around API design in the
industry.

REST is not an all or nothing architecture and APIs do not have to conform to
all of the REST constraints to be useful and valuable to the business.

## Guidelines

This section provides some general guidelines for API design. As a starting
point, APIs should be designed with the end user in mind-- the developer. The
goal is an interface that is intuitive, consistent, explorable, and pragmatic.

### Resources and Naming

Use plural nouns to identify resources. For example, use `/articles` and
`/comments` to address collections of articles and comments respectively. Use
`/articles/{article_name}` to address a specific article.

Use HTTP verbs (see below) to manipulate resources.

[Minimize path nesting for
resources](https://github.com/interagent/http-api-design#minimize-path-nesting).
For example, instead of addressing a comment via the article
`/articles/{article_name}/comments/{comment_id}` request the comment from the
root using `/comments/{comment_id}`.

Prefer synthetic IDs for individual resources over names. For example, if the 
URI of a user resource is `/users/username`, you will make it much harder to 
implement renaming of users in the future.

*Note*: Additional work is being done to identify the Wikia content ontology--
the concepts and nouns within the Wikia product domain. Additional information
and pointers will be provided when they become available.

*Clients should never depend on specific URIs or URI formats.* The only
exception to this is the entry point for the API. For example, an API client
should not rely on an image thumbnail format that they can manipulate to change
the size of the thumbnail. This creates a tight coupling between the client, the
URI format, and the server and breaks the client-server constraint of REST.

As a corollary to the above, all resources must be reachable by traversing links
from the root of the application.

### Representations and Media Types

Use JSON for state representations. Use
[`application/hal+json`](http://stateless.co/hal_specification.html) for the
media type.

One of the drawbacks to plain JSON is that there are no rules regarding the
structure or format of JSON messages. As a result, many APIs are snowflakes that
require custom clients and documentation. This can be avoided by using a
structured JSON message format such as
[HAL](http://stateless.co/hal_specification.html).

The advantages provided by HAL:
 * It’s simple and easy to understand.
 * It’s lightweight and has [good language
    coverage](https://github.com/mikekelly/hal_specification/wiki/Libraries)
    including Python, JS, PHP, Java, Objective-C, Clojure, and Go.
 * It encourages using standard link relations. This reduces the need for
    custom documentation.
 * It supports [transclusion](#transclusion) of resources via the
    [`_embeded`](https://tools.ietf.org/html/draft-kelly-json-hal-06#section-4.1.2) property.
 * There is a [schema for validation](http://hyperschema.org/mediatypes/hal)
    and an [API browser](https://github.com/mikekelly/hal-browser).
 * It supports both JSON and XML.

When deciding upon property names in JSON messages, first see if it
maps to an existing vocabulary. Start with the [IANA link
relations](http://www.iana.org/assignments/link-relations/link-relations.xhtml).
If you don’t find what you are looking for there, try
[schema.org](http://schema.org/docs/full.html). If you still can’t find what you
need consult the API design guild.

Ensure that the application design is not wedded to a particular hypermedia
format (e.g. HAL). The domain model should be sufficiently decoupled from the
media type to allow for multiple or evolving representations and potentially
versions.

## Linking

All resources should have a property containing the canonical "self" URI of 
that individual resource. In HAL this will be included in the "_links" 
property.

### Use Appropriate HTTP Status Codes

The HTTP status codes provide the most basic set of API semantics. Use them as
they were intended. Below is an enumeration of some of the common status codes.
If you don’t see what you need here refer [to the
RFC](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) or the [Wikipedia
page](http://en.wikipedia.org/wiki/List_of_HTTP_status_codes#2xx_Success).

 * `200 OK`: represents a successful HTTP request. Do not use for an
   error (see 4xx and 5xx) or a missing resource (use `404 Not Found`).
 * `201 Created`: the resource was created synchronously (e.g. via `POST` or `PUT`).
 * `202 Accepted`: the request was accepted. Use this to identify `POST`, `PUT`, or
	 `DELETE` requests that will be handled asynchronously.
 * `301 Moved Permanently`
 * `304 Not Modified`: used with conditional HTTP request to indicate that the
   client already has the request body.
 * `400 Bad Request`: there is a problem on the client side.
 * `404 Not Found`: the server did not find anything matching the request URI. A
    good rule of thumb is if the client is requesting a single entity and nothing
    matches the request, us '404 Not Found'. If the client is requesting a collection
    of entities and nothing matches the request, return a '200 OK' with an empty 
    collection.
 * `500 Internal Server Error`: there is a problem on the server side.

Related:
 * [Return appropriate status
	 codes](https://github.com/interagent/http-api-design/blob/master/README.md#return-appropriate-status-codes)

### Use HTTP Verbs to Manipulate Resources

Use HTTP verbs to manipulate resources. Below you will find a summary of the
primary HTTP verbs and how they should be used.

 * `GET`: used to retrieve data and should have no other side effects. It should
   *not* change any state on the server. Secondary side effects such as logging,
   caching, measuring, and monitoring are acceptable.
 * `POST`: create a new resource under the URI with the representation
   provided in the body of the request.
 * `PUT`: modify a resource at the specified URI with the representation
   provided. If the resource does not exist it will be created. `PUT` operations
   need to be idempotent.
 * `DELETE`: Delete the resource identified by the URI. `DELETE` operations need
	 to be idempotent.

If identical `PUT` or `DELETE` operations are repeated against a resource they may
have different HTTP status codes and headers but they will *not* change the
system state on the server.

All content responses will support conditional `GET` to avoid sending full
resource representations. Conditional `GET` can be used to save both bandwidth
and server resources.

To support conditional `GET`, provide `Last-Modified` and `Etag` headers with your
representations. The client can then send an `If-Modified-Since` header with the
request that the server can use to determine if the client has the most
up-to-date representation. If the client is up-to-date then the server can
respond with a `304 Not Modified` status code and an empty body. Similarly, the
client can use the `If-None-Match` header with the last `Etag` to receive a new
representation if it has changed.

### Use HTTP Headers for Additional Application Semantics

HTTP headers provide an additional layer of API semantics on top of HTTP status
codes and verbs. Below is a summary of some of the common HTTP headers that can
be used to affect cacheability and state manipulation.

A tabular enumeration of HTTP headers can be found
[here](http://en.wikipedia.org/wiki/List_of_HTTP_header_fields).

API endpoints need to support the following request headers:
 * `Accept`: Use `Accept` to support multiple versions of an API end
	 point or different representations.
 * `If-Match`: Use with `Etag` to make `PUT` and `POST` conditional and
	 avoid lost updates.
 * `If-Modified-Since`: See conditional `GET` above. Use with `Last-Modified`
	 from the same resource response.
 * `If-None-Match`: See conditional `GET` above. Use with `Etag`.
 * `If-Unmodified-Since`: Can be used to make `GET` or `PUT` conditional.
	 Combined with `Last-Modified`.

All responses will include the following headers:
 * `Cache-Control`: Use to specify how intermediaries will cache.
	 See [here](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html#sec13.1.3)
	 for more details.
 * `Content-Type`: This determines the parser used by the client and the
	 application semantics. The two acceptible types are `application/hal+json` and
	 `application/problem+json`.
 * `Etag`: Use to specify the version of the resources as a UUID.
 * `Last-Modified`: Use to specify the date of the resource. See `If-Modified-Since`
	 and `If-Unmodified-Since` above.

Use standard headers first before adding query parameters. The
standard headers are part of the HTTP protocol and have extensive documentation
and industry adoption. The same cannot be said for query parameters.

### Documentation

Use the [HAL Browser](https://github.com/mikekelly/hal-browser).

## Common Patterns

### Pagination

All collections returned by the API should either be of a known finite maximum 
length, or paged. Any unbounded collection is a denial of service waiting to 
happen.

All paged resources must maintain a maximum page size. Any request providing a
limit greater than the maximum page size will be truncated to the configured
maximum.

Add pagination by using `next`, and `previous` [link
relations](http://www.iana.org/assignments/link-relations/link-relations.xhtml)
to the representation of the collection. Here is an example [when using
HAL](http://tools.ietf.org/html/draft-kelly-json-hal-05#section-6).

API implementations may wish to retrieve one more than the requested length 
so they can determine whether a next link should be included.

TODO: Do we need a way of communicating the maximum page size to clients?

#### After/Limit Pagination

After/Limit pagination works when ordering items according to some value that is
unique to the item. By using 'after=[ordering property of last item in previous 
page], pagination is preserved even if the item in question is deleted between 
requests.

If you need to use query parameters for the URLs use `after` and `limit` (in that 
order). Example `/timeline?after=48399234&limit=25`.

This method of pagination is only possible if the "after" value is guaranteed to be
less than the first item on the next page. If no ordering property is available that 
meets this requirement, fall back to Offset/Limit pagination.

#### Offset/Limit Pagination

Offset/Limit pagination works by providing the offset from the beginning of the
collection being requested, and the maximum number of items to include. If you 
need to use query parameters for the URLs use `offset` and `limit` (in that order). 
Example `/search?q=Foo&offset=50&limit=25`.

Offset/Limit pagination is unstable. If items are added or deleted from the 
collection between requests, pagination will duplicate or skip resources. It is 
especially unsuitable for "infinite scroll"-style UIs, or collections where new
items are being added to the head of the list.

### Versioning

See [Versioning](Versioning.md)

### Timestamps

Use [ISO8601](http://en.wikipedia.org/wiki/ISO_8601) with combined date and time
in UTC for timestamps.

### Error Handling

Use [HTTP Problem](https://www.mnot.net/blog/2013/05/15/http_problem).

Below is an example response using HTTP Problem for a query that produced no
results:

    HTTP/1.1 404 Not Found
    Content-Type: application/problem+json
    Content-Language: en

    {
     "title": "We could not find the article you were looking for.",
     "detail": "Searching for the article id 12345 produced no results.",
    }


### Transclusion

HAL supports transclusion via the `_embedded` property. See below for an
example. The
[clients](https://github.com/mikekelly/hal_specification/wiki/Libraries) should
support this.

    "_embedded": {
			"users": [
				{
					"_links": {
						  "self": {
								"href": "http://whiskey.wikia.com/wiki/User:Craiglpalmer"
							}
					},
					"id": “Craiglpalmer",
					"name": "Craig L. Palmer"
				}
			]
    }

Embeds may be [full or
partial](https://tools.ietf.org/html/draft-kelly-json-hal-06#section-4.1.2).
Use query parameters to expand embedded resources e.g.
`?embed=user,comments`.

## Request Tagging

If a REST request contains a Wikia-Request-Id: header, this header should be
propagated to all other internal services called to fulfil the request. This
allows us to reconcile all the components of a single logical request in any
logging/auditing.

Once a service exists for assigning request IDs, any REST request that does
not contain such an ID should generate one by requesting it from that service.

## Additional Resources

### Articles

 * [Richardson REST Maturity
   Model (RMM)](http://martinfowler.com/articles/richardsonMaturityModel.html)
 * [Resource Oriented Web Services](http://blog.sgo.to/2014/02/rows.html)
 * [Three Levels of Hypermedia Messages](http://amundsen.com/blog/archives/1138)

### API Design Guides

 * [Heroku API Design Guide](https://github.com/interagent/http-api-design)
 * [18F API Standards](https://github.com/18F/api-standards)

### Books

 * [HTTP: The Definitive
	 Guide](http://www.amazon.com/HTTP-The-Definitive-Guide-Guides/dp/1565925092)
 * [RESTful WEB
	 APIs](http://www.amazon.com/RESTful-Web-APIs-Leonard-Richardson/dp/1449358063)

## Notes

 * Could transclusion be facilitated via Varnish+ESI in an intermediary in HAL? This
   would probably require translation back to JSON before returning to the
   client.
 * Should JSON-LD be the go-to standard for external APIs? The durability of the
	 standard seems higher but the current tooling is lower.
 * Should [JSON Schema](http://json-schema.org/) be used to validate
	 HAL? See the [media types page](http://hyperschema.org/mediatypes/) on
	 [hyperschema.org](http://hyperschema.org).
 * Should TLS be used for all requests? Certainly when access tokens are
	 involved. See the [related Heroku
	 recommendation](https://github.com/interagent/http-api-design#require-tls).
