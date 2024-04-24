
# Federal Administration API Guidelines
## 1. Introduction
These REST API Guidelines pick up where the federal API architecture leaves off. It follows the vision "As a modern administration, we simplify access to our government services for our partners by making services usable through any electronic means". These are design guidelines for RESTful APIs and we encourage all API developers in the federal administration to follow them to ensure that our APIs:

are easy to understand and learn

are general and abstracted from specific implementation and use cases

are robust and easy to use

have a common look and feel

follow a consistent RESTful style and syntax

are consistent with other teams’ APIs

RESTful APIs (Representational State Transferful Application Programming Interfaces) are APIs (Application Programming Interfaces) that adhere to the principles and constraints of the REST architectural style. These APIs are designed to facilitate communication and data exchange between different software systems over the web. To learn more about REST, we refer you to https://en.wikipedia.org/wiki/REST, other online sources or relevant specialist literature.

The guidelines are based on Zalando’s RESTful API and Event Guidelines. Thanks to Zalando for their valuable work and for making the guidelines available.

Conventions used in these guidelines
The requirement level keywords "MUST", "MUST NOT", "REQUIRED", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" used in this document (case insensitive) are to be interpreted as described in RFC 2119.

2. General guidelines
The titles are marked with the corresponding labels: MUST, SHOULD, MAY.

MUST provide API specification using OpenAPI [101]
We use the OpenAPI specification as a standard to define API specification files. API designers are required to provide the API specification using a single self-contained YAML file to improve readability. We use OpenAPI 3.0 or later.

The API specification files should be subject to version control using a source code management system - best together with the implementing sources.

3. REST Basics - Meta information
MUST contain API meta information [218]
API specifications must contain the following OpenAPI meta information to allow for API management:

#/info/title functional descriptive name of the API

#/info/version to distinguish API specifications versions following semantic rules. The version of the OpenAPI document (which is distinct from the OpenAPI Specification version or the API implementation version).

Following OpenAPI extension properties should be provided in addition:

#/info/description containing a proper description of the API

#/info/x-audience intended target audience of the API (see rule 219)

#/info/license/{name,identifier,url} the license information for the exposed API.

#/info/contact/{name,url,email} the contact information for the exposed API.

MUST provide API audience [219]
Each API must be classified with respect to the intended target audience supposed to consume the API, to facilitate differentiated standards on APIs for discoverability, changeability, quality of design and documentation, as well as permission granting. We differentiate the following API audience groups with clear organisational and legal boundaries:

public
APIs with this audience can be accessed by anyone with Internet access.

partner
APIs with this audience can be accessed by applications of business partners of the administrative unit owning the API and the administrative unit itself.

private
APIs with this audience can only be accessed by applications of the administrative unit itself

The API audience is provided as API meta information in the info-block of the OpenAPI specification and must conform to the following specification:

/info/x-audience:
  type: string
  x-extensible-enum:
    - public
    - partner
    - private
  description: |
    Intended target audience of the API. Relevant for standards around
    quality of design and documentation, reviews, discoverability,
    changeability, and permission granting.
Note: Exactly one audience per API specification is allowed as shown in the following example. For this reason a smaller audience group is intentionally included in the wider group and thus does not need to be declared additionally. If parts of your API have a different target audience, we recommend to split API specifications along the target audience — even if this creates redundancies.

Example:

openapi: 3.0.1
info:
  x-audience: public
  title: Tax information service
  description: API for <...>
  version: 1.2.4
  <...>
For details and more information on audience groups see the {api-audience-narrative}[API Audience narrative (internal_link)].

4. REST Basics - Data formats
MUST use standard data formats [238]
Open API (based on JSON Schema Validation vocabulary) defines formats from ISO and IETF standards for date/time, integers/numbers and binary data. You must use these formats, whenever applicable:

OpenAPI type	OpenAPI format	Specification	Example
integer

int32

4 byte signed integer between -231 and 231-1

7721071004

integer

int64

8 byte signed integer between -263 and 263-1

772107100456824

number

float

binary32 single precision decimal number — see IEEE 754-2008/ISO 60559:2011

3.1415927

number

double

binary64 double precision decimal number — see IEEE 754-2008/ISO 60559:2011

3.141592653589793

string

byte

base64url encoded byte following RFC 7493 Section 4.4

"VA=="

string

binary

