
# Federal Administration API Guidelines

```plaintext
Table of Contents
1. Introduction
   Conventions used in these guidelines
2. General guidelines
   MUST provide API specification using OpenAPI
3. REST Basics - Meta information
   MUST contain API meta information
   MUST provide API audience
4. REST Basics - Data formats
   MUST use standard data formats
   MUST use String + Regex for number types not supported by OpenAPI
   MUST define a format for number and integer types
   MUST encode binary data as part of JSON or XML payload in base64url
   MUST use standard formats for date and time properties
   SHOULD use standard formats for country, language and currency properties
   SHOULD use content negotiation, if clients may choose from different resource representations
   SHOULD only use UUIDs if necessary
5. REST Basics - URLs
   MUST segregate APIs according to their intended purposes
   SHOULD pluralize resource names
   MUST use URL-friendly resource identifiers
   MUST use kebab-case for path segments
   SHOULD use normalized paths without empty path segments and trailing slashes
   MUST keep URLs verb-free
   MUST avoid actions — think about resources
   MUST use domain-specific resource names
   MUST identify resources and sub-resources via path segments
   SHOULD limit number of resource types
   MUST use snake_case or camelCase for query parameters
   SHOULD stick to conventional query parameters
6. REST Basics - JSON payload
   MUST use JSON (preferred) or XML as payload data interchange format for structured data
   SHOULD use standard media types
   SHOULD pluralize array names
   MUST property names must be snake_case or camelCase
   SHOULD declare enum values using UPPER_SNAKE_CASE or PascalCase string
   SHOULD define maps using additionalProperties
   MUST use same semantics for null and absent properties
   MUST not use null for boolean properties
   MUST not use null for empty arrays
   MUST when returning JSON, always use a JSON object as top-level data structure
7. REST Basics - HTTP requests
   MUST use HTTP methods correctly
   MUST fulfill common method properties
   SHOULD consider to design the API idempotent
   MAY use secondary key for idempotent POST design
   MUST define collection format of header and query parameters
   SHOULD design simple query languages using query parameters
   SHOULD design complex query languages using JSON
8. REST Basics - HTTP status codes
   MUST use official HTTP status codes
   MUST specify success and error responses
   SHOULD only use most common HTTP status codes
   MUST use most specific HTTP status codes
   MUST use code 207 for batch or bulk requests
   MUST use code 429 with headers for rate limits
   SHOULD provide error information
   MUST not expose stack traces
9. REST Basics - HTTP headers
   SHOULD use standard headers
   SHOULD use kebab-case with lowercase separate words for HTTP header names
   MAY support ETag together with If-Match/If-None-Match header
   MAY consider to support idempotency-key header
10. REST Design - Hypermedia
    MUST use REST maturity level 2
    MAY use REST maturity level 3 - HATEOAS
    MUST use full, absolute URI for resource identification
11. REST Design - Performance
    MAY support partial responses via filtering
    MUST Do not cache by default / Document cacheable endpoints
12. REST Design - Pagination
    MUST support pagination for large result sets
    SHOULD prefer cursor-based pagination
13. REST Design - Compatibility
    MUST not break backward compatibility
    SHOULD prefer compatible extensions
    MUST treat OpenAPI specification as open for extension by default
    SHOULD avoid versioning
    SHOULD use URL versioning
14. REST Design - Deprecation
    MUST reflect deprecation in API specifications
    SHOULD add Deprecation and Sunset header to responses
Appendix A: Case Styles
Appendix B: References
   OpenAPI specification
   Publications, specifications, and standards
   Dissertations
   Books
   Blogs
Appendix C: Changelog
   Rule Changes
```
## 1. Introduction
These REST API Guidelines pick up where the federal API architecture leaves off. It follows the vision "As a modern administration, we simplify access to our government services for our partners by making services usable through any electronic means". These are design guidelines for RESTful APIs and we encourage all API developers in the federal administration to follow them to ensure that our APIs:

* are easy to understand and learn

* are general and abstracted from specific implementation and use cases

* are robust and easy to use

* have a common look and feel

* follow a consistent RESTful style and syntax

* are consistent with other teams’ APIs

