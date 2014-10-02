# API Design Guide

## Introduction

This document is an API design guide for use at Wikia. The goal of this guide
is the creation of consistent, pragmatic APIs and reduced developer overhead.
The main focus of this guide will be HTTP APIs.

If you have a question or issue that is not covered by this document please
contact...

This design guide is a work in progress. If you have feedback or questions
please contact...

## On RESTfulness

REST is the architecture of the web as described in the [PhD
dissertation](http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm) by Roy
Fielding in 2001. It informs much of the best practice around API design in the
industry. REST is not an all or nothing architecture and APIs do not have to conform to
all of the REST constraints to be useful and valuable to the business.

## Guidelines

This section provides some general guidelines for API design. As a starting
point, APIs should be designed with the end user in mind-- the developer. The
goal is an interface that is intuitive, consistent, explorable, and pragmatic.

### Resources and Naming

Most concepts should be nouns and not verbs. Use HTTP verbs (see below) to
manipulate resources. Favor concrete names (e.g. `/articles`) over abstract ones (e.g. `/items`).

Use plural nouns to identify resources. For example, use `/articles` and
`/comments` to address collections of articles and comments respectively. Use
`/articles/{article_name}` to address a specific article.

[Minimize path nesting for
resources](https://github.com/interagent/http-api-design#minimize-path-nesting).
For example, instead of addressing a comment via the article
`/articles/{article_name}/comments/{comment_id}` request the comment from the
root using `/comments/{comment_id}`.

### Use HTTP Status Codes

The HTTP status codes provide the most basic set of API semantics. Use them as
they were intended. Below is an enumeration of some of the common status codes.
If you don’t see what you need here refer [to the
RFC](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) or the [Wikipedia
page](http://en.wikipedia.org/wiki/List_of_HTTP_status_codes#2xx_Success).

 * `200 OK`: represents a successful HTTP request. It should not be used for an
   error (see 4xx and 5xx) or an empty result set (consider using 404).
 * `201 Created`: the resource was created synchronously (e.g. via `POST` or `PUT`).
 * `202 Accepted`: the request was accepted. Use this to identify `POST`, `PUT`, or
	 `DELETE` requests that will be handled asynchronously.
 * `301 Moved Permanently`
 * `304 Not Modified`: used with conditional HTTP request to indicate that the
   client already has the request body.
 * `400 Bad Request`: there is a problem on the client side.
 * `404 Not Found`: the server did not find anything matching the request URI.
 * `500 Internal Server Error`: there is a problem on the server side.

Related:
 * [Return appropriate status
	 codes](https://github.com/interagent/http-api-design/blob/master/README.md#return-appropriate-status-codes)

### Use HTTP Verbs to Manipulate Resources

Use HTTP verbs to manipulate resources. Below you will find a summary of the
primary HTTP verbs and how they should be used.

 * `GET`: used to retrieve data and should have no other side effects. It should
   not change any state on the server. Secondary side effects such as logging,
   caching, measuring, and monitoring are acceptable.
 * `POST`: create a new resource under the URI with the representation
   provided in the body of the request.
 * `PUT`: modify a resource at the specified URI with the representation
   provided. If the resource does not exist it will be created. `PUT` operations
   should be idempotent.
 * `DELETE`: Delete the resource identified by the URI. `DELETE` operations should
	 be idempotent.

If identical `PUT` or `DELETE` operations are repeated against a resource they may
have different HTTP status codes and headers but they should not change the
system state on the server.

Use conditional `GET` to avoid sending full resource representations.
Conditional `GET` can be used to save both bandwidth and server resources.

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

Request:
 * `Accept`: Consider using accept to support multiple versions of an API end
	 point or different representations.
 * `If-Match`: Use with `Etag` to make `PUT` and `POST` conditional and
	 avoid lost updates.
 * `If-Modified-Since`: See conditional `GET` above. Use with `Last-Modified`
	 from the same resource response.
 * `If-None-Match`: See conditional `GET` above. Use with `Etag`.
 * `If-Unmodified-Since`: Can be used to make `GET` or `PUT` conditional.
	 Combined with `Last-Modified`.

Response:
 * `Cache-Control`: Use to specify how intermediaries should cache.
	 [TBD](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html#sec13.1.3)
 * `Content-Type`: This determines the parser used by the client and the
	 application semantics. [Prefer machine readable content
	 types](https://github.com/interagent/http-api-design#provide-machine-readable-json-schema) over
	 plain-old-JSON (in the form of `application/json`).
 * `Etag`: Use to specify the version of the resources as a UUID.
 * `Last-Modified`: Use to specify the date of the resource. See `If-Modified-Since`
	 and `If-Unmodified-Since` above.

Consider using standard headers first before adding query parameters. The
standard headers are part of the HTTP protocol and have extensive documentation
and industry adoption. The same cannot be said for query parameters.

## Common Patterns

### Pagination
### Versioning

via `Accept` header?

### Timestamps

Use ISO8601 in UTC.

### Caching

### Error Handling

## Context Specific Guidelines

### Internal APIs


### External APIs


## Additional Resources

### Articles

 * [Richardson REST Maturity
   Model (RMM)](http://martinfowler.com/articles/richardsonMaturityModel.html)

### API Design Guides

 * [Heroku API Design Guide](https://github.com/interagent/http-api-design)

### Books

 * [HTTP: The Definitive
	 Guide](http://www.amazon.com/HTTP-The-Definitive-Guide-Guides/dp/1565925092)
 * [RESTful WEB
	 APIs](http://www.amazon.com/RESTful-Web-APIs-Leonard-Richardson/dp/1449358063)