base64url encoded byte sequence following RFC 7493 Section 4.4

"VGVzdA=="

string

date

RFC 3339 internet profile — subset of ISO 8601

"2019-07-30"

string

date-time

RFC 3339 internet profile — subset of ISO 8601

"2019-07-30T06:43:40.252Z"

string

time

RFC 3339 internet profile — subset of ISO 8601

"06:43:40.252Z"

string

duration

RFC 3339 internet profile — subset of ISO 8601

"P1DT30H4S"

string

period

RFC 3339 internet profile — subset of ISO 8601

"2019-07-30T06:43:40.252Z/PT3H"

string

password

"secret"

string

email

RFC 5322

"example@admin.ch"

string

idn-email

RFC 6531

"hello@bücher.example"

string

hostname

RFC 1034

"www.admin.ch"

string

idn-hostname

RFC 5890

"bücher.example"

string

ipv4

RFC 2673

"104.75.173.179"

string

ipv6

RFC 4291

"2600:1401:2::8a"

string

uri

RFC 3986

"https://www.admin.ch/"

string

uri-reference

RFC 3986

"/clothing/"

string

uri-template

RFC 6570

"/users/{id}"

string

iri

RFC 3987

"https://bücher.example/"

string

iri-reference

RFC 3987

"/damenbekleidung-jacken-mäntel/"

string

uuid

RFC 4122

"e2ab873e-b295-11e9-9c02-…​"

string

json-pointer

RFC 6901

"/items/0/id"

string

relative-json-pointer

Relative JSON pointers

"1/id"

string

regex

regular expressions as defined in ECMA 262

"^[a-z0-9]+$"

MUST use String + Regex for number types not supported by OpenAPI [126]
If no suitable OpenAPI type is available, a string combined with a pattern must be used to describe the number type. This pattern must be a valid regular expression, according to the ECMA-262 regular expression dialect. This is mainly useful to avoid problems due to limited precision of floating point numbers.

Examples:

components:
  schemas:

    Example:
      type: object
      properties:
        amount:
          type: string
          description: Amount of money rounded to 5 cents
          pattern: '^\d*\.\d[05]$'
          example: '133.45'
        fiveDotThreeNumber:
          type: string
          description: Number having 5 digits before and 3 digits after decimal point
          pattern: '^\d{5}\.\d{3}$'
          example: '12345.678'
MUST define a format for number and integer types [171]
You must always provide one of the formats int32, int64, float or double when you define an API property of JSON type number or integer.

By this we prevent clients from guessing the precision incorrectly, and thereby changing the value unintentionally. The precision will be translated by clients and servers into the most specific language types.

MUST encode binary data as part of JSON or XML payload in base64url [239]
If binary data is transported as part of a JSON or XML payload, you must define the binary data as string typed property with binary format using base64url encoding.

MUST use standard formats for date and time properties [169]
As a specific case of MUST use standard data formats, you must use the string typed formats date, date-time, time, duration, or period for the definition of date and time properties. The formats are based on the standard RFC 3339 internet profile -- a subset of ISO 8601

Exception: For passing date/time information via standard protocol headers, HTTP RFC 7231 requires to follow the date and time specification used by the Internet Message Format RFC 5322.

As defined by the standard, time zone offset may be used, however, we recommend to only use times based on UTC without local offsets. For example 2015-05-28T14:07:17Z rather than 2015-05-28T14:07:17+00:00. From experience we have learned that zone offsets are not easy to understand and often not correctly handled. Note also that zone offsets are different from local times which may include daylight saving time (summertime). When it comes to storage, all dates should be consistently stored in UTC without a zone offset. Localization should be done locally by the services that provide user interfaces, if required.

Hint: We discourage using numerical timestamps. It typically creates issues with precision, e.g. whether to represent a timestamp as 1460062925, 1460062925000 or 1460062925.000. Date strings, while more verbose and more difficult to parse, avoid this ambiguity.

SHOULD use standard formats for country, language and currency properties [170]
As a specific case of MUST use standard data formats you should use the following standard formats:

Country codes: ISO 3166-1-alpha-2 two letter country codes indicated via format iso-3166-alpha-2 in the OpenAPI specification.

Language codes: ISO 639-1 two letter language codes indicated via format iso-639-1 in the OpenAPI specification.

