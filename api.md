# API styleguide

- [Foundations](#foundations)
- [Requests](#requests)
- [Responses](#responses)


## Foundations

### Defining Routes
Each route exposed as an API  must be independent of all others, each must not share state with any other and they must always be transparent and stateless. This is intended to simplify maintenance and maximize reuse.

### TLS
Always require secure connections with TLS without exception. 

### URI components

#### Version
The URI should include `/vN` with the major version (`N`) as a prefix. e.g.:

    /v{version}/
    /v1/

URL-based versioning is used for simplicity, versus the more complex header-based approach. Relying on major version numbers is a signal to API consumers of intent -- that major number version changes are a significant event.

#### Resources
It is important that you have agreement from the API Product Owner and API community before reserving a Resource. Resources should reflect the customer's perspective, not necessarily the company's. 

Collection resource names should be plural nouns, e.g. `/customers`. This helps visually disambiguate collections from singletons. e.g.:

    /stores/v2

### Leaky abstractions
Avoid leaky abstractions in your URIs, field names and data structures. Ensure your clients don't care and don't know that your backend is for example a Mongo database, SAP, or an enterprise IBM product. That backend may change and you don't want to impact your (potentially many hundreds of) clients. 

### IDs
Where possible use IDs that we are well known within the business domain. Be clear between those IDs which are published (which have been committed to supporting and not breaking), versus private IDs (that are a current implementation detail).


### Todo
- Request IDs
- Paging


## Requests

### Request bodies
Accept serialized JSON on PUT/PATCH/POST request bodies, either instead of or in addition to form-encoded data. This creates symmetry with JSON-serialized response bodies, e.g.:

    curl 
      -X POST http://<server>/basket/v2 \
      -H "Content-Type: application/json" \ 
      -d '{
        "skuId":1234,
        "quantity":1
        }' 

### Downcase paths
Use downcased and dash-separated path names e.g:
      <server>/customers/my-offers

### HTTP Verbs

The `GET` verb should not effect the system, and should not change response on subsequent requests (unless the underlying data changes), i.e. it should be idempotent. Exceptions to 'changing the system' are typically instrumentation/logging-related.

Use `POST` to create one or multiple Resources. Use `PUT` to update or create a single Resource. Use `DELETE` to delete a Resource.


### Actions or Complex operations
Avoid actions or verbs in the URL. In special cases where these are needed, such as when a resource is moving to another resource (so state is changing against two Resources), perform the operation using a `POST`.
e.g. 
    `POST` `/basket/v2/items/{id}/save-for-later`

Search is a common example where a verb is commonly accepted in the URI.

## Responses

### Data types
Although your first client may not care whether you are using strings or integers to represent something like a price or quantity, ensure you use the correct data type. ie quantity: `2` and not `"2"`.

#### Date and time
Keep date time formats consistent with the ISO standards. Use [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) everywhere—more specifically, use the [RFC 3339](http://tools.ietf.org/html/rfc3339) subset of ISO 8601. This kind of ISO 8601 subset formatting is parseable in most programming languages, including Java (with [JodaTime](http://joda-time.sourceforge.net/cal_iso.html)), Python, Ruby, JavaScript, Perl etc. It is recommended by [RFC 7493](http://www.rfc-editor.org/rfc/rfc7493.txt) (the "I-JSON" specification) and [Google's JSON structure guide](http://google-styleguide.googlecode.com/svn/trunk/jsoncstyleguide.xml). ISO 8601 is also used in HTML5, XML and a variety of other specifications. The [microformats wiki](http://microformats.org/wiki/iso-8601) details the use of ISO 8601.

    "created": "2012-01-01T12:00:00Z"

#### HTML
Avoid exposing HTML through an API. You have no guarantee that a client can easily support this. Not all clients are HTML based, and many native apps struggle.  Instead of `<\br>` use `\n`. It _may_ be appropriate that a CMS API might return HTML, but again it may well be likely that a consumer would like the data behind that content.


### HTTP status codes
All the responses must use HTTP response status codes correctly (`2xx` = success; `3xx` = redirection, `4xx` = client data error, `5xx` = server error) and  HTTP status codes are not appropriate for the response body. Never use a 200 for an API that has returned an error. Apart from being wrong and unexpected, it is difficult for clients to handle, and will also be cached at potentially multiple points.
HTTP status codes are not appropriate for the response body. Never use a 200 for an API that has returned an error. Apart from being wrong and unexpected, it is difficult for clients to handle, and will also be cached at potentially multiple points.

**Some specific codes are highlighted below, but this list is by no means exhaustive.**

#### Success
- `200`: Request succeeded for a `GET` call, for a `DELETE` or `PATCH` call that completed synchronously, or for a `PUT` call that synchronously updated an existing resource
- `201`: Request succeeded for a `POST` call that completed synchronously, or for a `PUT` call that synchronously created a new resource
- `202`: Request succeeded but we've queued the persistence for background processing, as described in [RFC 7231](http://tools.ietf.org/html/rfc7231#section-6.3.3):

  > The request has been accepted for processing, but the processing has not
  > been completed.
- `204 No Content`: Request succeeded, but no response body will be returned.  

#### Authentication and Authorization error codes
- `401 Unauthorized`: Request failed because user is not authenticated
- `403 Forbidden`: Request failed because user does not have authorization to access a specific resource
- `405 Method not allowed`: Request failed due to incorrect method used (e.g.: `GET` used when it should be a `POST`). 

#### Make errors useful
- `422 Unprocessable Entity` for validation errors, as described in [RFC 2918](http://tools.ietf.org/html/rfc4918#section-11.2):

  > The 422 (Unprocessable Entity) status code means the server understands the
  > content type of the request entity (hence a 415(Unsupported Media Type)
  > status code is inappropriate), and the syntax of the request entity is
  > correct (thus a 400 (Bad Request) status code is inappropriate) but was
  > unable to process the contained instructions.  For example, this error
  > condition may occur if an XML request body contains well-formed (i.e.,
  > syntactically correct), but semantically erroneous, XML instructions.

- `429 Too Many Requests`: You have been rate-limited, retry later
- `500 Internal Server Error`: Something went wrong on the server, check status site and/or report the issue

Refer to [HTTP Response Code Spec](https://tools.ietf.org/html/rfc7231#section-6) for guidance on status codes for user error and server error cases.

### Return timestamps
Where appropriate, provide created and updated timestamps on resources by default.

### Nest foreign key relations
Represent foreign key references with a nested object, e.g. on a `Basket` resource:

      {
        "totalQuantity": 18.99,
        "customer": {
          "id": "123456..."
        },
        // ...
      }
Instead of e.g.:

      {
        "totalQuantity": 18.99,
        "customerId": "123456...",
        // ...
      }

This approach makes it possible to inline more information about the related resource without having to change the structure of the response or introduce more top-level response fields.
  
### Returning errors
Generate consistent error structures. Our general recommendation is to always have errors returned as a collection just in case you may want to return multiple. Include a machine-readable error `key`, and a human-readable error `message`.  Ensure you document your error format and the possible error keys. e.g.:

      {
        "key": "ERR_ORDER_NOT_FOUND", 
        "message": "No Order found"
      }

### Supporting caching
All API responses must set the correct `cache-control` and `concurrency control` HTTP headers to deliver a simple and usable API and to avoid needless network roundtrips and optimise the end user experience.  
There are three basic mechanics for managing caching:
1.	Freshness. The use of `Cache-control:max-age` directive to inform (and be informed) for how many seconds something can be cached for.
2.	Validation. Checking for stale data within a cache can be done by utilising the `last-modified` header.
3.	Invalidation. Where a cached entity is changed through a subsequent request (such as changing a basket by adding or removing items) the cache should itself be invalidated and fully refreshed regardless of `max-age` values.
The API's themselves must reflect and respect cache control headers passed to them from underlying systems and services and work on a worst-case response.


## Todo
- Authentication and Authorization
- Detail the use of `Patch`
- Content types and character encodings
- Hypermedia pattern
- Casing in attributes/fields (and use of abbreviation in naming)
- Headers indicating latency from dependencies
- `RateLimit-Remaining` header
- Health endpoints
- Localisation and Internationalisation
- Events / event sourcing


## References (and heavily indebted to)
- https://github.com/interagent/http-api-design
- https://github.com/paypal/api-standards/
- https://www.gov.uk/service-manual/making-software/apis