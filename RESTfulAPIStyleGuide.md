# RESTful API Style Guide

## Table of Contents

* [Forward](#forward)  
* [Scope](#scope)  
* [Introduction](#introduction)  
* [Responses](#responses)  
    * [Sample Response](#sample-response)  
    * [JSON](#json-response)  
    * [Prefer Shallow over Deep](#prefer-shallow-over-deep)  
    * [Attribute Names](#attribute-names)  
    * [Status Codes](#status-codes)
    * [HTTP Method to Status Code Mapping](#http-method-to-status-code-mapping)
    * [Error Handling](#error-handling)
    * [Null/Empty Property Values](#nullempty-property-values)  
* [URIs](#uris)  
    * [Version](#version)  
    * [Namespaces](#namespaces)  
    * [Resource References](#resource-references)  
* [Collection Resources](#collection-resources)  
    * [Collection Resource](#collection-resource)  
    * [Read Single Resource](#read-single-resource)  
    * [Update Single Resource](#update-single-resource)  
    * [Update Partial Single Resource](#update-partial-single-resource)  
    * [Create New Resource](#create-new-resource)  
    * [Create New Resource - Consumer Supplied Identifier](#create-new-resource---consumer-supplied-identifier)  
* [Read-only Resources](#read-only-resources)  
* [Versioning and Lifecycles](#versioning-and-lifecycles)
    * [Product Lifecycle Terms](#product-lifecycle-terms)
    * [Backwards Compatibility](#backwards-compatibility)
    * [Releases](#releases)
* [Best Practices](#best-practices)  
    * [X-Request-ID HTTP Header](#x-request-id-http-header)      
    * [Write Client Sample Code](#write-client-sample-code)  
* [Conclusion](#conclusion)  

-----------------------

## Forward

>"Our customers are looking to Sabre to support their own growth goals and customer service enhancements with technologies that are fast to market, simple to use and easily integrated into their business strategies."
>   
>[*Sean Menke on August 14, 2017*](https://www.sabre.com/insights/releases/sabre-names-vish-saoji-as-chief-technology-officer/)

-----------------------

## Scope

This document is meant to guide Sabre developers building APIs consumed by external developers. It assumes **RESTful** services delivering **JSON** over **HTTP**.  

Initially written by the team creating the **Corporate Travel Services (CTS)**, this guide is available for anyone inside the company to adopt, and contribute.

-----------------------

## Introduction

Sabre is creating APIs exposing our internal capabilities to the outside world. Valuable capabilities. We consider it a platform on which companies build their business. Offering APIs allows our customers to enable their mobile applications, websites, and services for travel. More people using our APIs means our business grows.

Planning together how we'll grow our portfolio of APIs is the only smart path going forward.  Our developer customers are too smart to choose a set of APIs that are confusing to learn, and difficult to consume. We can't afford to have different teams producing APIs lacking consistency:

* In the way developers talk to our APIs.
* In the way our APIs talk back to developers.
 
If we create our APIs the right way then we'll improve how developers feel when they use them. Our APIs will "just work" as expected, they won't be confusing across our portfolio, they won't contradict each other, and they'll appear well planned when seen together. 

Developers are happier when the tools they're using do the thing they claim they would. Happy developers write better code. Code that's higher quality, and produced faster. Good code is what drives today's businesses.
   
APIs have a user experience. A way that they're encountered by a human being working with them - a developer. We want to produce the best possible **developer experience (DX)**.  

Because our APIs are created by different groups within Sabre, who are using different implementation technology, the APIs need to have a reliable, consistent, DX. Deciding together the style choices influencing how we're designing our APIs will lead to a better overall impression of the **Sabre Platform APIs.**

Concrete examples of style choices include:

* Parameters taken in request
* CRUD behaviors
* Attribute names in response

Overall, when creating new APIs, it's crucial to be compatible with existing HTTP interaction patterns and resource models already built into the platform. Speak to an API designer if your new plan doesn't fit into an existing pattern.

## Responses

>The single biggest problem in communication is the illusion that it has taken place.
> 
>*George Bernard Shaw*

### Sample Response

Here's a concrete example response to get your mind going. Review this, then read through the detailed explanations that follow. 

    {
      "catalogType": "FLIGHT_ITINERARY",
      "catalogId": "eb6814b2-37cd-444a-8519-b8db47a75f47",
      "request": {
        "classOfService": "COACH",
        "shopByPrice": {
          "fareType": "LOWEST_AVAILABLE"
        }
      },
      "itineraries": [
        {
          "itineraryId": "ae38c7c8-5297-4722-8002-1a0fb88a8440.831715245",
          "fare": {
            "negotiated": false,
            "quotedPrice": {
              "totalFare": {
                "amount": 11020,
                "currencyCode": "USD"
              }
            }
          },
          "journeys": [
            {
              "context": {
                "fromAirportCode": "DFW",
                "toAirportCode": "LAS",
                "date": "2017-06-26",
                "time": "18:00"
              },
              "flights": [
                {
                  "flightId": "AA:128:266",
                  "fromAirportCode": "DFW",
                  "toAirportCode": "LAS",
                  "departureDate": "2017-06-26",
                  "departureTime": "01:55",
                  "arrivalDate": "2017-06-26",
                  "arrivalTime": "02:46",
                  "marketingAirlineCode": "AA",
                  "flightNumber": "128",
                  "numberOfStops": 0,
                  "distance": {
                    "length": 1055,
                    "unit": "MILE"
                  },
                  "durationInMinutes": 171
                }
              ]
            }
          ]
        }
      ]
    }

### JSON Response

**JavaScript Object Notation (JSON)** is a service response format typically consumed by modern front-end clients. JSON's self-documenting structure makes it easy for people to read, and computers to use. Its lightweight scheme makes it appealing for customers connected by bandwidth-constrained mobile devices. JavaScript-based webapps find JSON easy to consume.

APIs supporting only JSON are easier to build, maintain, and document. 

If you find customers demanding an alternative output format use the standard HTTP `Accept header`. Although this is a clear hint, the API may choose to ignore it. 

### Prefer Shallow over Deep

Many client-side application developers will take a response and use it for an in-memory data object. The service response you design will directly impact their experience. Please choose to make it a good one.

Be mindful of how deeply you nest your data structures. It's easy on the server-side to iterate over collections of data models that contain arrays of complex objects. Modern HTTP libraries automatically build responses from relational data models. Done? Not yet! 

First consider what does the client programmer think of that output? How do they see it? Do they need to invest a lot of time parsing by looping, and de-referencing your JSON response? Does your service enable clean, maintainable, more easily understood rendering code on their side? 

Whenever possible prefer relatively shallow response data structures over deeply nested ones. Think simplicity over sophistication. 

### Attribute Names

#### Use Camel Case

When your service returns an object with data attributes on it how should you name them? There are plenty of ways to devate naming conventions, but we want to choose the one pleasing the most customers. Many client-side programming languages such as JavaScript, ObjectiveC, and Java prefer `camelCase`.

Good examples:
 
    confirmationId
    description
    firstName
    fromAirportCode
    numberOfStops

Bad examples:

    ConfirmationId
    DESCRIPTION
    first_name
    from-airport-code
    numberofstops

Choose words in your data attributes that convey the most meaning.

#### Do Not Use Abbreviations

API responses are made for human beings to read and consume as they write program code. Help them better understand your attribute names by spelling out what they represent. Do not use abbreviations.

Abbreviations force programmers to make a choice when using your APIs. When writing a line of code they must decide if a response attribute, or a request parameter, is abbreviated. If our users learn that all words are spelled out there are less decisions to make as they work. There's less memorization knowing when abbreviations are used, and when they're not.

Good examples:

    airlineNameLength
    errorIndex
    maximumFareRange
    numberOfStops

Bad examples:

    airNameLen
    errIdx
    maxFareRng
    numStops
    
Computer programmers have a history of shortening variable names. This was learned behavior because people could type more quickly in legacy text editors. Avoiding keystrokes is not a concern today because modern IDEs offer autocompletion. Furthermore, cost savings aren't found in the initial authoring of code, but in the long-term maintenance of it. Let's demonstrate that well-named attributes, that aid in producing more readable code, will improve upkeep of software projects.

#### Consider Programming Languages 

Property names made of alphabetical characters are the best. Certainly don't choose characters that are incompatible with popular programming languages.
 
For example, JavaScript front-end programmers will tend to use a response as an in-memory data structure. Using a hyphen (-) in a service response is a mistake because it's interpreted as a subtraction operator.

Avoid using attribute names that are commonly reserved keywords in programming languages. Don't allow your data to conflict with keywords, or well-known global identities. For example:
 
    const
    finally
    self
    static
    type
    var
    window

This isn't a comprehensive list because there are so many keyword names across the possible languages that our APIs may encounter. 

#### Singular vs Plural Property Names

Use plural names for array properties because they often contain many values.

Other properties are singular.  

    {
      "traveler": "Norman Rockwell",
      "paymentCards": ["visa", "mastercard", "american express"],
      "errorCode": 8675309 
    }

### Status Codes

RESTful services use HTTP status codes to provide the result status of the executed service. 
For example `200` for success, `404` when a resource is not found.

HTTP status codes are defined by section [10 of RFC 2616](https://tools.ietf.org/html/rfc2616#section-10),
the Internet Assigned Numbers Authority (IANA) maintains the official [registry of HTTP status codes](https://www.iana.org/assignments/http-status-codes/http-status-codes.xhtml).

#### Status Code Ranges

In HTTP there are 5 ranges of status codes, `1xx`, `2xx`, `3xx`, `4xx` and `5xx` but in practice, only `2xx`, `4xx` and `5xx` are applicable to RESTful services.

|Range|Meaning|
|---|---|
| `2xx` | Successful operation. Indicates that a request was successful, of which can succeed in multiple ways. |
| `4xx` | Client-side error. Usually, it indicates that there was a problem with the data sent in the request, but can also indicate infrastructure-related issues, e.g. authentication, throttling. In most cases the client can modify their request and resubmit. |
| `5xx` | Server-side error. Indicates a valid request was sent by the client, but it could not be processed due to software defects or external conditions like downline system unavailability. 5xx range status codes should not be utilized for validation or logical error handling.|

#### Recommended Status Codes

HTTP defines a wide set of status codes, but only some of them are relevant to RESTful services logic, 
other are primarily intended for non-functional concerns like content negotiation, protocol handling and security.

Each service **must** clearly specify which status codes it reports and under which conditions. In particular 
it applies to the business logic oriented status codes like `404 Not Found`.

##### 2xx - Success Statuses

Status codes in the `2xx` range indicate operation execution success.

`200 OK` status code **should** be used to indicate execution success. For example, if a call to GET /airport?country=US 
returns a list of airports in the USA, you would return a 200 OK status code. Another example is POST /airport with
a JSON representation of the airport entity sent in the payload. The result `200 OK` indicates that a new airport was successfully created. 
It is also appropriate to use `200 OK` when a request was successful, but no results were found. 
For example, if a client specifies GET /airport?country=VA you would return a 200 OK status code. 
In this example, the request was successful, but there are no airports (results) in Vatican City.
Although there are other codes (like `201 Created` and `204 No Content`) it is recommended to use `200 OK` exclusively 
for the sake of simplicity of the client/server interactions. 

`201 Created` and `204 No Content` are still acceptable but not recommended. Whenever they are used for success indication, 
they **must** be applied consistently. `201 Created` is sometimes used to support `upsert` operation i.e. update or insert 
when entity does not exist yet. In that case, `201 Created` indicates that the actual action ended up with **adding** a new entity 
instead of **updating** the existing one which is indicated by `200 OK`.   

`207 Multi-Status` is primary designated for reporting statuses associated with batch operations - batch processing 
is not covered by this specification yet, but may be in the future.

Other codes: `202 Accepted`, `205 Reset Content`, `206 Partial Content` are part of the protocol handling and **should not** 
be used directly by the service.

##### 4xx - Client-side Errors

`4xx` range represents client-side errors caused by problems with the request, like invalid data or
invalid authentication/authorization. In most cases, the client is responsible for fixing the request before such requests
can be processed by the server.

`400 Bad Request` **should** be used when the request is invalid, due to invalid format or violated business logic constraints.
Validation errors can be caused by the invalid request body format, a required field that was not specified, or due to an invalid value format (e.g. type number when expecting type string). 
Service can also reject syntactically correct request which doesn't meet business logic constraints.

`404 Not Found` **should** be used to indicate that the resource does not exist. It applies only to the invocations where the resource identification 
is provided in the request (for example in the URI).

`409 Conflict` **can** be used to report optimistic locking violations, but `400 Bad Request` is recommended for such cases.

`410 Gone` is strongly discouraged, use `404 Not Found` instead.

`401 Unauthorized` and `403 Forbidden` status codes are used to report security-related violations like authentication (`401 Unauthorized`) 
and authorization (`403 Forbidden`). They are commonly reported by the frameworks and infrastructure components (like gateways) 
but in some cases, may be reported by the service itself. In this case, the service contract **should** clearly describe when such 
statuses may be reported. 

`429 Too Many Requests` status codes are used to report throttling conditions. For example, when the client submits too many requests within a specified time range.

`422 Unprocessable Entity` is strongly discouraged, use `400 Bad Request` instead.

Other codes are part of the protocol handling, infrastructure and **should not** be reported directly by the service.

##### 5xx - Server-side Errors

`5xx` range represents server-side errors. It is _highly_ recommended the service reports only `500 Internal Server Error` and `503 Service Unavailable`.

`500 Internal Server Error` is reserved for reporting unexpected issues and error conditions and most likely is caused by a software defect. 
For example it may be caused by unhandled exceptions (like _NullPointerException_).

`503 Service Unavailable` indicates the service is currently unable to processes the request (for example, due to resources unavailability
or maintenance), but future requests may succeed. For example, the database is temporarily unavailable or overloaded.

### HTTP Method to Status Code Mapping

Not all status codes should be reported by every HTTP method. Some combinations must be avoided.

#### Business logic oriented status codes

Business logic oriented status codes are related to the business logic of the API and are directly set by 
the creators of the API function. 

**2xx**

| Status/Method   | GET | POST | PUT | DELETE | PATCH |
|-----------------|-----|------|-----|--------|-------|
| 200 OK          |  X  |  X   |  X  |   X    |   X   |
| 201 Created     |     |  X   |     |        |       |
| 204 No Content  |     |      |  X  |   X    |   X   |

_Note_: `201 Created` and `204 No Content` status codes are allowed but not recommended by this specification, use `200 OK` instead.

**4xx**

| Status/Method   | GET | POST | PUT | DELETE | PATCH |
|-----------------|-----|------|-----|--------|-------|
| 400 Bad Request |  X  |  X   |  X  |   X    |   X   |
| 404 Not Found   |  X  |      |  X  |   X    |   X   |
| 409 Conflict    |     |  X   |  X  |   X    |   X   |

#### Infrastructure/framework oriented status codes

The following is (not exhausted) list of infrastructure error codes that are assumed to be returned by the application 
framework infrastructure (e.g. Spring MVC Framework for Java) that you do not need to code into your API.

| Status/Method               | GET | POST | PUT | DELETE | PATCH |
|-----------------------------|-----|------|-----|--------|-------|
| 401 Unauthorized            |  X  |  X   |  X  |   X    |   X   |
| 403 Forbidden               |  X  |  X   |  X  |   X    |   X   |
| 404 Not Found               |  X  |  X   |  X  |   X    |   X   |
| 429 Too Many Requests       |  X  |  X   |  X  |   X    |   X   |
| 500 Internal Server Error   |  X  |  X   |  X  |   X    |   X   |
| 503 Service Unavailable     |  X  |  X   |  X  |   X    |   X   |
| ...                         |  X  |  X   |  X  |   X    |   X   |

_Note_: `404 Not Found` can be returned in both business logic and infrastructure related contexts as a request to 
non-existing URI will also result in this status code. In such case, it is returned by the HTTP server rather than 
the API logic itself.

### Error Handling

API teams want to build a "black box" that consistently works without fail. Client application authors usually want 
the same thing from APIs. 

What happens when something goes wrong? Error reporting gives a peek into that black box. 

We need to convey the reason an error occurred so that users can easily answer, "is it me, or is it them?"
An API consumer wants to know if the request was set up badly, or whether something went wrong internally.
Quality error handling gives a client (programmer) options for how to recover programmatically.

Thinking from the programmer's perspective, errors can happen in your API at two critical stages:

* While they're developing their software trying to hit deadlines.
* During production as customers use their application.

Standard HTTP status codes are important, but in most cases are insufficient at providing enough information to understand 
the cause of the issue.  For that reason, whenever the service reports an error (a status code outside the `2xx` range), 
the response body **must** provide error details enclosed in a well-defined,
and easy to parse structure (preferable in JSON). And each service **must** provide such information consistently. 

Error responses **must** provide _timestamp_, _error code_ and human-readable, contextually relevant _message_. 

Consistency improves developer experience. Ensure that your error messages are similar, as design patterns emerge throughout your APIs.
For example, once a user sees errors in creating resources, they ought to see similar errors with other creation scenarios.

#### Recommended Error Response

The recommended error response should be JSON object with the following format:

```
{
  "timestamp": "2018-03-30T07:16:56.603Z",
  "errorCode": "VALIDATION_FAILED",
  "message": "id='id-1' has invalid format, only letters and numbers are allowed"
}
```

The error response has the following properties:

| Property    |Mandatory| Description |
|-------------|---------|-------------|
| timestamp   |   yes   | Time when the error message was generated |
| errorCode   |   yes   | Machine readable error code |
| message     |   yes   | Human readable message with additional information what went wrong |
| diagnostics |   no    | Supplementary diagnostics information |

**timestamp** provides the exact point in time when the error response was generated. 
This property is not _mandatory_ but providing it is _highly recommended_. The _timestamp_
**must** be in [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) format and use UTC. 
To simplify parsing, it is **recommended** to stick to the following format: `yyyy-MM-dd'T'HH:mm:ss.SSS'Z'`. For example,
`2018-03-30T07:16:56.603Z`.

**errorCode** property is _mandatory_ and must consist of alphanumeric characters. 
`_` (underline), `-` (hyphen), `.` (dot) characters can be used as words separators. Using spaces is _discouraged_. 

All error codes **must** follow the same formatting scheme and **must** be consistent among the the services in the same suite. 

Note: client logic may depend on the error codes reported by the service and for that reason they are part of an API. 
Error codes **must** be fully documented and any changes between the service version **must** be clearly described.

This specification does not standardize the format for _errorCode_; however, this may change in the future.

Example error codes: 
* _VALIDATION_FAILED_
* _INTERNAL_ERROR_

**message** property is _mandatory_ and **must** provide human-readable descriptions of an error condition. 
The content of _message_ is not intended to be understood, nor parsed by robots; data provided in the message should not be extracted
or used by the client to drive business logic. 

_message_ should be brief, but still provide enough information to simplify understanding of what went wrong. 
_message_ is not part of an API and its format may change at any time. Message cannot contain sensitive information like secrets nor any data which may disclose deployment 
or implementation details, for example server names, ip addresses, exceptions.

**diagnostics** property is _optional_. The format of diagnostics is not defined by this specification.
Diagnostics should provide additional data which is helpful to understand an error and find the root cause. 
It may contain information which disclose the application deployment details, server names, IP addresses, network topology,
application internals, etc., but providing sensitive data like secrets and passwords is not allowed. 
Diagnostics information **should not** be enabled permanently in the production environment. This functionality is primarily 
targeted for development environments and errors diagnostics. 


### Null/Empty Property Values

Contemplate removing empty or null values to avoid obscuring important data.

If a property is optional or has an empty or null value, consider dropping the property from the JSON response. A reason not to is if there's a compelling semantic reason for its existence.

-----------------------

## URIs

Human beings should be able to easily read and write URLs. Especially important for adoption on client platforms that don't have a native SDK, or library.  

### Version

The URI shall include `/vN` where the major version (`N`) is an integer number. Every API must release with a version number. We've chosen URL-based versioning because it's easy for API consumers.

#### URI Template
	/{version}/

Where _{version}_ starts with _v_ followed by version number.

#### Example
	/v1/
	
We explicitly discourage complex encoded versions in the URI, such as `/v1_8675_309` or `/v_8.675.309`.

### Namespaces

In any URI, the first noun (which may be singular or plural, depending on the situation) should be considered a "namespace". Namespaces should reflect the customer's perspective on how the product works, not necessarily the company's hierarchy. 

#### URI Template
	/{version}/{namespace}/

#### Example
	/v1/srw/

### Resource References
URI resource references should consistently use the same path components to refer to resources. Resources imply we're working with nouns (things) rather than verbs (actions). 

#### Use Lower Case
URI resource references should consistently use lower case with dashes as separators.

#### URI Template Forms
	/{version}/{namespace}/{resource}
	/{version}/{namespace}/{resource}/{resource-id}
	/{version}/{namespace}/{resource}/{resource-id}/{sub-resource}

-----------------------

## Collection Resources

Data resources intend to support some aspect of typical CRUD (Create, Read, Update, Delete) functionality. CRUD resources should be implemented with adherence to `POST/GET/PUT/PATCH/DELETE` HTTP verbs.

Collection resource names should be plural nouns, e.g. `/users`. This helps visually disambiguate collections from singletons.

| Verb    | Usage  | Is Idempotent | Notes |
| ---     | ---    | --- | --- |
| `GET`   | Read   | yes | |
| `POST`  | Create | no  | |
| `PUT`   | Create | yes | Only used for creation when client provides the resource identifier |
| `PUT`   | Update | yes | Only for entire resource update, not partial |
| `PATCH` | Update | yes | Used with [JSON Merge Patch](https://tools.ietf.org/html/rfc7396) message format |
| `DELETE`| Delete | yes | Should be repeatable with positive response. See [Delete Single Resource] for more detail |

### Collection Resource

<!--  todo - it is not clear. I'm adding this todo to have a place where we can start discussion about this are. -->
A list of all of the given resources, including any related metadata. Array of resources should be in the `items` field. Fields like `totalItems` and `totalPages` help provide context to paged results. Consistent naming of collection resource fields allow API clients to create generic handling for using the provided data across various resource collections.

The `GET` verb shouldn't effect the system, and shouldn't change response on subsequent requests (unless the underlying data changes), i.e. it should be idempotent. Exceptions to 'changing the system' are typically instrumentation/logging-related.

The list of data is presumed to be filtered based on the provided security context of the API client, this shouldn't be a list of all resources in the domain.

Providing a summarized, or minimized version of the data representation can
reduce the bandwidth footprint, in cases where individual resources contain a large object.

#### Filtering
##### Paging
Pages of results should be referred to consistently by the query parameters `page` and `pageSize`, where `pageSize` refers to the amount of results per request, and `page` refers to the requested page.

The `page` parameter is one-based. In other words, all paginated queries start at `page=1`.

Additionally, responses should include `totalItems` and `totalPages` whenever possible, where `totalItems` indicates the total items in the requested collection, and `totalPages` is the number of pages (iterpolated from `totalItems / pageSize`).

##### Hypermedia links
Hypermedia links are useful for paging through collections of resources, and `page`/`pageSize` query parameters can be maintained while navigating through pages of results.

Links should be provided with relative offsets `next`, `previous`, `first`, `last` wherever appropriate.

##### Time selection
`startTime` or `{propertyName}After`, `endTime` or `{propertyName}Before` query parameters should be provided if time selection is needed

##### Sorting
Provide `sortBy` and `sortOrder` whenever possible to allow sorting on collections:
* `sortBy` should be a field in the individual resources. 
* `sortOrder` should be set to `ascend` or `descend`.

#### URI Template
	GET /{version}/{namespace}/{resource}
	
#### Example Request
	GET /v1/tex/api/catalogs/eb6814b2-37cd-444a-8519-b8db47a75f47
	
#### Example Response
    {
      "catalogType": "FLIGHT_ITINERARY",
      "catalogId": "eb6814b2-37cd-444a-8519-b8db47a75f47",
      "request": {
        "travelerId": "site-gtmobilemouse-policy7",
        "classOfService": "COACH",
        "shopByPrice": {
          "fareType": "LOWEST_AVAILABLE"
        },
        "oneWay": {
          "fromAirportCode": "DFW",
          "toAirportCode": "LAS",
          "date": "2017-06-26",
          "time": "18:00"
        }
      },
      "itineraries": [
        {
          "itineraryId": "ae38c7c8-5297-4722-8002-1a0fb88a8440.831715245",
          "fare": {
            "negotiated": false,
            "quotedPrice": {
              "baseFare": {
                "amount": 0,
                "currencyCode": "USD"
              },
              "totalTaxes": {
                "amount": 0,
                "currencyCode": "USD"
              },
              "totalFare": {
                "amount": 11020,
                "currencyCode": "USD"
              }
            }
          },
          "journeys": [
            {
              "context": {
                "fromAirportCode": "DFW",
                "toAirportCode": "LAS",
                "date": "2017-06-26",
                "time": "18:00"
              },
              "soldOut": false,
              "flights": [
                {
                  "flightId": "AA:128:266",
                  "fromAirportCode": "DFW",
                  "toAirportCode": "LAS",
                  "departureDate": "2017-06-26",
                  "departureTime": "01:55",
                  "arrivalDate": "2017-06-26",
                  "arrivalTime": "02:46",
                  "marketingAirlineCode": "AA",
                  "flightNumber": "128",
                  "numberOfStops": 0,
                  "equipment": {
                    "type": "32B"
                  },
                  "distance": {
                    "length": 1055,
                    "unit": "MILE"
                  },
                  "durationInMinutes": 171,
                  "onTimePercentage": 0,
                  "seatMapId": "ae38c7c8-5297-4722-8002-1a0fb88a8440.831715245:AA:128",
                  "fareRulesId": "8754839c-e427-43bd-a65e-c64fe6fdc910",
                  "agencyPreference": {
                    "preferred": false
                  },
                  "corporatePreference": {
                    "preferred": false
                  }
                }
              ]
            }
          ]
        }
      ]
    }


#### HTTP Status
Empty collections (e.g. responding with zero items), shouldn't return a `404 Not Found`, because it's not appropriate. The `itineraries` array of items should just be empty, and provide collection metadata fields (e.g. `"totalCount": 0`). Invalid values in request parameters should result in a `400 Bad Request`. Return `200 OK` for successful responses.

### Read Single Resource
A single resource, typically derived from the parent collection of resources (often more detailed than the collection resource items).

Executing `GET` should never affect the system (i.e. be idempotent), and shouldn't change response on subsequent requests.

All identifiers for sensitive data should be non-sequential, and preferably non-numeric. In scenarios where this data might be used as a subordinate to other data, immutable string identifiers should be utilized for easier readability and debugging (i.e. `"site-gtmobilemouse-policy7"` vs `8675309`).

#### URI Template
	GET /{version}/{namespace}/{resource}/{resource-id}
	
#### Example Request
	GET /v1/tex/api/travelers/site-gtmobilemouse-policy7
	
#### Example Response    
    {
      "personalDetails": {
        "gender": "MALE",
        "personalIdentifiableInformation": {
          "firstName": "Vernon",
          "lastName": "Bear",
          "birthDate": "1952-06-19"
        }
      },
      "paymentCards": {
        "flight": [
          {
            "paymentCardId": "visa-1",
            "customDescription": "visa-1"
          }
        ],
        "airExtras": [
          {
            "paymentCardId": "visa-1",
            "customDescription": "visa-1"
          }
        ]
      },
      "corporateSettings": {
        "justifications": [
          {
            "type": "FLIGHT",
            "code": "01 Non-compliance",
            "description": "CompIsCompliant"
          },
          {
            "type": "FLIGHT",
            "code": "02 Non-compliance",
            "description": "NoCompIsNonCompliant"
          },
          {
            "type": "CAR_RENTAL",
            "code": "01 Non-compliance",
            "description": "CompIsCompliant"
          }
        ],
        "authorizerNames": [
          "Joshua Banda",
          "Ripsy Phillip"
        ],
        "supplementaryDataGroups": [
          {
            "name": "Track User",
            "supplementaryDataQuestions": [
              {
                "supplementaryDataQuestionId": "10594189",
                "question": "Project Code",
                "answerRequired": false
              },
              {
                "supplementaryDataQuestionId": "10591700",
                "question": "Reason for travel",
                "answerRequired": true,
                "instructionalText": "Select the appropriate travel reason",
                "constrainedResponse": {
                  "numberOfAnswersAllowed": 1,
                  "preDefinedAnswers": [
                    {
                      "answerId": "CM",
                      "answer": "Client meeting",
                      "isDefault": false
                    },
                    {
                      "answerId": "IM",
                      "answer": "Internal meeting",
                      "isDefault": false
                    },
                    {
                      "answerId": "PL",
                      "answer": "Personal travel",
                      "isDefault": false
                    }
                  ]
                }
              }
            ]
          }
        ],
        "announcements": [
          {
            "paragraphs": [
              "<p>only UA flights</p>"
            ]
          }
        ]
      }
    }


#### HTTP Status

If the provided resource identifier is not found, responds `404 Not Found` HTTP status (even with 'soft deleted' records in data sources). Otherwise, `200 OK` HTTP status should be utilized when data is found.

### Update Single Resource

Updates a single resource. The shape of the `PUT` request should maintain parity with the `GET` response for the selected resource. Fields in the request body can be optional or ignored during deserialization, such as `createTime` or other system-calculated values.

#### URI Template
	PUT /{version}/{namespace}/{resource}/{resource-id}
	
#### Example Request
	PUT /v1/tex/api/loyalties/8456d07a-6676-11e7-907b-a6006ad3dba0
	
	{
     "flights": [
        "AA": "asdf876",
        "LH": "LH1236699999"
     ],
     "hotels": [
        "spg": "9a8sdf-lk239",
        "hhonors": "123-asdf-789"
     ]
    }
	
#### HTTP Status

Any failed request validation responds `400 Bad Request` HTTP status. If clients attempt to modify read-only fields, this is also a `400 Bad Request`.

If there are business rules (more than data type/length/etc), it is best to provide a specific
error code & message (in addition to the `400`) for that validation.

For situations which require interaction with APIs or processes outside of the current request, the `422` status code is appropriate. 

After successful update, `PUT` operations should respond with `204 No Content` status, with no response body.

### Update Partial Single Resource
Updates a part of a single resource. Unlike `PUT`, which requires parity with `GET`, `PATCH` merely changes the fields provided, and leaves the rest of the resource unaffected. 

[JSON Merge Patch](https://tools.ietf.org/html/rfc7396) is a message format used to description a set of modifications to a target resource, used for all `PATCH` operations at Sabre.

Unless it optimizes client flow in calling other APIs, or system generated values could be commonly changed by an update, response should be `204 No Content` and no response body. Because `PATCH` is often called frequently in interactive UX design, returning the entire response could be irresponsible from a bandwidth perspective, especially in mobile scenarios.

#### URI Template
	PATCH /{version}/{namespace}/{resource}/{resource-id}
	
#### Example Request
	PATCH /v1/tex/api/travelers/site-gtmobilemouse-policy7
	
    {
      "personalDetails": {
        "personalIdentifiableInformation": {
          "firstName": "Norman",
          "lastName": "Rockwell",
          "suffix": null
        }
      }
    }
    
#### Example Response
	204 No Content
    
#### HTTP Status
Status/response for `PATCH` is the same as `PUT`.

### Delete Single Resource
Deletes a single resource. In order to enable retries (typically patchy connectivity), `DELETE` is treated as idempotent, so it should always respond with a `204 No Content` HTTP status. `404 Not Found` HTTP status shouldn't be utilized here, as on a second retry a client might mistakenly think the resource never existed at all. `GET` can be utilized to verify the resources exists prior to `DELETE`.

#### URI Template
	DELETE /{version}/{namespace}/{resource}/{resource-id}
	
#### Example Request
	DELETE /v1/tex/api/catalogs/eb6814b2-37cd-444a-8519-b8db47a75f47

#### Example Response
	204 No Content

### Create New Resource

Creates a new resource in the collection. Request body may be somewhat different than `GET`/`PUT` response/request (typically fewer fields as the server will generate some values).

In most cases, the API server produces an identifier for the resource. In cases where identifier is supplied by the API consumer, use "Create New Resource - Consumer Supplied Identifier."

Once the `POST` has successfully completed, a new resource will be created. The identifier for this resource should be added to the resource collection URI.

Hypermedia links provide an easy way to get the URL of the newly created resource, using the `rel`: `self`, in addition to other links for operations allowed for the new resource.

#### URI Template
	POST /{version}/{namespace}/{resource}
	
#### Example Request
Note that server-generated values are not provided in the request.

    POST /v1/tex/api/catalogs/
    {
      "travelerId": "site-gtmobilemouse-policy7",
      "classOfService": "COACH",
      "shopByPrice": {
        "fareType": "LOWEST_AVAILABLE"
      },
      "oneWay": {
        "fromAirportCode": "DFW",
        "toAirportCode": "SFO",
        "date": "2017-08-31",
        "time": "09:00"
      }
    }

#### Example Response
    201 Created
    
    "headers": {
        "location": "f249c16f-5f44-4664-89fd-9cb8338ae843",
      }

### Create New Resource - Consumer Supplied Identifier

When an API consumer defines the resource identifier, the `PUT` verb should be utilized, as the operation is idempotent, even during creation.

The same interaction as "Create New Resource" is used here. `201` + response body on resource creation, and `204` + no response body when an existing resource is updated.

-----------------------

## Read-only Resources
In situations where highly cacheable data is utilized to calculate values, or provide static reference, `GET` can be utilized instead of `POST`, as `POST` is not cacheable in HTTP. 

`GET` operations should be idempotent, and shouldn't be used to change resource state. 

### URI Template
	GET /{version}/{namespace}/{read-only-resource}
	
### Example Request
	GET /v1/location/geocode?address=77+N.+Washington+Street%2C+Boston%2C+MA%2C+02114

-----------------------

## Versioning and Lifecycles

Users need APIs to be stable, and APIs need to evolve over time.

Version numbers are specific to individual APIs, and not to the platform as a whole. Incrementing the version number will happen most often when the underlying data model significantly changes. In this case you'll create the new API, a data migration strategy, and a plan for deprecating the old version.

Apply the guidelines below to your API strategy.

### Product Lifecycle Terms

|  Phase  |                                   Release                                   |       Environment      |             Customer Availability            |                              Dev Studio Visibility                             |
|:-------:|:---------------------------------------------------------------------------:|:----------------------:|:--------------------------------------------:|:------------------------------------------------------------------------------:|
| Planned | Release dates and availability have been established                        | -                      | -                                            | -                                                                              |
| Alpha   | Deployed for prototype-level customer testing and validation                | Test                   | Yes; available to select customers           | No; you are responsible for providing direct documentation to select customers |
| Beta    | Deployed for production-level customer validation testing                   | Test and/or Production | Yes; available to select customers           | Yes, available to select  customers                                            |
| Live    | Operational and fully-supported with general availability                   | Test and Production    | Yes; available to new and existing customers | Yes; available to new and existing customers                                   |
| Legacy  | Operational and fully-supported (including backwards- compatible bug fixes) | Test and Production    | Yes; existing customers only                 | Yes; documentation must be updated with legacy version language                |
| Retired | Unpublished from test and production                                        | None                   | No; routing has been disabled                | No; documentation has been archived                                            |

### Backwards Compatibility 

APIs can be improved in non-breaking ways. The following compatibility scheme is **required** for all Sabre JSON products:

* A **Major** version is required when backwards-incompatible changes are made. This is an ordinal starting with 1 for the first live release (ex: `v1`).
* All **Minor** version (additional functionality) and **Patch** (bug fixes) releases must be backwards-compatible and are rolled-up into the **Major** version (ex: `v1`). If you're delivering an API that's backwards-compatible, there's no need to revise its version number.

How do you know that your API change won't break existing clients already in production? Here are some questions to keep in mind as your consider backwards-compatibility: 

* Did you remove parameters or response attributes?
* Did you convert optional parameters into required ones?
* Did you add required parameters?
* Did you fundamentally change business rules?

#### Major (Backwards-incompatible) Change Criteria

* Adding a required field to the request
* Removing or renaming a service, interface, field, method, or enum value
* Changing the *type* of a field (ex: from a number to a string)
* Changing a resource name format
* Changing visible behavior of existing requests
* Changing the URL format in the HTTP definition
* Adding a read/write field to a resource message

#### Minor + Patch (Backwards-compatible) Change Criteria

**Minor**

* Adding a method, such as GET to a POST resource 
* Adding an optional parameter to a request
* Adding an attribute to a response
* Adding a value to an enum (ex: a parameter's valiud values)
* Adding an output-only resource field 

**Patch**

* Fixing a bug or any urgent issue that cannot wait until the next release

### Releases

#### Communication

Send an advance notification to the customer **30 days prior** to the launch day of the new Live version. In the communication, state that the new Live version *will be* released, and therefore, the pre-existing version *will become* a Legacy version. This can also be sent when **the new version is deployed to the test environment**, whichever comes first.

When necessary, work with your Marketing Representative to communicate the business value of the new version based on the defined tier of your product.

#### Documentation

Demonstrate the business value of the new release and assist the customer in understanding the API by providing sufficient documentation. This takes the form of:

* Detailed documentation describing the new features of the Live version
* New documentation artifacts (ex: schema) for customers to consume the Live version
* Release notes that detail the new features to assist customers in their decision to migrate to the live version

#### Release Impact Matrix

|       | Detailed Documentation | Release Notes | Updated Documentation Artifacts | Marketing Representative | 30-Day Advance Notice |
|:-----:|:----------------------:|:-------------:|:-------------------------------:|:------------------------:|:---------------------:|
| Major |            X           |       X       |                X                |             X            |           X           |
| Minor |            X           |       X       |                X                |                          |                       |
| Patch |                        |       X       |                                 |                          |                       |


-----------------------
	
## Best Practices

### X-Request-ID HTTP Header

Consumers of our APIs may optionally send the `X-Request-ID` custom HTTP header with a unique ID identifying
the request for tracking purposes. 

We should log it together with all information required to troubleshoot issues reported by 
customers.

### Write Client Sample Code

It's recommended to create some form of sample code that consumes your new APIs. 

APIs are written for other humans beings to use use, and it's suggested that your using them will help keep the experience of your developer/customer in mind. 

We always learn from real-world experimentation. Use what you learn to improve your APIs.  

-----------------------

## Conclusion

>"Developers are increasingly influential, and their opinions drive changes."
>
>*TMC customer visit, August 11, 2017* 

Coordinating our API development will make the Sabre platform strategy more complete. From the outside looking in, our portfolio of APIs should look as through it was created by a single hand. Only we on the inside will know exactly how many brains contributed to the effort. 

Using a shared style guide, like this one, is one part of coordinating our effort. 

Creating consistent APIs across the company is difficult work, but it's worthy work. Our customers will be the first to notice when we plan together. Consistency is a key element of an outstanding developer experience. 

Highly capable APIs, matched with outstanding DX, will ensure that our technology is used as much as possible.
   
