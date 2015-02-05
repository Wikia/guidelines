# REST API Versioning and Compatibility

##Backwards Compatibility

Stable APIs MUST be written in a way that is provides backwards compatibility 
for complying clients.

A complying client:

* Does not rely on the order of fields in a representation
* Ignores any field in a representation that it does not recognise
* Correctly handles missing optional fields
* Follows HTTP-level (301, 302) redirects transparently
* Does not construct URIs except from templates provided by previous
  REST responses

Thus, an API change is not backwards compatible if a previously valid request 
returns a semantically different result:

* The resource is removed without providing a redirect to an equivalent 
  resource
* A query parameter is no longer recognised
* The meaning of a previously accepted query parameter changes
* An HTTP method previously accepted at the URI is no longer accepted
* A new required field is added to the POST or PUT data accepted at that 
  resource
* The meaning of a field in the POST or PUT data accepted at that resource 
  changes
* A field is removed from the JSON resource returned by the API
* The type of a field in a returned JSON resource is changed (i.e. from 
  string to object)
* The meaning of a field in a JSON document returned from the API is changed

On the other hand, the following changes are considered backwards compatible:

* A resource is moved to a new URI, but the old URI provides a “301 moved 
  permanently” response
* A new query parameter is accepted, but its absence maintains previous 
  behaviour
* New optional fields are added to the resource returned from the request
* New fields are accepted in a resource provided to a POST or PUT, but their 
  absence maintains previous behaviour
* A previously required field or query parameter is made optional

Forwards Compatibility

Maintaining forwards compatibility in a REST API is a lot harder, and a lot 
more dangerous, as it involves either ignoring instructions provided by the 
client, or not returning data to the client that the client expects to be 
present.

Because Wikia does not intend to support multiple versions of its API 
running simultaneously outside controlled service upgrade processes, it’s 
safest not to support forwards compatibility of APIs. So:

If a client provides an unrecognised or invalid query parameter, or an 
unrecognised or invalid field in a JSON document, the server should reject 
that request.

API Versioning

If we follow the above compatibility guidelines, we should not have to worry 
about versioning our REST APIs. Services MAY provide a response header 
telling clients what version of the service they are running, but clients
should not rely on that version for anything more than cache invalidation. 
Determining what "version" a response is should be done by looking at the
contents of the response to see whether the expected data is there or not.