Language variant tags: BCP 47 multi letter language tag indicated via format bcp47 in the OpenAPI specification. (It is a compatible extension of ISO 639-1 with additional optional information for language usage, like region, variant, script)

Currency codes: ISO 4217 three letter currency codes indicated via format iso-4217 in the OpenAPI specification.

SHOULD use content negotiation, if clients may choose from different resource representations [244]
In some situations the API supports serving different representations of a specific resource (at the same URL), e.g. JSON, PDF, TEXT, or HTML representations for an invoice resource. You should use content negotiation to support clients specifying via the standard HTTP headers, e.g. Accept, Accept-Language, Accept-Encoding which representation is best suited for their use case, for example, which language of a document, representation / content format, or content encoding. You SHOULD use standard media types like application/json or application/pdf for defining the content format in the Accept header.

SHOULD only use UUIDs if necessary [144]
Generating IDs can be a scaling problem in high frequency and near real time use cases. UUIDs solve this problem, as they can be generated without collisions in a distributed, non-coordinated way and without additional server round trips.

However, they also come with some disadvantages:

pure technical key without meaning; not ready for naming or name scope conventions that might be helpful for pragmatic reasons, i.e. we learned to use names for product attributes, instead of UUIDs

less usable, because…​

cannot be memorized and easily communicated by humans

harder to use in debugging and logging analysis

less convenient for consumer facing usage

quite long: readable representation requires 36 characters and comes with higher memory and bandwidth consumption

not ordered along their creation history and no indication of used id volume

may be in conflict with additional backward compatibility support of legacy ids

UUIDs should be avoided when not needed for large scale id generation. Instead, for instance, server side support with id generation can be preferred (POST on id resource, followed by idempotent PUT on entity resource). Usage of UUIDs is especially discouraged as primary keys of master and configuration data, like brand-ids or attribute-ids which have low id volume but widespread steering functionality.

Please note that sequential, strictly monotonically increasing numeric identifiers may reveal critical, confidential business information, such as order volume, to non-privileged clients.

In any case, we should always use string rather than number type for identifiers. This gives us more flexibility to evolve the identifier naming scheme. Accordingly, if used as identifiers, UUIDs should not be qualified using a format property.

Hint: Usually, random UUID is used - see UUID version 4 in RFC 4122. Though UUID version 1 also contains leading timestamps it is not reflected by its lexicographic sorting. This deficit is addressed by ULID (Universally Unique Lexicographically Sortable Identifier). You may favour ULID instead of UUID, for instance, for pagination use cases ordered along creation time.

5. REST Basics - URLs
Guidelines for naming and designing resource paths and query parameters.

MUST segregate APIs according to their intended purposes [135]
If, in addition to the public API, there are some non-public APIs, we encourage you to maintain two different API specifications.

API audience

SHOULD pluralize resource names [134]
Usually, a collection of resource instances is provided (at least the API should be ready here).

Exceptions:

a singular resource name is allowed in case a property (e.g. state) of a ressource is exposed as sub-ressource for simple update

the pseudo identifier self used to specify a resource endpoint where the resource identifier is provided by authorization information (see MUST identify resources and sub-resources via path segments).

MUST use URL-friendly resource identifiers [228]
To simplify encoding of resource IDs in URLs they must match the regex [a-zA-Z0-9:._\-/]*. Resource IDs only consist of ASCII strings using letters, numbers, underscore, minus, colon, period, and - on rare occasions - slash.

Note: to prevent ambiguities of unnormalized paths resource identifiers must never be empty. Consequently, support of empty strings for path parameters is forbidden.

MUST use kebab-case for path segments [129]
Path segments are restricted to ASCII kebab-case strings matching regex ^[a-z][a-z\-0-9]*$. The first character must be a lower case letter, and subsequent characters can be a letter, or a dash(-), or a number.

Example:

/shipment-orders/{shipment-order-id}
Hint: kebab-case applies to concrete path segments and not necessarily the names of path parameters.

SHOULD use normalized paths without empty path segments and trailing slashes [136]
You should not specify paths with duplicate or trailing slashes, e.g. /customers//addresses or /customers/. As a consequence, you should also not specify or use path variables with empty string values.

Note: Non standard paths have no clear semantics. As a result, behavior for non standard paths varies between different HTTP infrastructure components and libraries. This may lead to ambiguous and unexpected results during request handling and monitoring.