RESTful APIs (Representational State Transferful Application Programming Interfaces) are APIs (Application Programming Interfaces) that adhere to the principles and constraints of the REST architectural style. These APIs are designed to facilitate communication and data exchange between different software systems over the web. To learn more about REST, we refer you to [https://en.wikipedia.org/wiki/REST](https://en.wikipedia.org/wiki/REST), other online sources or relevant specialist literature.

The guidelines are based on [Zalando’s RESTful API and Event Guidelines](https://opensource.zalando.com/restful-api-guidelines/). Thanks to Zalando for their valuable work and for making the guidelines available.

### Conventions used in these guidelines

The requirement level keywords "MUST", "MUST NOT", "REQUIRED", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" used in this document (case insensitive) are to be interpreted as described in [RFC 2119](https://www.ietf.org/rfc/rfc2119.txt)
.

## 2. General guidelines
The titles are marked with the corresponding labels: MUST, SHOULD, MAY.

### MUST provide API specification using OpenAPI [101]

We use the [OpenAPI specification](https://swagger.io/specification/) as a standard to define API specification files. API designers are required to provide the API specification using a single **self-contained YAML** file to improve readability. We use **OpenAPI 3.0** or later.

The API specification files should be subject to version control using a source code management system - best together with the implementing sources.

## 3. REST Basics - Meta information
### MUST contain API meta information [218]

API specifications must contain the following OpenAPI meta information to allow for API management:

* #/info/title functional descriptive name of the API

* #/info/version to distinguish API specifications versions following [semantic rules](https://semver.org/spec/v2.0.0.html). The version of the OpenAPI document (which is distinct from the OpenAPI Specification version or the API implementation version).

Following OpenAPI extension properties **should** be provided in addition:

* #/info/description containing a proper description of the API

* #/info/x-audience intended target audience of the API [see rule 219](#must-provide-api-audience-219)


* #/info/license/{name,identifier,url} the license information for the exposed API.

* #/info/contact/{name,url,email} the contact information for the exposed API.

### MUST provide API audience 219

Each API must be classified with respect to the intended target **audience** supposed to consume the API, to facilitate differentiated standards on APIs for discoverability, changeability, quality of design and documentation, as well as permission granting. We differentiate the following API audience groups with clear organisational and legal boundaries:

**public**

APIs with this audience can be accessed by anyone with Internet access.

**partner**

APIs with this audience can be accessed by applications of business partners of the administrative unit owning the API and the administrative unit itself.

**private**

APIs with this audience can only be accessed by applications of the administrative unit itself

The API audience is provided as API meta information in the info-block of the OpenAPI specification and must conform to the following specification:

```plaintext
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
```
    
**Note**: Exactly **one audience** per API specification is allowed as shown in the following example. For this reason a smaller audience group is intentionally included in the wider group and thus does not need to be declared additionally. If parts of your API have a different target audience, we recommend to split API specifications along the target audience — even if this creates redundancies.

Example:
```plaintext
openapi: 3.0.1
info:
  x-audience: public
  title: Tax information service
  description: API for <...>
  version: 1.2.4
  <...>
```
For details and more information on audience groups see the {api-audience-narrative}[API Audience narrative (internal_link)].

## 4. REST Basics - Data formats
### MUST use standard data formats [238]

[Open API](https://github.com/OAI/OpenAPI-Specification/blob/main/versions/3.1.0.md#data-types) (based on [JSON Schema Validation vocabulary](https://datatracker.ietf.org/doc/html/draft-bhutton-json-schema-validation-00#section-7.3)) defines formats from ISO and IETF standards for date/time, integers/numbers and binary data. You **must** use these formats, whenever applicable:

| OpenAPI type | OpenAPI format | Specification | Example |
|--------------|----------------|---------------|---------|
| integer | int32 | 4 byte signed integer between -2^31 and 2^31-1 | 7721071004 |
| integer | int64 | 8 byte signed integer between -2^63 and 2^63-1 | 772107100456824 |
| number | float | binary32 single precision decimal number — see [IEEE 754-2008/ISO 60559:2011](https://en.wikipedia.org/wiki/IEEE_754) | 3.1415927 |
| number | double | binary64 double precision decimal number — see [IEEE 754-2008/ISO 60559:2011](https://en.wikipedia.org/wiki/IEEE_754) | 3.141592653589793 |
| string | byte | base64url encoded byte following [RFC 7493 Section 4.4](https://datatracker.ietf.org/doc/html/rfc7493#section-4.4) | "VA==" |
| string | binary | base64url encoded byte sequence following [RFC 7493 Section 4.4](https://datatracker.ietf.org/doc/html/rfc7493#section-4.4)) | "VGVzdA==" |
| string | date | RFC 3339 internet profile — subset of ISO 8601 | "2019-07-30" |
| string | date-time | [RFC 3339](https://datatracker.ietf.org/doc/html/rfc3339) internet profile — subset of [ISO 8601](https://datatracker.ietf.org/doc/html/rfc3339#ref-ISO8601) | "2019-07-30T06:43:40.252Z" |
| string | time | [RFC 3339](https://datatracker.ietf.org/doc/html/rfc3339) internet profile — subset of [ISO 8601](https://datatracker.ietf.org/doc/html/rfc3339#ref-ISO8601) | "06:43:40.252Z" |
| string | duration | [RFC 3339](https://datatracker.ietf.org/doc/html/rfc3339) internet profile — subset of [ISO 8601](https://datatracker.ietf.org/doc/html/rfc3339#ref-ISO8601) | "P1DT30H4S" |
| string | period | [RFC 3339](https://datatracker.ietf.org/doc/html/rfc3339) internet profile — subset of [ISO 8601](https://datatracker.ietf.org/doc/html/rfc3339#ref-ISO8601) | "2019-07-30T06:43:40.252Z/PT3H" |
| string | password | "secret" | "secret" |
| string | email | [RFC 5322](https://datatracker.ietf.org/doc/html/rfc5322) | "example@admin.ch" |
| string | idn-email | [RFC 6531](https://datatracker.ietf.org/doc/html/rfc6531) | "hello@bücher.example" |
| string | hostname | [RFC 1034](https://datatracker.ietf.org/doc/html/rfc1034) | "www.admin.ch" |
| string | idn-hostname | [RFC 5890](https://datatracker.ietf.org/doc/html/rfc5890) | "bücher.example" |
| string | ipv4 | [RFC 2673](https://datatracker.ietf.org/doc/html/rfc2673) | "104.75.173.179" |
| string | ipv6 | [RFC 4291](https://datatracker.ietf.org/doc/html/rfc4291) | "2600:1401:2::8a" |
| string | uri | [RFC 3986](https://datatracker.ietf.org/doc/html/rfc3986) | "https://www.admin.ch/" |
| string | uri-reference | [RFC 3986](https://datatracker.ietf.org/doc/html/rfc3986) | "/clothing/" |
| string | uri-template | [RFC 6570](https://datatracker.ietf.org/doc/html/rfc6570) | "/users/{id}" |
| string | iri | [RFC 3987](https://datatracker.ietf.org/doc/html/rfc3987) | "https://bücher.example/" |
| string | iri-reference | [RFC 3987](https://datatracker.ietf.org/doc/html/rfc3987) | "/damenbekleidung-jacken-mäntel/" |
| string | uuid | [RFC 4122](https://datatracker.ietf.org/doc/html/rfc4122) | "e2ab873e-b295-11e9-9c02-…" |
| string | json-pointer | [RFC 6901](https://datatracker.ietf.org/doc/html/rfc6901) | "/items/0/id" |
| string | relative-json-pointer | [Relative JSON pointers](https://datatracker.ietf.org/doc/html/draft-handrews-relative-json-pointer) | "1/id" |
| string | regex | regular expressions as defined in [ECMA 262](https://ecma-international.org/publications/files/ECMA-ST/Ecma-262.pdf) | "^[a-z0-9]+$" |


### MUST use String + Regex for number types not supported by OpenAPI [126]

If no suitable OpenAPI type is available, a string combined with a pattern must be used to describe the number type. This pattern must be a valid regular expression, according to the ECMA-262 regular expression dialect. This is mainly useful to avoid problems due to limited precision of floating point numbers.

Examples:
```plaintext
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
```
          
### MUST define a format for number and integer types [171]

You must always provide one of the formats ``` int32 ```, ``` int64 ```, ``` float ``` or ``` double ``` when you define an API property of JSON type ``` number ``` or ``` integer ```.

By this we prevent clients from guessing the precision incorrectly, and thereby changing the value unintentionally. The precision will be translated by clients and servers into the most specific language types.

### MUST encode binary data as part of JSON or XML payload in ``` base64url ```[239]

If binary data is transported as part of a JSON or XML payload, you must define the binary data as string typed property with binary format using base64url encoding.

### MUST use standard formats for date and time properties [169]

As a specific case of MUST [use standard data formats](file:///C:/Users/U80872465/AppData/Local/Temp/07c5eb20-32a2-4dad-8870-7c64f6b72fda_output.zip.fda/output/index.html#238), you must use the ```string``` typed formats ```date```, ```date-time```, ```time```, ```duration```, or ```period``` for the definition of date and time properties. The formats are based on the standard [RFC 3339](https://datatracker.ietf.org/doc/html/rfc3339) internet profile -- a subset of [ISO 8601](https://datatracker.ietf.org/doc/html/rfc3339#ref-ISO8601)

**Exception**: For passing date/time information via standard protocol headers, HTTP [RFC 7231](https://datatracker.ietf.org/doc/html/rfc7231#section-7.1.1.1) requires to follow the date and time specification used by the Internet Message Format [RFC 5322](https://datatracker.ietf.org/doc/html/rfc5322).

As defined by the standard, time zone offset may be used, however, we recommend to only use times based on UTC without local offsets. For example ```2015-05-28T14:07:17Z``` rather than ```2015-05-28T14:07:17+00:00```. From experience we have learned that zone offsets are not easy to understand and often not correctly handled. Note also that zone offsets are different from local times which may include daylight saving time (summertime). When it comes to storage, all dates should be consistently stored in UTC without a zone offset. Localization should be done locally by the services that provide user interfaces, if required.

**Hint**: We discourage using numerical timestamps. It typically creates issues with precision, e.g. whether to represent a timestamp as 1460062925, 1460062925000 or 1460062925.000. Date strings, while more verbose and more difficult to parse, avoid this ambiguity.

### SHOULD use standard formats for country, language and currency properties [170]

As a specific case of **MUST** [use standard data formats](file:///C:/Users/U80872465/AppData/Local/Temp/07c5eb20-32a2-4dad-8870-7c64f6b72fda_output.zip.fda/output/index.html#238) you should use the following standard formats:

Country codes: [ISO-3166-alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) two letter country codes indicated via format ```iso-3166-alpha-2``` in the OpenAPI specification.

Language codes: [ISO-639-1](https://en.wikipedia.org/wiki/List_of_ISO_639_language_codes) two letter language codes indicated via format ```iso-639-1``` in the OpenAPI specification.

Language variant tags: [BCP47](https://www.rfc-editor.org/info/bcp47) multi letter language tag indicated via format ```bcp47``` in the OpenAPI specification. (It is a compatible extension of [iso-639-1](https://en.wikipedia.org/wiki/List_of_ISO_639_language_codes) with additional optional information for language usage, like region, variant, script)

Currency codes: [ISO-4217](https://en.wikipedia.org/wiki/ISO_4217) three letter currency codes indicated via format ```iso-4217``` in the OpenAPI specification.

### SHOULD use content negotiation, if clients may choose from different resource representations [244]

In some situations the API supports serving different representations of a specific resource (at the same URL), e.g. JSON, PDF, TEXT, or HTML representations for an invoice resource. You should use [content negotiation](https://en.wikipedia.org/wiki/Content_negotiation) to support clients specifying via the standard HTTP headers, e.g. ```Accept```, ```Accept-Language```, ```Accept-Encoding``` which representation is best suited for their use case, for example, which language of a document, representation / content format, or content encoding. You SHOULD [use standard media types](file:///C:/Users/U80872465/AppData/Local/Temp/07c5eb20-32a2-4dad-8870-7c64f6b72fda_output.zip.fda/output/index.html#172) like ```application/json``` or ```application/pdf``` for defining the content format in the ```Accept``` header.

### SHOULD only use UUIDs if necessary [144]

Generating IDs can be a scaling problem in high frequency and near real time use cases. UUIDs solve this problem, as they can be generated without collisions in a distributed, non-coordinated way and without additional server round trips.

However, they also come with some disadvantages:

* pure technical key without meaning; not ready for naming or name scope conventions that might be helpful for pragmatic reasons, i.e. we learned to use names for product attributes, instead of UUIDs

* less usable, because…​

   * cannot be memorized and easily communicated by humans

   * harder to use in debugging and logging analysis

   * less convenient for consumer facing usage

* quite long: readable representation requires 36 characters and comes with higher memory and bandwidth consumption

* not ordered along their creation history and no indication of used id volume

* may be in conflict with additional backward compatibility support of legacy ids

UUIDs should be avoided when not needed for large scale id generation. Instead, for instance, server side support with id generation can be preferred (```POST``` on id resource, followed by idempotent ```PUT``` on entity resource). Usage of UUIDs is especially discouraged as primary keys of master and configuration data, like brand-ids or attribute-ids which have low id volume but widespread steering functionality.

Please note that sequential, strictly monotonically increasing numeric identifiers may reveal critical, confidential business information, such as order volume, to non-privileged clients.

In any case, we should always use string rather than number type for identifiers. This gives us more flexibility to evolve the identifier naming scheme. Accordingly, if used as identifiers, UUIDs should not be qualified using a format property.

Hint: Usually, random UUID is used - see UUID version 4 in [RFC 4122](https://datatracker.ietf.org/doc/html/rfc4122). Though UUID version 1 also contains leading timestamps it is not reflected by its lexicographic sorting. This deficit is addressed by ULID (Universally Unique Lexicographically Sortable Identifier). You may favour ULID instead of UUID, for instance, for pagination use cases ordered along creation time.

## 5. REST Basics - URLs
Guidelines for naming and designing resource paths and query parameters.

### MUST segregate APIs according to their intended purposes [135]

If, in addition to the public API, there are some non-public APIs, we encourage you to maintain two different API specifications.

[API audience](file:///C:/Users/U80872465/AppData/Local/Temp/07c5eb20-32a2-4dad-8870-7c64f6b72fda_output.zip.fda/output/index.html#219)

### SHOULD pluralize resource names [134]

Usually, a collection of resource instances is provided (at least the API should be ready here).

**Exceptions:**

a singular resource name is allowed in case a property (e.g. state) of a resource is exposed as sub-resource for simple update

the pseudo identifier ```self``` used to specify a resource endpoint where the resource identifier is provided by authorization information (see **MUST** [identify resources and sub-resources via path segments](file:///C:/Users/U80872465/AppData/Local/Temp/07c5eb20-32a2-4dad-8870-7c64f6b72fda_output.zip.fda/output/index.html#143).

### MUST use URL-friendly resource identifiers [228]

To simplify encoding of resource IDs in URLs they must match the regex ```[a-zA-Z0-9:._\-/]*```. Resource IDs only consist of ASCII strings using letters, numbers, underscore, minus, colon, period, and - on rare occasions - slash.

**Note:** to prevent ambiguities of [unnormalized paths](file:///C:/Users/U80872465/AppData/Local/Temp/07c5eb20-32a2-4dad-8870-7c64f6b72fda_output.zip.fda/output/index.html#136) resource identifiers must never be empty. Consequently, support of empty strings for path parameters is forbidden.

### MUST use kebab-case for path segments [129]

Path segments are restricted to ASCII kebab-case strings matching regex ^[a-z][a-z\-0-9]*$. The first character must be a lower case letter, and subsequent characters can be a letter, or a dash(```-```), or a number.

Example:

```plaintext
/shipment-orders/{shipment-order-id}
```

**Hint**: kebab-case applies to concrete path segments and not necessarily the names of path parameters.

### SHOULD use normalized paths without empty path segments and trailing slashes [136]

You should not specify paths with duplicate or trailing slashes, e.g. `/customers//addresses` or `/customers/`. As a consequence, you should also not specify or use path variables with empty string values.

**Note**: Non standard paths have no clear semantics. As a result, behavior for non standard paths varies between different HTTP infrastructure components and libraries. This may lead to ambiguous and unexpected results during request handling and monitoring.

We recommend to implement services robust against clients not following this rule. All services **should** [normalize](https://en.wikipedia.org/wiki/URI_normalization) request paths before processing by removing duplicate and trailing slashes. Hence, the following requests should refer to the same resource:
```plaintext
GET /orders/{order-id}
GET /orders/{order-id}/
GET /orders//{order-id}
```

### MUST keep URLs verb-free [141]
The API describes resources, so the only place where actions should appear is in the HTTP methods. In URLs, use only nouns. Instead of thinking of actions (verbs), it’s often helpful to think about putting a message in a letter box: e.g., instead of having the verb cancel in the url, think of sending a message to cancel an order to the cancellations letter box on the server side.

### MUST avoid actions — think about resources [138]

REST is all about your resources, so consider the domain entities that take part in web service interaction, and aim to model your API around these using the standard HTTP methods as operation indicators. For instance, if an application has to lock articles explicitly so that only one user may edit them, create an article lock with `PUT` or `POST` instead of using a lock action.

Correct
```plaintext
PUT /orders/{id}/status
Body: { status:cancelled }
```
Incorrect

```plaintext
PUT /orders/{id}/cancel
```
The added benefit is that you already have a service for browsing and filtering article locks.

### MUST use domain-specific resource names [142]

API resources represent elements of the application’s domain model. Using domain-specific nomenclature for resource names helps developers to understand the functionality and basic semantics of your resources. It also reduces the need for further documentation outside the API definition. For example, "sales-order-items" is superior to "order-items" in that it clearly indicates which business object it represents.

### MUST identify resources and sub-resources via path segments [143]

Some API resources may contain or reference sub-resources. Embedded sub-resources, which are not top-level resources, are parts of a higher-level resource and cannot be used outside of its scope. Sub-resources should be referenced by their name and identifier in the path segments as follows:
```plaintext
/resources/{resource-id}/sub-resources/{sub-resource-id}
```
In order to improve the consumer experience, you should aim for intuitively understandable URLs, where each sub-path is a valid reference to a resource or a set of resources. For instance, if `/partners/{partner-id}/addresses/{address-id}` is valid, then, in principle, also `/partners/{partner-id}/addresses`, `/partners/{partner-id}` and `/partners` must be valid. Examples of concrete url paths:

```plaintext
/shopping-carts/de:1681e6b88ec1/items/1
/shopping-carts/de:1681e6b88ec1
/content/images/9cacb4d8
/content/images
```
**Exception:** In some situations the resource identifier is not passed as a path segment but via the authorization information, e.g. an authorization token or session cookie. Here, it is reasonable to use self as pseudo-identifier path segment. For instance, you may define `/employees/self` or `/employees/self/personal-details` as resource paths —  and may additionally define endpoints that support identifier passing in the resource path, like define `/employees/{empl-id}` or `/employees/{empl-id}/personal-details`.

### SHOULD limit number of resource types [146]

To keep maintenance and service evolution manageable, we should follow "functional segmentation" and "separation of concern" design principles and do not mix different business functionalities in the same API definition. In practice this means that the number of resource types exposed via an API should be limited. In this context a resource type is defined as a set of highly related resources such as a collection, its members and any direct sub-resources.

For example, the resources below would be counted as three resource types, one for customers, one for the addresses, and one for the customers' related addresses:

```plaintext
/customers
/customers/{id}
/customers/{id}/preferences
/customers/{id}/addresses
/customers/{id}/addresses/{addr}
/addresses
/addresses/{addr}
```
Note that:

We consider `/customers/id/preferences` part of the `/customers` resource type because it has a one-to-one relation to the customer without an additional identifier.

We consider `/customers` and `/customers/id/addresses` as separate resource types because `/customers/id/addresses/{addr}` also exists with an additional identifier for the address.

We consider `/addresses` and `/customers/id/addresses` as separate resource types because there’s no reliable way to be sure they are the same.

Given this definition, our experience is that well defined APIs involve no more than 4 to 8 resource types. There may be exceptions with more complex business domains that require more resources, but you should first check if you can split them into separate subdomains with distinct APIs.

Nevertheless one API should hold all necessary resources to model complete business processes helping clients to understand these flows.

### MUST use snake_case or camelCase for query parameters [130]

Property names are restricted to ASCII snake_case or camelCase. In an API the two casings must not be mixed.

Examples snake_case:

```plaintext
customer_number, sales_order_number, billing_address
```
```plaintext
Examples camelCase:

```plaintext
customerNumber, salesOrderNumber, billingAddress
```
### SHOULD stick to conventional query parameters [137]

If you provide query support for searching, sorting, filtering, and paginating, you should stick to the following naming conventions:

* `q`: the parameter which specifies the query.

* `sort`: comma-separated list of fields (as defined by MUST define collection format of header and query parameters) to define the sort order. To indicate sorting direction, fields may be prefixed with `+` (ascending) or `-` (descending), e.g. /sales-orders?sort=+id.

* `offset`: numeric offset of the first element provided on a page representing a collection request. See REST Design - Pagination section below.

* `cursor`: an opaque pointer to a page, never to be inspected or constructed by clients. It usually (encrypted) encodes the page position, i.e. the identifier of the first or last page element, the pagination direction, and the applied query filters to recreate the collection. See REST Design - Pagination section below.

* `limit`: client suggested limit to restrict the number of entries on a page. See REST Design - Pagination section below.

## 6. REST Basics - JSON payload
These guidelines provides recommendations for defining JSON data. JSON here refers to [RFC 7159](https://datatracker.ietf.org/doc/html/rfc7159) (which updates [RFC 4627]([url]((https://datatracker.ietf.org/doc/html/rfc4627)), the "application/json" media type and custom JSON media types defined for APIs. The guidelines clarifies some specific cases to allow JSON data to have an idiomatic form across services, teams and departements.

### MUST use JSON (preferred) or XML as payload data interchange format for structured data [167]

Use JSON (preferred) or XML to represent structured (resource) data transferred with HTTP requests and responses as body payload.

Additionally, the JSON payload must conform to the more restrictive Internet JSON ([RFC 7493]([url](https://datatracker.ietf.org/doc/html/rfc7493)), particularly

* [Section 2.1](https://datatracker.ietf.org/doc/html/rfc7493#section-2.1) on encoding of characters, and

* [Section 2.3](https://datatracker.ietf.org/doc/html/rfc7493#section-2.3) on object constraints.

As a consequence, a JSON payload must

* use `UTF-8` [encoding](https://datatracker.ietf.org/doc/html/rfc7493#section-2.1)

* consist of [valid Unicode strings](https://datatracker.ietf.org/doc/html/rfc7493#section-2.1), i.e. must not contain non-characters or surrogates, and

contain only [unique member names](https://datatracker.ietf.org/doc/html/rfc7493#section-2.3) (no duplicate names).

### SHOULD use standard media types [172]

You should use standard media types (defined in [media type registry](https://www.iana.org/assignments/media-types/media-types.xhtml) of Internet Assigned Numbers Authority (IANA)) as `content-type` (or `accept`) header information. More specifically, for JSON payload you should use the standard media type `application/json` (or `application/problem+json` for *SHOULD* provide error information).

### SHOULD pluralize array names [120]

Array names should be pluralized to indicate that they contain multiple values. This, in turn, implies that object names should be singular.

```plaintext
{
  customer: {
    orders: [ {...}, {...}, {...} ]
  }
}
```

 ### MUST property names must be snake_case or camelCase [118]

Property names are restricted to ASCII snake_case or camelCase. In an API the two casings must not be mixed.

Examples snake_case:

```plaintext
customer_number, sales_order_number, billing_address
```
```plaintext
Examples camelCase:

```plaintext
customerNumber, salesOrderNumber, billingAddress
```

### SHOULD declare enum values using UPPER_SNAKE_CASE or PascalCase string [240]
Enumerations should be represented as `string` typed OpenAPI definitions of request parameters or model properties. Enum values (using `enum` or `x-extensible-enum`) need to consistently use the UPPER_SNAKE_CASE or PascalCase format.

**Exception:** This rule does not apply for case sensitive values sourced from outside API definition scope, e.g. for language codes from [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639_language_codes), or when declaring possible values for a rule 137 [`sort` parameter].

### SHOULD define maps using additionalProperties [216]

A "map" here is a mapping from string keys to some other type. In JSON this is represented as an object, the key-value pairs being represented by property names and property values. In OpenAPI schema (as well as in JSON schema) they should be represented using additionalProperties with a schema defining the value type. Such an object should normally have no other defined properties.

The map keys don’t count as property names in the sense of rule 118, and can follow whatever format is natural for their domain. Please document this in the description of the map object’s schema.

Here is an example for such a map definition (the `translations` property):

```plaintext
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
```
```plaintext
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
```
 ### MUST use same semantics for null and absent properties [123]

OpenAPI 3.x allows to mark properties as `required` and as `nullable` to specify whether properties may be absent (`{}`) or `null` (`{"example":null}`). If a property is defined to be not `required` and `nullable` (see 2nd row in Table below), this rule demands that both cases must be handled in the exact same manner by specification.

The following table shows all combinations and whether the examples are valid:

| required | nullable | {} | {"example":null} |
|----------|----------|----|------------------|
| true     | true     | ❌ No  | ✔ Yes           |
| false    | true     | ✔ Yes | ✔ Yes           |
| true     | false    | ❌ No  | ❌ No           |
| false    | false    | ✔ Yes | ❌ No           |


The only exception to this rule is JSON Merge Patch ([RFC 7396](https://datatracker.ietf.org/doc/html/rfc7396)) which uses `null` to explicitly indicate property deletion while absent properties are ignored, i.e. not modified.

### MUST not use `null` for boolean properties [122]

Schema based JSON properties that are by design booleans must not be presented as nulls. A boolean is essentially a closed enumeration of two values, true and false. If the content has a meaningful null value, we strongly prefer to replace the boolean with enumeration of named values or statuses - for example accepted_terms_and_conditions with enumeration values YES, NO, UNDEFINED.

### MUST not use `null` for empty arrays [124]

Empty array values must unambiguously be represented as the empty list, `[]`.

### MUST when returning JSON, always use a JSON object as top-level data structure [110]

In a response body, you must always return a JSON object (and not e.g. an array) as a top level data structure to support future extensibility. JSON objects support compatible extension by additional attributes. This allows you to easily extend your response without breaking backwards compatibility.

## 7. REST Basics - HTTP requests
### MUST use HTTP methods correctly [148]

Be compliant with the standardized HTTP method semantics (see HTTP/1 [RFC-7230](https://datatracker.ietf.org/doc/html/rfc7230) and [RFC-7230](https://datatracker.ietf.org/doc/html/rfc7230) updates from 2014) summarized as follows (only methods with 'must' and/or 'should' requirements are listed below):

### GET
`GET` requests are used to read either a single or a collection resource.

* `GET` requests must NOT have a request body payload (see `GET with body`)

* `GET` request parameters must not contain sensitive information. Use `GET with body` in such cases.

* `GET` requests on collection resources should provide sufficient filter and REST Design - Pagination mechanisms.

### GET with body payload

APIs sometimes face the problem, that they have to provide extensive structured request information with `GET`, that may conflict with the size limits of clients, load-balancers, and servers. As we require APIs to be standard conform (request body payload in `GET` must be ignored on server side), API designers must check the following two options:

1. `GET` with URL encoded query parameters: when it is possible to encode the request information in query parameters, respecting the usual size limits of clients, gateways, and servers, this should be the first choice. The request information can either be provided via multiple query parameters or by a single structured URL encoded string.

2. `POST` with body payload content: when a `GET` with URL encoded query parameters is not possible, a `POST` request with body payload must be used, and explicitly documented with a hint like in the following example:

```plaintext
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
```

* `GET with body` the lengthy structured request information must not be encoded using header parameters. From a conceptual point of view, the semantic of an operation should always be expressed by the resource names, as well as the involved path and query parameters. In other words by everything that goes into the URL. Request headers are reserved for general context information. In addition, size limits on query parameters and headers are not reliable and depend on clients, gateways, server, and actual settings. Thus, switching to headers does not solve the original problem.

 ### PUT

`PUT` requests are used to **update** (and sometimes to create) **entire** resources – single or collection resources.

**Important**: It is good practice to keep the resource identifier management under control of the service provider and not the client, and, hence, to prefer `POST` for creation of (at least top-level) resources, and focus `PUT` on its usage for updates. However, in situations where the identifier and all resource attributes are under control of the client as input for the resource creation you should use `PUT` and pass the resource identifier as URL path parameter. Putting the same resource twice is required to be idempotent and to result in the same single resource instance (see **MUST** fulfill common method properties) without data duplication in case of repetition.

### POST
`POST` requests are idiomatically used to **create** single resources on a collection resource endpoint.

**Important**: By using `POST` to create resources the resource ID must not be passed as request input date by the client, but created and maintained by the service and returned with the response payload.

Apart from resource creation, `POST` should be also used for scenarios that cannot be covered by the other methods sufficiently. However, in such cases make sure to document the fact that `POST` is used as a workaround (see e.g. `GET with body`).

### PATCH
`PATCH` method extends HTTP via [RFC-5789](https://datatracker.ietf.org/doc/html/rfc5789) standard to update parts of the resource objects where e.g. in contrast to `PUT` only a specific subset of resource fields should be changed. The syntax and semantics of the patch document is not defined in [RFC-5789](https://datatracker.ietf.org/doc/html/rfc5789) and must be described in the API specification by using specific media types.

**Note**: since implementing `PATCH` correctly is a bit tricky, we strongly suggest to choose one and only one of the following patterns per endpoint (unless forced by a backwards compatible change). In preference order:

1. use `PUT` with complete objects to update a resource as long as feasible (i.e. do not use `PATCH` at all).

2. use `PATCH` with [JSON Merge Patch](https://datatracker.ietf.org/doc/html/rfc7396) standard, a specialized media type `application/merge-patch+json` for partial resource representation to update parts of resource objects.

3. use `PATCH` with [JSON Patch](https://datatracker.ietf.org/doc/html/rfc6902) standard, a specialized media type application/json-patch+json that includes instructions on how to change the resource.

4. use `POST` (with a proper description of what is happening) instead of `PATCH`, if the request does not modify the resource in a way defined by the semantics of the standard media types above.

### DELETE
`DELETE` requests are used to **delete** resources.

**Important:** After deleting a resource with `DELETE`, a `GET` request on the resource is expected to either return **404** (not found) or **410** (gone) depending on how the resource is represented after deletion. Under no circumstances the resource must be accessible after this operation on its endpoint.

### DELETE with query parameters
`DELETE` request can have query parameters. Query parameters should be used as filter parameters on a resource and not for passing context information to control the operation behavior.

```plaintext
DELETE /resources?param1=value1&param2=value2...&paramN=valueN
```
**Note:** When providing `DELETE` with query parameters, API designers must carefully document the behavior in case of (partial) failures to manage client expectations properly.

The response status code of `DELETE` with query parameters requests should be similar to usual `DELETE` requests.

### DELETE with body payload
In rare cases `DELETE` may require additional information, that cannot be classified as filter parameters and thus should be transported via request body payload, to perform the operation. Since [RFC-7231](https://datatracker.ietf.org/doc/html/rfc7231#section-4.3.5) states, that `DELETE` has an undefined semantic for payloads, `POST` must be used. In this case the POST endpoint must be documented. The response status code of `DELETE` with body requests should be similar to usual `DELETE` requests.

### MUST fulfill common method properties [149]
Request methods in RESTful services can be…​

* [safe](https://datatracker.ietf.org/doc/html/rfc7231#section-4.2.1) - the operation semantic is defined to be read-only, meaning it must not have intended side effects, i.e. changes, to the server state.

* [idempotent](https://datatracker.ietf.org/doc/html/rfc7231#section-4.2.2) - the operation has the same intended effect on the server state, independently whether it is executed once or multiple times. Note: this does not require that the operation is returning the same response or status code.

* [cacheable](https://datatracker.ietf.org/doc/html/rfc7231#section-4.2.3) - to indicate that responses are allowed to be stored for future reuse. In general, requests to safe methods are cacheable, if it does not require a current or authoritative response from the server.

**Note:** The above definitions, of intended (side) effect allows the server to provide additional state changing behavior as logging, accounting, pre- fetching, etc. However, these actual effects and state changes, must not be intended by the operation so that it can be held accountable.

Method implementations must fulfill the following basic properties according to [RFC 7231](https://datatracker.ietf.org/doc/html/rfc7231):

| Method  | Safe  | Idempotent                                              | Cacheable |
|---------|-------|---------------------------------------------------------|-----------|
| `GET`     | ✔ Yes | ✔ Yes                                                   | ✔ Yes     |
| `HEAD`    | ✔ Yes | ✔ Yes                                                   | ✔ Yes     |
| `POST`    | ❌ No  | ⚠️ No, but **SHOULD** consider to design the API idempotent | ⚠️ May, but only if specific `POST` endpoint is safe. **Hint:** not supported by most caches. |
| `PUT`     | ❌ No  | ✔ Yes                                                   | ❌ No      |
| `PATCH`   | ❌ No  | ⚠️ No, but **SHOULD** consider to design the API idempotent | ❌ No      |
| `DELETE`  | ❌ No  | ✔ Yes                                                   | ❌ No      |
| `OPTIONS` | ✔ Yes | ✔ Yes                                                   | ❌ No      |
| `TRACE`   | ✔ Yes | ✔ Yes                                                   | ❌ No      |

**Note:** **MUST** Do not cache by default / Document cacheable endpoints.

### SHOULD consider to design the API idempotent [229]

In many cases it is helpful or even necessary to design POST and PATCH idempotent for clients to expose conflicts and prevent resource duplicate (a.k.a. zombie resources) or lost updates, e.g. if same resources may be created or changed in parallel or multiple times. To design an idempotent API endpoint owners should consider to apply one of the following three patterns.

* A resource specific **conditional key** provided via `If-Match` header in the request. The key is in general a meta information of the resource, e.g. a hash or version number, often stored with it. It allows to detect concurrent creations and updates to ensure idempotent behavior (see **MAY** support `ETag` together with `If-Match`/`If-None-Match` header).

* A resource specific **secondary key** provided as resource property in the request body. The secondary key is stored permanently in the resource. It allows to ensure idempotent behavior by looking up the unique secondary key in case of multiple independent resource creations from different clients (see **MAY** use secondary key for idempotent `POST` design).

* A client specific **idempotency key** provided via `Idempotency-Key` header in the request. The key is not part of the resource but stored temporarily pointing to the original response to ensure idempotent behavior when retrying a request (see **MAY** consider to support `idempotency-key` header).

**Note:** While **conditional key** and **secondary key** are focused on handling concurrent requests, the **idempotency key** is focused on providing the exact same responses, which is even a stronger requirement than the idempotency defined above. It can be combined with the two other patterns.

To decide, which pattern is suitable for your use case, please consult the following table showing the major properties of each pattern:

|                                   | Conditional Key | Secondary Key | Idempotency Key |
|-----------------------------------|-----------------|---------------|-----------------|
| **Applicable with**               | PATCH           | POST          | POST/PATCH      |
| **HTTP Standard**                 | ✔ Yes           | ❌ No          | ❌ No            |
| **Prevents duplicate (zombie) resources** | ✔ Yes | ✔ Yes         | ❌ No            |
| **Prevents concurrent lost updates**      | ✔ Yes | ❌ No          | ❌ No            |
| **Supports safe retries**                 | ✔ Yes | ✔ Yes         | ✔ Yes           |
| **Supports exact same response**          | ❌ No  | ❌ No          | ✔ Yes           |
| **Can be inspected (by intermediaries)**  | ✔ Yes | ❌ No          | ✔ Yes           |
| **Usable without previous GET**           | ❌ No  | ✔ Yes         | ✔ Yes           |

**Note:** The patterns applicable to `PATCH` can be applied in the same way to `PUT` and `DELETE` providing the same properties.

If you mainly aim to support safe retries, we suggest to apply conditional key and secondary key pattern before the Idempotency Key pattern.

Note, like for `PUT`, successful `POST` or `PATCH` returns **200** or **204** (if the resource was updated - with or without returning the resource), or **201** (if resource was created). Hence, clients can differentiate successful robust repetition from resource created server activity of idempotent `POST`.

### MAY use secondary key for idempotent POST design [231]

The most important pattern to design `POST` idempotent for creation is to introduce a resource specific **secondary key** provided in the request body, to eliminate the problem of duplicate (a.k.a zombie) resources.

The secondary key is stored permanently in the resource as alternate key or combined key (if consisting of multiple properties) guarded by a uniqueness constraint enforced server-side, that is visible when reading the resource. The best and often naturally existing candidate is a unique foreign key, that points to another resource having one-on-one relationship with the newly created resource, e.g. a parent process identifier.

A good example here for a secondary key is the shopping cart ID in an order resource.

### MUST define collection format of header and query parameters [154]

Header and query parameters allow to provide a collection of values by providing a comma-separated list of values. Providing a collection of values by repeating the parameter multiple times with different values must be avoided.

| Parameter Type | Comma-separated Values     | Multiple Parameters          |
|----------------|----------------------------|------------------------------|
| Header         | Header: value1,value2      | Header: value1, Header: value2 |
| Query          | ?param=value1,value2       | ?param=value1&param=value2   |


### SHOULD design simple query languages using query parameters [236]
We prefer the use of query parameters to describe resource-specific query languages for the majority of APIs because it’s native to HTTP, easy to extend and has excellent implementation support in HTTP clients and web frameworks.

Query parameters should have the following aspects specified:

* Reference to corresponding property, if any

* Value range, e.g. inclusive vs. exclusive

* Comparison semantics (equals, less than, greater than, etc)

* Implications when combined with other queries, e.g. and vs. or

How query parameters are named and used is up to individual API designers. The following examples should serve as ideas:

* `name=John`, to query for elements based on property equality

*`age=5`, to query for elements based on logical properties

* `max_length=5`, based on upper and lower bounds (min and max)

* `shorter_than=5`, using terminology specific e.g. to length

* `created_before=2019-07-17` or `not_modified_since=2019-07-17`

   * Using terminology specific e.g. to time: before, after, since and until

We don’t advocate for or against specific names because in the end APIs should be free to choose the terminology that fits their domain the best.

### SHOULD design complex query languages using JSON [237]

Minimalistic query languages based on query parameters are suitable for simple use cases with a small set of available filters that are combined in one way and one way only (e.g. and semantics). Simple query languages are generally preferred over complex ones.

Some APIs will have a need for sophisticated and more complex query languages. Dominant examples are APIs around search (incl. faceting) and product catalogs.

Aspects that set those APIs apart from the rest include but are not limited to:

* Unusual high number of available filters

* Dynamic filters, due to a dynamic and extensible resource model

* Free choice of operators, e.g. and, or and not

APIs that qualify for a specific, complex query language are encouraged to use nested JSON data structures and define them using OpenAPI directly. They provide the following benefits:

Data structures are easy to use for clients

No special library support necessary

No need for string concatenation or manual escaping

* Data structures are easy to use for servers

   * No special tokenizers needed

   * Semantics are attached to data structures rather than text tokens

* Consistent with other HTTP methods

* API is fully defined in OpenAPI

   * No external documents or grammars required

   * Existing means are familiar to everyone

JSON-specific rules and most certainly needs to make use of the `GET`-with-body pattern.

### Example
The following JSON document should give you an idea of what a structured query might look like.

```plaintext
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
```

Feel free to also get some inspiration from:

* [Elastic Search: Query DSL](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html)

* [GraphQL: Queries](https://graphql.org/learn/queries/)

## 8. REST Basics - HTTP status codes
### MUST use official HTTP status codes [243]
You must only use official HTTP status codes consistently with their intended semantics. Official HTTP status codes are defined via RFC standards and registered in the [IANA Status Code Registry](https://www.iana.org/assignments/http-status-codes/http-status-codes.xhtml). Main RFC standards are [RFC7231 - HTTP/1.1: Semantics](https://datatracker.ietf.org/doc/html/rfc7231#section-6) (or [RFC7235 - HTTP/1.1: Authentication](https://datatracker.ietf.org/doc/html/rfc7235#page-6)) and [RFC 6585 - HTTP: Additional Status Codes](https://datatracker.ietf.org/doc/html/rfc6585) (and there are upcoming new ones, e.g. [draft legally-restricted-status](https://datatracker.ietf.org/doc/html/draft-tbray-http-legally-restricted-status-05)). An overview on the official error codes provides [Wikipedia: HTTP status codes](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes) (which also lists some unofficial status codes, e.g. defined by popular web servers like Nginx, that we do not suggest to use).

### MUST specify success and error responses [151]
APIs should define the functional, business view and abstract from implementation aspects. Success and error responses are a vital part to define how an API is used correctly.

Therefore, you must define **success and service specific** error responses in your API specification. Both are part of the interface definition and provide important information for service clients to handle standard as well as exceptional situations. Error code response descriptions should provide information about the specific conditions that lead to the error, especially if these conditions can be changed by how the endpoint is used by the clients.

### SHOULD only use most common HTTP status codes [150]

The most commonly used codes are best understood and are listed below as a subset of the official HTTP status codes and consistent with their semantics in the RFCs. We avoid less commonly used codes that can easily create misconceptions due to less familiar semantics and API specific interpretations.

**Important:** As long as your HTTP status code usage is well covered by the semantics defined here, you should not describe it to avoid an overload of common sense information and the risk of inconsistent definitions. Only if the HTTP status code is not included in the list below or if its usage requires additional information beyond the well defined semantic, the API specification must provide a clear description of the HTTP status code in the response.

### Success codes
| Code | Meaning | Methods |
|------|---------|---------|
| **200**  | OK - this is the most general success response and used, if the more specific codes below are not applicable. | `<all>` |
| **201**  | Created - Returned on successful resource creation. **201** is returned with or without response payload (unlike **200** / **204**). We recommend to additionally return the created resource URL via the Location response header (see [standard-headers]). | `POST`, `PUT` |
| **202**  | Accepted - The request was successful and will be processed asynchronously. | `POST`, `PUT`, `PATCH`, `DELETE` |
| **204**  | No content - Returned instead of **200**, if no response payload is returned. | `PUT`, `PATCH`, `DELETE` |
| **207**  | Multi-Status - The response body contains status information for multiple different parts of a batch/bulk request (see MUST use code 207 for batch or bulk requests). | `POST`, (`DELETE`) |


### Redirection codes

| Code | Meaning | Methods |
|------|---------|---------|
| **301**  | Moved Permanently - This and all future requests should be redirected to the given URI. | <all> |
| **303**  | See Other - The response to the request can be found under another URI using a GET method. | `POST`, `PUT`, `PATCH`, `DELETE` |
| **304**  | Not Modified - indicates that a conditional GET or HEAD request would have resulted in a 200 response if it were not for the fact that the condition evaluated to false, i.e. the resource has not been modified since the date or version passed via request headers If-Modified-Since or If-None-Match. | `GET`, `HEAD` |

### Client side error codes

| Code | Meaning | Methods |
|------|---------|---------|
| 400  | Bad request - unspecified client error indicating that the server cannot process the request due to what is perceived to be a client error (e.g. malformed request syntax, invalid request). Should also be delivered if the input payload fails due to business logic / semantic validation (instead of using status code 422). | `<all>` |
| 401  | Unauthorized - actually "Unauthenticated": credentials are not valid for the target resource. User must log in. | `<all>` |
| 403  | Forbidden - the user is not authorized to use this resource. | `<all>` |
| 404  | Not found - the resource is not found. | `<all>` |
| 405  | Method Not Allowed - the method is not supported, see OPTIONS. | `<all>` |
| 406  | Not Acceptable - indicates that the server cannot produce a response matching the list of acceptable values defined in the request’s proactive content negotiation headers, and that the server is unwilling to supply a default representation. | `<all>` |
| 408  | Request timeout - the server times out waiting for the resource. | `<all>` |
| 409  | Conflict - request cannot be completed due to conflict with the current state of the target resource. For example, you may get a 409 response when updating a resource that is older than the existing one on the server, resulting in a version control conflict. Hint, you should not return 409, but 200 or 204 in case of successful robust creation of resources (via PUT or POST), if the resource already exists. | `POST, PUT, PATCH, DELETE` |
| 410  | Gone - resource does not exist any longer, e.g. when accessing a resource that has intentionally been deleted. | `<all>` |
| 412  | Precondition Failed - returned for conditional requests, e.g. If-Match if the condition failed. Used for optimistic locking. | `PUT, PATCH, DELETE` |
| 415  | Unsupported Media Type - e.g. clients sends request body without content type. | `POST, PUT, PATCH, DELETE` |
| 423  | Locked - Pessimistic locking, e.g. processing states. | `PUT, PATCH, DELETE` |
| 428  | Precondition Required - server requires the request to be conditional. Typically, this means that a required precondition header, such as If-Match, is missing. | `<all>` |
| 429  | Too many requests - the client does not consider rate limiting and sent too many requests (see MUST use code 429 with headers for rate limits). | `<all>` |

### Server side error codes:
| Code | Meaning | Methods |
|------|---------|---------|
| 500  | Internal Server Error - a generic error indication for an unexpected server execution problem. | `<all>` |
| 501  | Not Implemented - server cannot fulfill the request (usually implies future availability, e.g. new feature). | `<all>` |
| 503  | Service Unavailable - service is (temporarily) not available (e.g. if a required component or downstream service is not available). If possible, the service should indicate how long the client should wait by setting the Retry-After header. | `<all>` |

### MUST use most specific HTTP status codes [220]
You must use the most specific HTTP status code when returning information about your request processing status or error situations.

### MUST use code 207 for batch or bulk requests [152]
Some APIs are required to provide either batch or bulk requests using `POST` for performance reasons, i.e. for communication and processing efficiency. In this case services may be in need to signal multiple response codes for each part of a batch or bulk request. As HTTP does not provide proper guidance for handling batch/bulk requests and responses, we herewith define the following approach:

* A batch or bulk request **always** responds with HTTP status code **207** unless a non-item-specific failure occurs.

* A batch or bulk request **may** return **4xx/5xx** status codes, if the failure is non-item-specific and cannot be restricted to individual items of the batch or bulk request, e.g. in case of overload situations or general service failures.

* A batch or bulk response with status code **207 always** returns as payload a multi-status response containing item specific status and/or monitoring information for each part of the batch or bulk request.

**Note:** These rules apply even in the case that processing of all individual parts fail or each part is executed asynchronously!

The rules are intended to allow clients to act on batch and bulk responses in a consistent way by inspecting the individual results. We explicitly reject the option to apply **200** for a completely successful batch as proposed in Nakadi’s `POST /event-types/{name}/events` as short cut without inspecting the result, as we want to avoid risks and expect clients to handle partial batch failures anyway.

The bulk or batch response may look as follows:

```plaintext
BatchOrBulkResponse:
  description: batch response object.
  type: object
  properties:
    items:
      type: array
      items:
        type: object
        properties:
          id:
            description: Identifier of batch or bulk request item.
            type: string
          status:
            description: >
              Response status value. A number or extensible enum describing
              the execution status of the batch or bulk request items.
            type: string
            x-extensible-enum: [...]
          description:
            description: >
              Human readable status description and containing additional
              context information about failures etc.
            type: string
        required: [id, status]
```
**Note:** while a batch defines a collection of requests triggering independent processes, a bulk defines a collection of independent resources created or updated together in one request. With respect to response processing this distinction normally does not matter.

### MUST use code 429 with headers for rate limits [153]

APIs that wish to manage the request rate of clients must use the **429** (Too Many Requests) response code, if the client exceeded the request rate (see [RFC 6585](https://datatracker.ietf.org/doc/html/rfc6585)). Such responses must also contain header information providing further details to the client.

Return a `Retry-After` header indicating how long the client ought to wait before making a follow-up request. The Retry-After header can contain a HTTP date value to retry after or the number of seconds to delay. Either is acceptable but APIs should prefer to use a delay in seconds.

### SHOULD provide error information [176]

In case of errors, information about the error should be provided by returning adequate information in the body.

```plaintext
{
  "title": "Your request parameters didn't validate.",
  "invalid-params": [
    {
      "name": "age",
      "reason": "must be a positive integer"
    },
    {
      "name": "color",
      "reason": "must be 'green', 'red' or 'blue'"
    }
  ]
}
```
**Note:** such error messages must not provide system critical or confidential information.

### MUST not expose stack traces [177]

Stack traces contain implementation details that are not part of an API, and on which clients should never rely. Moreover, stack traces can leak sensitive information that partners and third parties are not allowed to receive and may disclose insights about vulnerabilities to attackers.

## 9. REST Basics - HTTP headers
We describe a handful of standard HTTP headers, which we found raising the most questions in our daily usage, or which are useful in particular circumstances but not widely known.

Though we generally discourage usage of proprietary headers, they are useful to pass generic, service independent, overarching information relevant for our specific application architecture. We consistently define these proprietary headers in this section below. Whether services support these concerns or not is optional. Therefore, the OpenAPI API specification is the right place to make this explicitly visible — use the parameter definitions of the resource HTTP methods.

### SHOULD use standard headers [133]

Use this list and explicitly mention its support in your OpenAPI definition.

### SHOULD use kebab-case with lowercase separate words for HTTP header names [132]

HTTP standard defines headers as case-insensitive ([RFC 7230, p.22](https://datatracker.ietf.org/doc/html/rfc7230#page-22)). Since HTTP/2 uses lowercase header names only, we suggest using lowercase header names throughout

### MAY support `ETag` together with `If-Match`/`If-None-Match` header [182]

When creating or updating resources it may be necessary to expose conflicts and to prevent the 'lost update' or 'initially created' problem. [Following RFC 7232 "HTTP: Conditional Requests"](https://datatracker.ietf.org/doc/html/rfc7232) this can be best accomplished by supporting the `ETag` header together with the `If-Match` or `If-None-Match` conditional header. The contents of an `ETag: <entity-tag>` header is either (a) a hash of the response body, (b) a hash of the last modified field of the entity, or (c) a version number or identifier of the entity version.

To expose conflicts between concurrent update operations via `PUT`, `POST`, or `PATCH`, the `If-Match: <entity-tag>` header can be used to force the server to check whether the version of the updated entity is conforming to the requested `<entity-tag>`. If no matching entity is found, the operation is supposed a to respond with status code **412** - precondition failed.

Beside other use cases, `If-None-Match`: * can be used in a similar way to expose conflicts in resource creation. If any matching entity is found, the operation is supposed a to respond with status code **412** - precondition failed.

The `ETag`, `If-Match`, and `If-None-Match` headers should be defined as follows in the API definition:

```plaintext
components:
  headers:
  - ETag:
      description: |
        The RFC 7232 ETag header field in a response provides the entity-tag of
        a selected resource. The entity-tag is an opaque identifier for versions
        and representations of the same resource over time, regardless whether
        multiple versions are valid at the same time. An entity-tag consists of
        an opaque quoted string, possibly prefixed by a weakness indicator (see
        [RFC 7232 Section 2.3](https://tools.ietf.org/html/rfc7232#section-2.3).

      type: string
      required: false
      example: W/"xy", "5", "5db68c06-1a68-11e9-8341-68f728c1ba70"

  - If-Match:
      description: |
        The RFC7232 If-Match header field in a request requires the server to
        only operate on the resource that matches at least one of the provided
        entity-tags. This allows clients express a precondition that prevent
        the method from being applied if there have been any changes to the
        resource (see [RFC 7232 Section
        3.1](https://tools.ietf.org/html/rfc7232#section-3.1).

      type: string
      required: false
      example: "5", "7da7a728-f910-11e6-942a-68f728c1ba70"

  - If-None-Match:
      description: |
        The RFC7232 If-None-Match header field in a request requires the server
        to only operate on the resource if it does not match any of the provided
        entity-tags. If the provided entity-tag is `*`, it is required that the
        resource does not exist at all (see [RFC 7232 Section
        3.2](https://tools.ietf.org/html/rfc7232#section-3.2).

      type: string
      required: false
      example: "7da7a728-f910-11e6-942a-68f728c1ba70", *
```
### MAY consider to support `idempotency-key` header [230]

When creating or updating resources it can be helpful or necessary to ensure a strong idempotent behavior comprising same responses, to prevent duplicate execution in case of retries after timeout and network outages. Generally, this can be achieved by sending a client specific unique request key – that is not part of the resource – via `Idempotency-Key` header.

The unique request key is stored temporarily, e.g. for 24 hours, together with the response and the request hash (optionally) of the first request in a key cache, regardless of whether it succeeded or failed. The service can now look up the unique request key in the key cache and serve the response from the key cache, instead of re-executing the request, to ensure idempotent behavior. Optionally, it can check the request hash for consistency before serving the response. If the key is not in the key store, the request is executed as usual and the response is stored in the key cache.

This allows clients to safely retry requests after timeouts, network outages, etc. while receive the same response multiple times. **Note:** The request retry in this context requires to send the exact same request, i.e. updates of the request that would change the result are off-limits. The request hash in the key cache can protection against this misbehavior. The service is recommended to reject such a request using status code **400**.

**Important:** To grant a reliable idempotent execution semantic, the resource and the key cache have to be updated with hard transaction semantics – considering all potential pitfalls of failures, timeouts, and concurrent requests in a distributed systems.

The `Idempotency-Key` header must be defined as follows, but you are free to choose your expiration time:

```plaintext
components:
  headers:
  - idempotency-key:
      description: |
        The idempotency key is a free identifier created by the client to
        identify a request. It is used by the service to identify subsequent
        retries of the same request and ensure idempotent behavior by sending
        the same response without executing the request a second time.

        Clients should be careful as any subsequent requests with the same key
        may return the same response without further check. Therefore, it is
        recommended to use an UUID version 4 (random) or any other random
        string with enough entropy to avoid collisions.

        Idempotency keys expire after 24 hours. Clients are responsible to stay
        within this limit, if they require idempotent behavior.

      type: string
      format: uuid
      required: false
      example: "7da7a728-f910-11e6-942a-68f728c1ba70"
```
**Hint:** The key cache is not intended as request log, and therefore should have a limited lifetime, else it could easily exceed the data resource in size.

## 10. REST Design - Hypermedia
### MUST use REST maturity level 2 [162]

We strive for a good implementation of [REST Maturity Level 2](https://martinfowler.com/articles/richardsonMaturityModel.html#level2) as it enables us to build resource-oriented APIs that make full use of HTTP verbs and status codes. You can see this expressed by many rules throughout these guidelines, e.g.:

* MUST avoid actions — think about resources

* MUST keep URLs verb-free

* MUST use HTTP methods correctly

* SHOULD only use most common HTTP status codes

Although this is not HATEOAS, it should not prevent you from designing proper link relationships in your APIs as stated in rules below.

### MAY use REST maturity level 3 - HATEOAS [163]

We do not generally recommend to implement [REST Maturity Level 3](https://martinfowler.com/articles/richardsonMaturityModel.html#level3). HATEOAS comes with additional API complexity without real value in a SOA context where client and server interact via REST APIs.

However, we do not forbid HATEOAS; you could use it, if you checked its limitations and still see clear value for your usage scenario that justifies its additional complexity. Some articles you might want to study before deciding for HATEOAS can be found here:

* [HATEOAS Driven REST APIs](https://restfulapi.net/hateoas/)

* [RESTistential Crisis over Hypermedia APIs](https://www.infoq.com/news/2014/03/rest-at-odds-with-web-apis/)

* [Why I Hate HATEOAS](https://jeffknupp.com/blog/2014/06/03/why-i-hate-hateoas/)

### MUST use full, absolute URI for resource identification [217]
Links to other resource must always use full, absolute URI.

**Motivation:** Exposing any form of relative URI (no matter if the relative URI uses an absolute or relative path) introduces avoidable client side complexity. It also requires clarity on the base URI, which might not be given when using features like embedding subresources.

## 11. REST Design - Performance
#### MAY support partial responses via filtering [157]

Depending on your use case and payload size, you can significantly reduce network bandwidth need by supporting filtering of returned entity `fields`. Here, the client can explicitly determine the subset of `fields` he wants to receive via the fields query parameter. (It is analogue to [GraphQL](https://graphql.org/learn/queries/#fields) fields and simple queries, and also applied, for instance, for [Google Cloud API’s partial responses](https://cloud.google.com/storage/docs/json_api#partial-response).)

### Example
### Unfiltered
```plaintext
GET http://api.example.org/users/123 HTTP/1.1

HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": "cddd5e44-dae0-11e5-8c01-63ed66ab2da5",
  "name": "John Doe",
  "address": "1600 Pennsylvania Avenue Northwest, Washington, DC, United States",
  "birthday": "1984-09-13",
  "friends": [ {
    "id": "1fb43648-dae1-11e5-aa01-1fbc3abb1cd0",
    "name": "Jane Doe",
    "address": "1600 Pennsylvania Avenue Northwest, Washington, DC, United States",
    "birthday": "1988-04-07"
  } ]
}
```
### Filtered
```plaintext
GET http://api.example.org/users/123?fields=(name,friends(name)) HTTP/1.1

HTTP/1.1 200 OK
Content-Type: application/json

{
  "name": "John Doe",
  "friends": [ {
    "name": "Jane Doe"
  } ]
}
```
### MUST Do not cache by default / Document cacheable endpoints [227]

Caching has to take many aspects into account, e.g. general cacheability of response information, resource update and invalidation rules, existence of multiple consumer instances. As a consequence, caching is in best case complex, e.g. with respect to consistency, in worst case inefficient.

As a consequence, client side as well as transparent web caching should be avoided, unless the service supports and requires it to protect itself, e.g. in case of a heavily used and therefore rate limited master data service, i.e. data items that rarely or not at all change after creation.

As default, API providers and consumers should always set the `Cache-Control` header set to `Cache-Control: no-store` and assume the same setting, if no `Cache-Control` header is provided.

**Note:** There is no need to document this default setting. However, please make sure that your framework is attaching this header value by default, or ensure this manually, e.g. using the best practice of Spring Security as shown below. Any setup deviating from this default must be sufficiently documented.

```plaintext
Cache-Control: no-cache, no-store, must-revalidate, max-age=0
```
If your service really requires to support caching, please observe the following rules:

* Document all cacheable GET, HEAD, and POST endpoints by declaring the support of Cache-Control, Vary, and ETag headers in response. Note: you must not define the Expires header to prevent redundant and ambiguous definition of cache lifetime. A sensible default documentation of these headers is given below.

* Take care to specify the ability to support caching by defining the right caching boundaries, i.e. time-to-live and cache constraints, by providing sensible values for Cache-Control and Vary in your service. We will explain best practices below.

* Provide efficient methods to warm up and update caches, e.g. as follows:

   * In general, you should support ETag Together With If-Match/ If-None-Match Header on all cacheable endpoints.

   * For larger data items support HEAD requests or more efficient GET requests with If-None-Match header to check for updates.

   * For small data sets provide full collection GET requests supporting ETag, as well as HEAD requests or GET requests with If-None-Match to check for updates.

   * For medium sized data sets provide full collection GET requests supporting ETag together with REST Design - Pagination and <entity-tag> filtering GET requests for limiting the response to changes since the provided <entity-tag>. Note: this is not supported by generic client and proxy caches on HTTP layer.

**Hint:** For proper cache support, you must return **304** without content on a failed `HEAD` or `GET` request with `If-None-Match: <entity-tag>` instead of **412**.

```plaintext
components:
  headers:
  - Cache-Control:
      description: |
        The RFC 7234 Cache-Control header field is providing directives to
        control how proxies and clients are allowed to cache responses results
        for performance. Clients and proxies are free to not support caching of
        results, however if they do, they must obey all directives mentioned in
        [RFC-7234 Section 5.2.2](https://tools.ietf.org/html/rfc7234) to the
        word.

        In case of caching, the directive provides the scope of the cache
        entry, i.e. only for the original user (private) or shared between all
        users (public), the lifetime of the cache entry in seconds (max-age),
        and the strategy how to handle a stale cache entry (must-revalidate).
        Please note, that the lifetime and validation directives for shared
        caches are different (s-maxage, proxy-revalidate).

      type: string
      required: false
      example: "private, must-revalidate, max-age=300"

  - Vary:
      description: |
        The RFC 7231 Vary header field in a response defines which parts of
        a request message, aside the target URL and HTTP method, might have
        influenced the response. A client or proxy cache must respect this
        information, to ensure that it delivers the correct cache entry (see
        [RFC-7231 Section
        7.1.4](https://tools.ietf.org/html/rfc7231#section-7.1.4)).

      type: string
      required: false
      example: "accept-encoding, accept-language"
```
**Hint:** For `ETag` source see `MAY` support `ETag` together with `If-Match`/`If-None-Match` header.

The default setting for `Cache-Control` should contain the `private` directive for endpoints with standard OAuth authorization, as well as the `must-revalidate` directive to ensure, that the client does not use stale cache entries. Last, the `max-age` directive should be set to a value between a few seconds (`max-age=60`) and a few hours (`max-age=86400`) depending on the change rate of your master data and your requirements to keep clients consistent.

```plaintext
Cache-Control: private, must-revalidate, max-age=300
```
The default setting for `Vary` is harder to determine correctly. It highly depends on the API endpoint, e.g. whether it supports compression, accepts different media types, or requires other request specific headers. To support correct caching you have to carefully choose the value. However, a good first default may be:

```plaintext
Vary: accept, accept-encoding
```
Anyhow, this is only relevant, if you encourage clients to install generic HTTP layer client and proxy caches.

**Note:** generic client and proxy caching on HTTP level is hard to configure. Therefore, we strongly recommend to attach the (possibly distributed) cache directly to the service (or gateway) layer of your application. This relieves from interpreting the `Vary` header and greatly simplifies interpreting the `Cache-Control` and `ETag` headers. Moreover, is highly efficient with respect to caching performance and overhead, and allows to support more advanced cache update and warm up patterns.

Anyhow, please carefully read [RFC 7234](https://datatracker.ietf.org/doc/html/rfc7234) before adding any client or proxy cache.

## 12. REST Design - Pagination
### MUST support pagination for large result set [159]

Access to large lists of data items must support pagination to protect the service against overload as well as for best client side iteration and batch processing experience. This holds true for all lists that are (potentially) larger than just a few hundred entries.

There are two well known page iteration techniques:

* **Offset-based pagination:** numeric offset identifies the first page-entry

* **Cursor-based pagination** — aka key-based pagination: a unique key identifies the first page-entry (see also [Twitter API](https://developer.x.com/en/docs/twitter-api/v1/pagination) or [Facebook API](https://developers.facebook.com/docs/graph-api/results))

The technical conception of pagination should also consider user experience related issues. As mentioned in this [article](https://www.smashingmagazine.com/2016/03/pagination-infinite-scrolling-load-more-buttons/), jumping to a specific page is far less used than navigation via next/prev page links. This favours cursor-based over offset-based pagination.

**Note:** To provide a consistent look and feel of pagination patterns, you must stick to the common query parameter names defined in **SHOULD** stick to conventional query parameters.

### SHOULD prefer cursor-based pagination [160]

Offset based pagination looks intuitive and simple at a first glance, but cursor-based pagination is usually better and more efficient. Especially when it comes to high-data volumes and/or storage in NoSQL databases.

Before choosing cursor-based pagination, consider the following trade-offs:

* Usability/framework support:

   * Offset-based pagination is more widely known than cursor-based pagination, so it has more framework support and is easier to use for API clients

Use case - jump to a certain page:

   * If jumping to a particular page in a range (e.g., 51 of 100) is really a required use case, cursor-based navigation is not feasible.

* Data changes may lead to anomalies in result pages:

   * Offset-based pagination may create duplicates or lead to missing entries if rows are inserted or deleted between two subsequent paging requests.

   * If implemented incorrectly, cursor-based pagination may fail when the cursor entry has been deleted before fetching the pages.

* Performance considerations - efficient server-side processing using offset-based pagination is hardly feasible for:

   * Very big data sets, especially if they cannot reside in the main memory of the database.

   * Sharded or NoSQL databases.

* Cursor-based navigation may not work if you need the total count of results.

The `cursor` used for pagination is an opaque pointer to a page, that must never be **inspected** or **constructed** by clients. It usually encodes (encrypts) the page position, i.e. the identifier of the first or last page element, the pagination direction, and the applied query filters - or a hash over these - to safely recreate the collection.

## 13. REST Design - Compatibility
### MUST not break backward compatibility [106]

Change APIs, but keep all consumers running. Consumers usually have independent release lifecycles, focus on stability, and avoid changes that do not provide additional value. APIs are contracts between service providers and service consumers that cannot be broken via unilateral decisions.

There are two techniques to change APIs without breaking them:

* follow rules for compatible extensions

* introduce new API versions and still support older versions (SHOULD use URL versioning).

We strongly encourage using compatible API extensions and discourage versioning (see **SHOULD** avoid versioning). The following guidelines for service providers (**SHOULD** prefer compatible extensions) enable us (having Postel’s Law in mind) to make compatible changes without versioning.

### SHOULD prefer compatible extensions [107]

API designers should apply the following rules to evolve RESTful APIs for services in a backward-compatible way:

* Add only optional, never mandatory fields.

* Never change the semantic of fields (e.g. changing the semantic from customer-number to customer-id, as both are different unique customer keys)

* Input fields may have (complex) constraints being validated via server-side business logic. Never change the validation logic to be more restrictive and make sure that all constraints are clearly defined in description.

* Enum ranges can be reduced when used as input parameters, only if the server is ready to accept and handle old range values too. Enum range can be reduced when used as output parameters.

* Enum ranges cannot be extended when used for output parameters — clients may not be prepared to handle it. However, enum ranges can be extended when used for input parameters.

* Support redirection in case an URL has to change 301 (Moved Permanently).

### MUST treat OpenAPI specification as open for extension by default [111]

The OpenAPI specification is not very specific on default extensibility of objects, and redefines JSON-Schema keywords related to extensibility, like `additionalProperties`. Following our compatibility guidelines, OpenAPI object definitions are considered open for extension by default as per [Section 5.18 "additionalProperties"](https://json-schema.org/latest/json-schema-validation.html#rfc.section.5.18) of JSON-Schema.

When it comes to OpenAPI, this means an `additionalProperties` declaration is not required to make an object definition extensible:

* API clients consuming data must not assume that objects are closed for extension in the absence of an `additionalProperties` declaration and must ignore fields sent by the server they cannot process. This allows API servers to evolve their data formats.

* For API servers receiving unexpected data, the situation is slightly different. Instead of ignoring fields, servers may reject requests whose entities contain undefined fields in order to signal to clients that those fields would not be stored on behalf of the client. API designers must document clearly how unexpected fields are handled for `PUT`, `POST`, and `PATCH` requests.

API formats must not declare `additionalProperties` to be false, as this prevents objects being extended in the future.

Note that this guideline concentrates on default extensibility and does not exclude the use of `additionalProperties` with a schema as a value, which might be appropriate in some circumstances, e.g. see **SHOULD** define maps using `additionalProperties`.

### SHOULD avoid versioning [113]

When changing your RESTful APIs, do so in a compatible way and avoid generating additional API versions. Multiple versions can significantly complicate understanding, testing, maintaining, evolving, operating and releasing our systems ([supplementary reading](https://martinfowler.com/articles/enterpriseREST.html)).

If changing an API can’t be done in a compatible way, then proceed in one of these three ways:

* create a new resource (variant) in addition to the old resource variant

* create a new service endpoint — i.e. a new application with a new API (with a new domain name)

* create a new API version supported in parallel with the old API by the same microservice

As we discourage versioning by all means because of the manifold disadvantages, we strongly recommend to only use the first two approaches.

 ### SHOULD use URL versioning [115]

When API versioning is unavoidable you should design your multi-version RESTful APIs using URL versioning. With URL versioning a version is included in the path:

* The syntax of version is v<N>, where N is a simple monotonically increasing version number e.g. v2, v3, v4

* Add versions when needed only, thus you never use v1 but start with v2

* Prefer resource versioning /customers/v2 over api versioning /v2/customers

* Clients must not use different versions, therfore:

   * A resource version must provide all operations of the resource

   * An API version must provide all resources of the API

We recommend URL versioning over media type versioning, as it is well known and widely accepted.

## 14. REST Design - Deprecation
Sometimes it is necessary to phase out an API endpoint, an API version, or an API feature, e.g. if a field or parameter is no longer supported or a whole business functionality behind an endpoint is supposed to be shut down. As long as the API endpoints and features are still used by consumers these shut downs are breaking changes and not allowed. To progress the following deprecation rules have to be applied to make sure that the necessary consumer changes and actions are well communicated and aligned using deprecation and sunset dates.

### MUST reflect deprecation in API specifications [187]

The API deprecation must be part of the API specification.

If an API endpoint (operation object), an input argument (parameter object), an in/out data object (schema object), or on a more fine grained level, a schema attribute or property should be deprecated, the producers must set `deprecated: true` for the affected element and add further explanation to the `description` section of the API specification. If a future shut down is planned, the producer must provide a sunset date and document in details what consumers should use instead and how to migrate.

### SHOULD add `Deprecation` and `Sunset` header to responses [189]

During the deprecation phase, the producer should add a `Deprecation: <date-time>` ([see draft: RFC Deprecation HTTP Header](https://datatracker.ietf.org/doc/html/draft-ietf-httpapi-deprecation-header)) and - if also planned - a `Sunset: <date-time>` (see [RFC 8594](https://datatracker.ietf.org/doc/html/rfc8594#section-3)) header on each response affected by a deprecated element (see MUST reflect deprecation in API specifications).

The `Deprecation` header can either be set to `true` - if a feature is retired -, or carry a deprecation time stamp, at which a replacement will become/became available and consumers must not on-board any longer. The optional `Sunset` time stamp carries the information when consumers latest have to stop using a feature. The sunset date should always offer an eligible time interval for switching to a replacement feature.

```plaintext
Deprecation: Tue, 31 Dec 2024 23:59:59 GMT
Sunset: Wed, 31 Dec 2025 23:59:59 GMT
```
If multiple elements are deprecated the `Deprecation` and `Sunset` headers are expected to be set to the earliest time stamp to reflect the shortest interval consumers are expected to get active.

## Appendix A: Case Styles
Following case styles are used in this style guide.

| Camel Case                 | numberOfDonuts        |
|----------------------------|-----------------------|
| **Pascal Case**                | NumberOfDonuts        |
| **Kebab Case**                 | number-of-donuts      |
| **Kebab Case, Upper Case**     | Number-Of-Donuts      |
| **Snake Case**                 | number_of_donuts      |
| **Upper Snake Case**           | NUMBER_OF_DONUTS      |


## Appendix B: References
This section collects links to documents to which we refer, and base our guidelines on.

### OpenAPI specification
* [OpenAPI specification](https://github.com/OAI/OpenAPI-Specification/)

* [OpenAPI specification mind map](https://openapi-map.apihandyman.io/)

## Publications, specifications and standards
* [RFC 3339](https://datatracker.ietf.org/doc/html/rfc3339): Date and Time on the Internet: Timestamps

* [RFC 4122](https://datatracker.ietf.org/doc/html/rfc4122): A Universally Unique IDentifier (UUID) URN Namespace

* [RFC 4627](https://datatracker.ietf.org/doc/html/rfc4627): The application/json Media Type for JavaScript Object Notation (JSON)

* [RFC 8288](https://datatracker.ietf.org/doc/html/rfc8288): Web Linking

* [RFC 6585](https://tools.ietf.org/html/rfc6585): Additional HTTP Status Codes

* [RFC 6902](https://tools.ietf.org/html/rfc6902): JavaScript Object Notation (JSON) Patch

* [RFC 7159](https://tools.ietf.org/html/rfc7159): The JavaScript Object Notation (JSON) Data Interchange Format

* [RFC 7230](https://tools.ietf.org/html/rfc7230): Hypertext Transfer Protocol (HTTP/1.1): Message Syntax and Routing

* [RFC 7231](https://tools.ietf.org/html/rfc723)): Hypertext Transfer Protocol (HTTP/1.1): Semantics and Content

* [RFC 7232](https://tools.ietf.org/html/rfc7232): Hypertext Transfer Protocol (HTTP/1.1): Conditional Requests

* [RFC 7233](https://tools.ietf.org/html/rfc7233): Hypertext Transfer Protocol (HTTP/1.1): Range Requests

* [RFC 7234](https://tools.ietf.org/html/rfc7234): Hypertext Transfer Protocol (HTTP/1.1): Caching

* [RFC 7240](https://tools.ietf.org/html/rfc7240): Prefer Header for HTTP

* [RFC 7396](https://tools.ietf.org/html/rfc7396): JSON Merge Patch

* [RFC 7807](https://tools.ietf.org/html/rfc7807): Problem Details for HTTP APIs

* [RFC 4648](https://tools.ietf.org/html/rfc4648): The Base16, Base32, and Base64 Data Encodings

[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601): Date and time format

[ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2): Two letter country codes

[ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes): Two letter language codes

[ISO 4217](https://en.wikipedia.org/wiki/ISO_4217): Currency codes

[BCP 47](https://tools.ietf.org/html/bcp47): Tags for Identifying Languages

### Dissertations
* [Roy Thomas Fielding - Architectural Styles and the Design of Network-Based Software Architectures](http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm): This is the text which defines what REST is.

### Books
* [REST in Practice: Hypermedia and Systems Architecture](http://www.amazon.de/REST-Practice-Hypermedia-Systems-Architecture/dp/0596805829)

* [Build APIs You Won’t Hate](https://leanpub.com/build-apis-you-wont-hate)

* [InfoQ eBook - Web APIs: From Start to Finish](http://www.infoq.com/minibooks/emag-web-api)

### Blogs
* [Lessons-learned blog: Thoughts on RESTful API Design](https://restful-api-design.readthedocs.io/en/latest/)

## Appendix C: Changelog
This change log only contains major changes and lists major changes since the inital version of Swiss Governement. 


### Rule Changes
* `2023-09-01`: V0.9 Created
