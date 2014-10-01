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

## Universal Guidelines

### Use HTTP Status Codes

The HTTP status codes provide the most basic set of API semantics. Use them as
they were intended. Below is an enumeration of some of the common status codes.
If you don’t see what you need here refer [to the
RFC](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) or the [Wikipedia
page](http://en.wikipedia.org/wiki/List_of_HTTP_status_codes#2xx_Success).

  * 200 OK: represents a successful HTTP request. It should not be used for an
		error (see 4xx and 5xx) or an empty result set (consider using 404).
	* 201 Created: the resource was created. Use this when a new resource has been
		created.
	* 301 Moved Permanently
	* 304 Not Modified: used with conditional HTTP request to indicate that the
		client already has the request body.
	* 400 Bad Request: there is a problem on the client side.
	* 404 Not Found: the server did not find anything matching the request URI.
	* 500 Internal Server Error: there is a problem on the server side.

### Use HTTP Verbs

idempotent
safe vs unsafe

### Use HTTP Headers for Additional Application Semantics

HTTP headers provide an additional layer of API semantics on top of HTTP status
codes and verbs. Consider using headers first before adding query parameters. Below is a
summary of some of the common patterns that can be implemented with HTTP
headers.

The request headers can be found
[here](http://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html#sec5.3). The
response headers are listed
[here](http://www.w3.org/Protocols/rfc2616/rfc2616-sec6.html#sec6.2).

 * Pagination
 * Saving a round-trip

### Caching

## Context Specific Guidelines
### Internal APIs


### External APIs

## Additional Resources