We recommend to implement services robust against clients not following this rule. All services should normalize request paths before processing by removing duplicate and trailing slashes. Hence, the following requests should refer to the same resource:

GET /orders/{order-id}
GET /orders/{order-id}/
GET /orders//{order-id}
MUST keep URLs verb-free [141]
The API describes resources, so the only place where actions should appear is in the HTTP methods. In URLs, use only nouns. Instead of thinking of actions (verbs), it’s often helpful to think about putting a message in a letter box: e.g., instead of having the verb cancel in the url, think of sending a message to cancel an order to the cancellations letter box on the server side.

MUST avoid actions — think about resources [138]
REST is all about your resources, so consider the domain entities that take part in web service interaction, and aim to model your API around these using the standard HTTP methods as operation indicators. For instance, if an application has to lock articles explicitly so that only one user may edit them, create an article lock with PUT or POST instead of using a lock action.

Correct

PUT /orders/{id}/status
Body: { status:cancelled }
Incorrect

PUT /orders/{id}/cancel
The added benefit is that you already have a service for browsing and filtering article locks.

MUST use domain-specific resource names [142]
API resources represent elements of the application’s domain model. Using domain-specific nomenclature for resource names helps developers to understand the functionality and basic semantics of your resources. It also reduces the need for further documentation outside the API definition. For example, "sales-order-items" is superior to "order-items" in that it clearly indicates which business object it represents.

MUST identify resources and sub-resources via path segments [143]
Some API resources may contain or reference sub-resources. Embedded sub-resources, which are not top-level resources, are parts of a higher-level resource and cannot be used outside of its scope. Sub-resources should be referenced by their name and identifier in the path segments as follows:

/resources/{resource-id}/sub-resources/{sub-resource-id}
In order to improve the consumer experience, you should aim for intuitively understandable URLs, where each sub-path is a valid reference to a resource or a set of resources. For instance, if /partners/{partner-id}/addresses/{address-id} is valid, then, in principle, also /partners/{partner-id}/addresses, /partners/{partner-id} and /partners must be valid. Examples of concrete url paths:

/shopping-carts/de:1681e6b88ec1/items/1
/shopping-carts/de:1681e6b88ec1
/content/images/9cacb4d8
/content/images
Exception: In some situations the resource identifier is not passed as a path segment but via the authorization information, e.g. an authorization token or session cookie. Here, it is reasonable to use self as pseudo-identifier path segment. For instance, you may define /employees/self or /employees/self/personal-details as resource paths —  and may additionally define endpoints that support identifier passing in the resource path, like define /employees/{empl-id} or /employees/{empl-id}/personal-details.

SHOULD limit number of resource types [146]
To keep maintenance and service evolution manageable, we should follow "functional segmentation" and "separation of concern" design principles and do not mix different business functionalities in the same API definition. In practice this means that the number of resource types exposed via an API should be limited. In this context a resource type is defined as a set of highly related resources such as a collection, its members and any direct sub-resources.

For example, the resources below would be counted as three resource types, one for customers, one for the addresses, and one for the customers' related addresses:

/customers
/customers/{id}
/customers/{id}/preferences
/customers/{id}/addresses
/customers/{id}/addresses/{addr}
/addresses
/addresses/{addr}
Note that:

We consider /customers/id/preferences part of the /customers resource type because it has a one-to-one relation to the customer without an additional identifier.

We consider /customers and /customers/id/addresses as separate resource types because /customers/id/addresses/{addr} also exists with an additional identifier for the address.

We consider /addresses and /customers/id/addresses as separate resource types because there’s no reliable way to be sure they are the same.

Given this definition, our experience is that well defined APIs involve no more than 4 to 8 resource types. There may be exceptions with more complex business domains that require more resources, but you should first check if you can split them into separate subdomains with distinct APIs.

Nevertheless one API should hold all necessary resources to model complete business processes helping clients to understand these flows.

MUST use snake_case or camelCase for query parameters [130]
Property names are restricted to ASCII snake_case or camelCase. In an API the two casings must not be mixed.

Examples snake_case:

customer_number, sales_order_number, billing_address
Examples camelCase:

customerNumber, salesOrderNumber, billingAddress
SHOULD stick to conventional query parameters [137]
If you provide query support for searching, sorting, filtering, and paginating, you should stick to the following naming conventions:

q: the parameter which specifies the query.

sort: comma-separated list of fields (as defined by MUST define collection format of header and query parameters) to define the sort order. To indicate sorting direction, fields may be prefixed with + (ascending) or - (descending), e.g. /sales-orders?sort=+id.

offset: numeric offset of the first element provided on a page representing a collection request. See REST Design - Pagination section below.

cursor: an opaque pointer to a page, never to be inspected or constructed by clients. It usually (encrypted) encodes the page position, i.e. the identifier of the first or last page element, the pagination direction, and the applied query filters to recreate the collection. See REST Design - Pagination section below.

limit: client suggested limit to restrict the number of entries on a page. See REST Design - Pagination section below.

6. REST Basics - JSON payload
These guidelines provides recommendations for defining JSON data. JSON here refers to RFC 7159 (which updates RFC 4627), the "application/json" media type and custom JSON media types defined for APIs. The guidelines clarifies some specific cases to allow JSON data to have an idiomatic form across services, teams and departements.

MUST use JSON (preferred) or XML as payload data interchange format for structured data [167]
Use JSON (preferred) or XML to represent structured (resource) data transferred with HTTP requests and responses as body payload.

Additionally, the JSON payload must conform to the more restrictive Internet JSON (RFC 7493), particularly

Section 2.1 on encoding of characters, and

Section 2.3 on object constraints.

As a consequence, a JSON payload must

use UTF-8 encoding

consist of valid Unicode strings, i.e. must not contain non-characters or surrogates, and

contain only unique member names (no duplicate names).

SHOULD use standard media types [172]
You should use standard media types (defined in media type registry of Internet Assigned Numbers Authority (IANA)) as content-type (or accept) header information. More specifically, for JSON payload you should use the standard media type application/json (or application/problem+json for SHOULD provide error information).

SHOULD pluralize array names [120]
Array names should be pluralized to indicate that they contain multiple values. This, in turn, implies that object names should be singular.

{
  customer: {
    orders: [ {...}, {...}, {...} ]
  }
}
MUST property names must be snake_case or camelCase [118]
Property names are restricted to ASCII snake_case or camelCase. In an API the two casings must not be mixed.

Examples snake_case:

customer_number, sales_order_number, billing_address
Examples camelCase:

customerNumber, salesOrderNumber, billingAddress
SHOULD declare enum values using UPPER_SNAKE_CASE or PascalCase string [240]
Enumerations should be represented as string typed OpenAPI definitions of request parameters or model properties. Enum values (using enum or x-extensible-enum) need to consistently use the UPPER_SNAKE_CASE or PascalCase format.

Exception: This rule does not apply for case sensitive values sourced from outside API definition scope, e.g. for language codes from ISO 639-1, or when declaring possible values for a rule 137 [sort parameter].

SHOULD define maps using additionalProperties [216]
A "map" here is a mapping from string keys to some other type. In JSON this is represented as an object, the key-value pairs being represented by property names and property values. In OpenAPI schema (as well as in JSON schema) they should be represented using additionalProperties with a schema defining the value type. Such an object should normally have no other defined properties.

The map keys don’t count as property names in the sense of rule 118, and can follow whatever format is natural for their domain. Please document this in the description of the map object’s schema.

Here is an example for such a map definition (the translations property):

components:
  schemas:
    Message:
      description:
        A message together with translations in several languages.
      type: object
      properties:
        message_key:
          type: string
          description: The message key.
        translations:
          description:
            The translations of this message into several languages.
            The keys are [IETF BCP-47 language tags](https://tools.ietf.org/html/bcp47).
          type: object
          additionalProperties:
            type: string
            description:
              the translation of this message into the language identified by the key.
An actual JSON object described by this might then look like this:

{ "message_key": "color",
  "translations": {
    "de": "Farbe",
    "en-US": "color",
    "en-GB": "colour",
    "eo": "koloro",
    "nl": "kleur"
  }
}
MUST use same semantics for null and absent properties [123]
OpenAPI 3.x allows to mark properties as required and as nullable to specify whether properties may be absent ({}) or null ({"example":null}). If a property is defined to be not required and nullable (see 2nd row in Table below), this rule demands that both cases must be handled in the exact same manner by specification.

The following table shows all combinations and whether the examples are valid:

required	nullable	{}	{"example":null}
true

true

❌ No

✔ Yes

false

true

✔ Yes

✔ Yes

true

false

❌ No

❌ No

false

false

✔ Yes

❌ No

The only exception to this rule is JSON Merge Patch (RFC 7396) which uses null to explicitly indicate property deletion while absent properties are ignored, i.e. not modified.

MUST not use null for boolean properties [122]
Schema based JSON properties that are by design booleans must not be presented as nulls. A boolean is essentially a closed enumeration of two values, true and false. If the content has a meaningful null value, we strongly prefer to replace the boolean with enumeration of named values or statuses - for example accepted_terms_and_conditions with enumeration values YES, NO, UNDEFINED.

MUST not use null for empty arrays [124]
Empty array values must unambiguously be represented as the empty list, [].

MUST when returning JSON, always use a JSON object as top-level data structure [110]
In a response body, you must always return a JSON object (and not e.g. an array) as a top level data structure to support future extensibility. JSON objects support compatible extension by additional attributes. This allows you to easily extend your response without breaking backwards compatibility.

7. REST Basics - HTTP requests
MUST use HTTP methods correctly [148]
Be compliant with the standardized HTTP method semantics (see HTTP/1 RFC-7230 and RFC-7230 updates from 2014) summarized as follows (only methods with 'must' and/or 'should' requirements are listed below):

GET
GET requests are used to read either a single or a collection resource.

GET requests must NOT have a request body payload (see GET with body)

GET request parameters must not contain sensitive information. Use GET with body in such cases.

GET requests on collection resources should provide sufficient filter and REST Design - Pagination mechanisms.

GET with body payload
APIs sometimes face the problem, that they have to provide extensive structured request information with GET, that may conflict with the size limits of clients, load-balancers, and servers. As we require APIs to be standard conform (request body payload in GET must be ignored on server side), API designers must check the following two options:

GET with URL encoded query parameters: when it is possible to encode the request information in query parameters, respecting the usual size limits of clients, gateways, and servers, this should be the first choice. The request information can either be provided via multiple query parameters or by a single structured URL encoded string.

POST with body payload content: when a GET with URL encoded query parameters is not possible, a POST request with body payload must be used, and explicitly documented with a hint like in the following example:

paths:
  /products:
    post:
      description: >
        [GET with body payload](https://opensource.admin.ch/restful-api-guidelines/#get-with-body) - no resources created:
        Returns all products matching the query passed as request input payload.
      requestBody:
        required: true
        content:
          ...
GET with body the lengthy structured request information must not be encoded using header parameters. From a conceptual point of view, the semantic of an operation should always be expressed by the resource names, as well as the involved path and query parameters. In other words by everything that goes into the URL. Request headers are reserved for general context information. In addition, size limits on query parameters and headers are not reliable and depend on clients, gateways, server, and actual settings. Thus, switching to headers does not solve the original problem.

PUT
PUT requests are used to update (and sometimes to create) entire resources – single or collection resources.

Important: It is good practice to keep the resource identifier management under control of the service provider and not the client, and, hence, to prefer POST for creation of (at least top-level) resources, and focus PUT on its usage for updates. However, in situations where the identifier and all resource attributes are under control of the client as input for the resource creation you should use PUT and pass the resource identifier as URL path parameter. Putting the same resource twice is required to be idempotent and to result in the same single resource instance (see MUST fulfill common method properties) without data duplication in case of repetition.

POST
POST requests are idiomatically used to create single resources on a collection resource endpoint.

Important: By using POST to create resources the resource ID must not be passed as request input date by the client, but created and maintained by the service and returned with the response payload.

Apart from resource creation, POST should be also used for scenarios that cannot be covered by the other methods sufficiently. However, in such cases make sure to document the fact that POST is used as a workaround (see e.g. GET with body).

PATCH
PATCH method extends HTTP via RFC-5789 standard to update parts of the resource objects where e.g. in contrast to PUT only a specific subset of resource fields should be changed. The syntax and semantics of the patch document is not defined in RFC-5789 and must be described in the API specification by using specific media types.

Note: since implementing PATCH correctly is a bit tricky, we strongly suggest to choose one and only one of the following patterns per endpoint (unless forced by a backwards compatible change). In preference order:

use PUT with complete objects to update a resource as long as feasible (i.e. do not use PATCH at all).

use PATCH with JSON Merge Patch standard, a specialized media type application/merge-patch+json for partial resource representation to update parts of resource objects.

use PATCH with JSON Patch standard, a specialized media type application/json-patch+json that includes instructions on how to change the resource.

use POST (with a proper description of what is happening) instead of PATCH, if the request does not modify the resource in a way defined by the semantics of the standard media types above.

DELETE
DELETE requests are used to delete resources.

Important: After deleting a resource with DELETE, a GET request on the resource is expected to either return 404 (not found) or 410 (gone) depending on how the resource is represented after deletion. Under no circumstances the resource must be accessible after this operation on its endpoint.

DELETE with query parameters
DELETE request can have query parameters. Query parameters should be used as filter parameters on a resource and not for passing context information to control the operation behavior.

DELETE /resources?param1=value1&param2=value2...&paramN=valueN
Note: When providing DELETE with query parameters, API designers must carefully document the behavior in case of (partial) failures to manage client expectations properly.

The response status code of DELETE with query parameters requests should be similar to usual DELETE requests.

DELETE with body payload
In rare cases DELETE may require additional information, that cannot be classified as filter parameters and thus should be transported via request body payload, to perform the operation. Since RFC-7231 states, that DELETE has an undefined semantic for payloads, POST must be used. In this case the POST endpoint must be documented. The response status code of DELETE with body requests should be similar to usual DELETE requests.

MUST fulfill common method properties [149]
Request methods in RESTful services can be…​

safe - the operation semantic is defined to be read-only, meaning it must not have intended side effects, i.e. changes, to the server state.

idempotent - the operation has the same intended effect on the server state, independently whether it is executed once or multiple times. Note: this does not require that the operation is returning the same response or status code.

cacheable - to indicate that responses are allowed to be stored for future reuse. In general, requests to safe methods are cacheable, if it does not require a current or authoritative response from the server.

Note: The above definitions, of intended (side) effect allows the server to provide additional state changing behavior as logging, accounting, pre- fetching, etc. However, these actual effects and state changes, must not be intended by the operation so that it can be held accountable.

Method implementations must fulfill the following basic properties according to RFC 7231:

Method	Safe	Idempotent	Cacheable
GET

✔ Yes

✔ Yes

✔ Yes

HEAD

✔ Yes

✔ Yes

✔ Yes

POST

❌ No

⚠️ No, but SHOULD consider to design the API idempotent

⚠️ May, but only if specific POST endpoint is safe. Hint: not supported by most caches.

PUT

❌ No

✔ Yes

❌ No

PATCH

❌ No

⚠️ No, but SHOULD consider to design the API idempotent

❌ No

DELETE

❌ No

✔ Yes

❌ No

OPTIONS

✔ Yes

✔ Yes

❌ No

TRACE

✔ Yes

✔ Yes

❌ No

Note: MUST Do not cache by default / Document cacheable endpoints.

SHOULD consider to design the API idempotent [229]
In many cases it is helpful or even necessary to design POST and PATCH idempotent for clients to expose conflicts and prevent resource duplicate (a.k.a. zombie resources) or lost updates, e.g. if same resources may be created or changed in parallel or multiple times. To design an idempotent API endpoint owners should consider to apply one of the following three patterns.

A resource specific conditional key provided via If-Match header in the request. The key is in general a meta information of the resource, e.g. a hash or version number, often stored with it. It allows to detect concurrent creations and updates to ensure idempotent behavior (see MAY support ETag together with If-Match/If-None-Match header).

A resource specific secondary key provided as resource property in the request body. The secondary key is stored permanently in the resource. It allows to ensure idempotent behavior by looking up the unique secondary key in case of multiple independent resource creations from different clients (see MAY use secondary key for idempotent POST design).

A client specific idempotency key provided via Idempotency-Key header in the request. The key is not part of the resource but stored temporarily pointing to the original response to ensure idempotent behavior when retrying a request (see MAY consider to support idempotency-key header).

Note: While conditional key and secondary key are focused on handling concurrent requests, the idempotency key is focused on providing the exact same responses, which is even a stronger requirement than the idempotency defined above. It can be combined with the two other patterns.

To decide, which pattern is suitable for your use case, please consult the following table showing the major properties of each pattern:

Conditional Key	Secondary Key	Idempotency Key
Applicable with

PATCH

POST

POST/PATCH

HTTP Standard

✔ Yes

❌ No

❌ No

Prevents duplicate (zombie) resources

✔ Yes

✔ Yes

❌ No

Prevents concurrent lost updates

✔ Yes

❌ No

❌ No

Supports safe retries

✔ Yes

✔ Yes

✔ Yes

Supports exact same response

❌ No

❌ No

✔ Yes

Can be inspected (by intermediaries)

✔ Yes

❌ No

✔ Yes

Usable without previous GET

❌ No

✔ Yes

✔ Yes

Note: The patterns applicable to PATCH can be applied in the same way to PUT and DELETE providing the same properties.

If you mainly aim to support safe retries, we suggest to apply conditional key and secondary key pattern before the Idempotency Key pattern.

Note, like for PUT, successful POST or PATCH returns 200 or 204 (if the resource was updated - with or without returning the resource), or 201 (if resource was created). Hence, clients can differentiate successful robust repetition from resource created server activity of idempotent POST.

MAY use secondary key for idempotent POST design [231]
The most important pattern to design POST idempotent for creation is to introduce a resource specific secondary key provided in the request body, to eliminate the problem of duplicate (a.k.a zombie) resources.

The secondary key is stored permanently in the resource as alternate key or combined key (if consisting of multiple properties) guarded by a uniqueness constraint enforced server-side, that is visible when reading the resource. The best and often naturally existing candidate is a unique foreign key, that points to another resource having one-on-one relationship with the newly created resource, e.g. a parent process identifier.

A good example here for a secondary key is the shopping cart ID in an order resource.

MUST define collection format of header and query parameters [154]
Header and query parameters allow to provide a collection of values by providing a comma-separated list of values. Providing a collection of values by repeating the parameter multiple times with different values must be avoided.

Parameter Type	Comma-separated Values	Multiple Parameters
Header

Header: value1,value2

Header: value1, Header: value2

Query

?param=value1,value2

?param=value1&param=value2

SHOULD design simple query languages using query parameters [236]
We prefer the use of query parameters to describe resource-specific query languages for the majority of APIs because it’s native to HTTP, easy to extend and has excellent implementation support in HTTP clients and web frameworks.

Query parameters should have the following aspects specified:

Reference to corresponding property, if any

Value range, e.g. inclusive vs. exclusive

Comparison semantics (equals, less than, greater than, etc)

Implications when combined with other queries, e.g. and vs. or

How query parameters are named and used is up to individual API designers. The following examples should serve as ideas:

name=John, to query for elements based on property equality

age=5, to query for elements based on logical properties

max_length=5, based on upper and lower bounds (min and max)

shorter_than=5, using terminology specific e.g. to length

created_before=2019-07-17 or not_modified_since=2019-07-17

Using terminology specific e.g. to time: before, after, since and until

We don’t advocate for or against specific names because in the end APIs should be free to choose the terminology that fits their domain the best.

SHOULD design complex query languages using JSON [237]
Minimalistic query languages based on query parameters are suitable for simple use cases with a small set of available filters that are combined in one way and one way only (e.g. and semantics). Simple query languages are generally preferred over complex ones.

Some APIs will have a need for sophisticated and more complex query languages. Dominant examples are APIs around search (incl. faceting) and product catalogs.

Aspects that set those APIs apart from the rest include but are not limited to:

Unusual high number of available filters

Dynamic filters, due to a dynamic and extensible resource model

Free choice of operators, e.g. and, or and not

APIs that qualify for a specific, complex query language are encouraged to use nested JSON data structures and define them using OpenAPI directly. They provide the following benefits:

Data structures are easy to use for clients

No special library support necessary

No need for string concatenation or manual escaping

Data structures are easy to use for servers

No special tokenizers needed

Semantics are attached to data structures rather than text tokens

Consistent with other HTTP methods

API is fully defined in OpenAPI

No external documents or grammars required

Existing means are familiar to everyone

JSON-specific rules and most certainly needs to make use of the GET-with-body pattern.

Example
The following JSON document should give you an idea of what a structured query might look like.

{
  "and": {
    "name": {
      "match": "Alice"
    },
    "age": {
      "or": {
        "range": {
          ">": 25,
          "<=": 50
        },
        "=": 65
      }
    }
  }
}
Feel free to also get some inspiration from:

Elastic Search: Query DSL

GraphQL: Queries
