# Collection+JSON - Document Format

## Description
[Collection+JSON][] is a JSON-based read/write hypermedia-type designed to support management and querying of simple collections.

#### Author:

Mike Amundsen (mamund@yahoo.com)

#### Dates:

* 2011-05-04 (Created)
* 2011-07-12 (Updated)

#### Status:

__Approved__

## Contents

1. [General Concepts](#1-general-concepts)
2. [Objects](#2-objects)
3. [Arrays](#3-arrays)
4. [Properties](#4-properties)
5. [Link Relations](#5-link-relations)
6. [Data Types](#6-data-types)
7. [Extensibility](#7-extensibility)
8. [Acknowledgements](#8-acknowledgements)
9. [References](#9-references)
10. [Update History](#10-update-history)

> __NOTE:__
> The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC2119](http://tools.ietf.org/html/rfc2119).

### 1. General Concepts

This section contains general concepts that apply to all [Collection+JSON][] documents.

The [Collection+JSON][] hypermedia type is designed to support full read/write capability for simple lists (contacts, tasks, blog entries, etc.). The standard application semantics supported by this media type include Create, Read, Update, and Delete (CRUD) along w/ support for predefined queries including query templates (similar to HTML "GET" forms). Write operations are defined using a [template][] object supplied by the server as part of the response representation.

Each [item][] in a [Collection+JSON][] [collection][] has an assigned URI (via the [href][] property) and an optional array of one or more [data][] elements along with an optional array of one or more [link][] elements. Both arrays support a [name][] property for each object in the [collection][] in order to decorate these elements with domain-specific semantic information (e.g. `"data" : [{"name" : "first-name", ...},...]`).

The [Collection+JSON][] hypermedia type has a limited set of predefined link relation values and supports additional values applied by implementors in order to better describe the application domain to which the media type is applied.

The following sections describe the process of reading and writing data using the [Collection+JSON][] hypermedia type as well as the way to parse and execute Query Templates. Additional examples can be found in the Examples section of this documentation.

#### 1.1 Reading and Writing Data

The [Collection+JSON][] media type supports a limited form of read/write semantics; the Create-Read-Update-Delete (or CRUD) pattern. In addition to CRUD operations, the [Collection+JSON][] media type supports Query Templates.

This section describes the details of how clients can recognize and implement reads and writes found within [Collection+JSON][] responses.

##### 1.1.1 Reading Collections

To get a list of the [items][] in a collection, the client sends an HTTP GET request to the URI of a collection. A [Collection+JSON][] Document is returned which contains one or more [item][] objects in an array. The response may describe all, or only a partial list, of the [items][] in a collection.

```
*** REQUEST ***
GET /my-collection/ HTTP/1.1
Host: www.example.org
Accept: application/vnd.collection+json

*** RESPONSE ***
200 OK HTTP/1.1
Content-Type: application/vnd.collection+json
Content-Length: xxx

{ "collection" : {...}, ... }
```

##### 1.1.2 Adding an Item

To create a new [item][] in the collection, the client first uses the [template][] object to compose a valid [item][] representation and then uses HTTP POST to send that representation to the server for processing.

If the [item][] resource was created successfully, the server responds with a status code of 201 and a Location header that contains the URI of the newly created [item][] resource.

```
*** REQUEST ***
POST /my-collection/ HTTP/1.1
Host: www.example.org
Content-Type: application/vnd.collection+json

{ "template" : { "data" : [ ...] } }
 
*** RESPONSE ***
201 Created HTTP/1.1
Location: http://www.example.org/my-collection/1
```

##### 1.1.3 Reading an Item

Clients can retrieve an existing [item][] resource by sending an HTTP GET request to the URI of an [item][] resource. If the request is valid, the server will respond with a representation of that [item][] resource.

```
*** REQUEST ***
GET /my-collection/1 HTTP/1.1
Host: www.example.org
Accept: application/vnd.collection+json

*** RESPONSE ***
200 OK HTTP/1.1
Content-Type: application/vnd.collection+json
Content-Length: xxx

{ "collection" : { "href" : "...", "items" [ { "href" : "...", "data" : [...].} } }
```

Note that the valid response is actually a complete [collection][] document that contains only one [item][] (and possibly related [queries][] and [template][] properties). 

##### 1.1.4 Updating an Item

To update an existing resource, the client uses the [template][] object as a guide to composing a replacement [item][] representation and then uses HTTP PUT to send that representation to the server.

If the update is successful, the server will respond with HTTP status code 200 and possibly a representation of the updated [item][] resource representation.

```
*** REQUEST ***
PUT /my-collection/1 HTTP/1.1
Host: www.example.org
Content-Type: application/vnd.collection+json

{ "template" : { "data" : [ ...] } }
 
*** RESPONSE ***
200 OK HTTP/1.1
```

##### 1.1.5. Deleting an Item

Clients can delete existing resources by sending an HTTP DELETE request to the URI of the [item][] resource.

If the delete request is successful, the server SHOULD respond with an HTTP status code of 204.

```
*** REQUEST ***
DELETE /my-collection/1 HTTP/1.1
Host: www.example.org
 
*** RESPONSE ***
204 No Content HTTP/1.1
```

#### 1.2 Query Templates

Clients that support the [Collection+JSON][] media type SHOULD be able to recognize and parse query templates found within responses. Query templates consist of a [data][] array associated with an [href][] property. The queries array supports query templates.

For query templates, the name/value pairs of the [data][] array set are appended to the URI found in the [href][] property associated with the queries array (with a question-mark ["?"] as separator) and this new URI is sent to the processing agent.

```json
// query template sample
{
  "queries" :
  [
    {
      "href" : "http://example.org/search",
      "rel" : "search",
      "prompt" : "Enter search string",
      "data" :
      [
        {"name" : "search", "value" : ""}
      ]
    }
  ]
}
```

In the above example, if the user supplied "JSON" for the value property, the user agent would construct the following URI:

http://example.org/search?search=JSON

### 2. Objects

An object is an unordered [collection][] of zero or more name/value pairs, where a [name][] is a string and a value is a string, number, boolean, null, object, or array.

The following elements are always represented as objects in [Collection+JSON][] documents. They are: [collection][] error and [template][].

#### 2.1. collection

The [collection][] object contains all the "records" in the representation. This is a REQUIRED object and there MUST NOT be more than one [collection][] object in a [Collection+JSON][] document. It is a top-level document property.

The [collection][] object SHOULD have a version property. For this release, the value of the version property MUST be set to 1.0. If there is no version property present, it should be assumed to be set to 1.0.

The [collection][] object SHOULD have an [href][] property. The [href][] property MUST contain a valid URI. This URI SHOULD represent the address used to retrieve a representation of the document. This URI MAY be used to add a new record (See Reading and Writing Data).

The [collection][] object MAY have an [links][]  array child property.

The [collection][] object MAY have an [items][] array child property.

The [collection][] object MAY have an queries array child property.

The [collection][] object MAY have an [template][] object child property.

The [collection][] object MAY have an error object child property.

```json
// sample collection object
{
  "collection" :
  {
    "version" : "1.0",
    "href" : URI,
    "links" : [ARRAY],
    "items" : [ARRAY],
    "queries" : [ARRAY],
    "template" : {OBJECT},
    "error" : {OBJECT}
  }
}
```

#### 2.2 error

The error object contains additional information on the latest error condition reported by the server. This is an OPTIONAL object and there MUST NOT be more than one error object in a [Collection+JSON][] document. It is a top-level document property.

The following elements MAY appear as child properties of the error object: code message and title.

```json
// sample error object
{
  "error" :
  {
    "title" : STRING,
    "code" : STRING,
    "message" : STRING
  }
}
```

#### 2.3 template

The [template][] object contains all the input elements used to add or edit [collection][] "records." This is an OPTIONAL object and there MUST NOT be more than one [template][] object in a [Collection+JSON][] document. It is a top-level document property.

The [template][] object SHOULD have a [data][] array child property.

```json
// sample template object
{
  "template" :
  {
    "data" : [ARRAY]
  }
}
```

### 3. Arrays

An array is an ordered sequence of zero or more values.

The following elements are always represented as Arrays in [Collection+JSON][] documents. They are: [items][] [data][] queries and links.

#### 3.1. items

The [items][] array represents the list of records in the [Collection+JSON][] document. It is a child property of the [collection][] object.

Each element in the [items][] array SHOULD contain an [href][] property. The [href][] property MUST contain a URI. This URI MAY be used to retrieve a [Collection+JSON][] document representing the associated item. It MAY be used to edit or delete the associated item. See Reading and Writing Data for details.

The [items][] array MAY have a [data][] array child property.

The [items][] array MAY have a [links][] array child property.

```json
// sample items array
{
  "collection" :
  {
    "version" : "1.0",
    "href" : URI,
    "items" :
    [
      {
        "href" : URI,
        "data" : [ARRAY],
        "links" : [ARRAY]
      },
      ...
      {
        "href" : URI,
        "data" : [ARRAY],
        "links" : [ARRAY]
      }
    ]
  }
}
```

#### 3.2. data

The [data][] array is a child property of the [items][] array and the [template][] object.

The [data][] array SHOULD contain one or more anonymous objects. Each object MAY have any of three possible properties: [name][] (REQUIRED), value (OPTIONAL), and prompt (OPTIONAL).

```json
// sample data array
{
  "template" :
  {
    "data" :
    [
      {"prompt" : STRING, "name" : STRING, "value" : VALUE},
      {"prompt" : STRING, "name" : STRING, "value" : VALUE},
      ...
      {"prompt" : STRING, "name" : STRING, "value" : VALUE}
    ]
  }
}
```

#### 3.2. queries

The queries array is an OPTIONAL top-level property of the [Collection+JSON][] document.

The queries array SHOULD contain one or more anonymous objects. Each object composed of five possible properties: [href][] (REQUIRED), rel (REQUIRED), [name][] (OPTIONAL), prompt (OPTIONAL), and a [data][] array (OPTIONAL).

If present, the [data][] array represents query parameters for the associated [href][] property of the same object. See Query Templates for details.

```json
// sample queries array
{
  "queries" :
  [
    {"href" : URI, "rel" : STRING, "prompt" : STRING, "name" : STRING},
    {"href" : URI, "rel" : STRING, "prompt" : STRING, "name" : STRING,
      "data" :
      [
        {"name" : STRING, "value" : VALUE}
      ]
    },
    ...
    {"href" : URI, "rel" : STRING, "prompt" : STRING, "name" : STRING}
  ]
}
```

#### 3.4. links

The [links][] array is an OPTIONAL child property of the [items][] array. It SHOULD contain one or more anonymous objects. Each has five possible properties: [href][] (REQUIRED), rel (REQURIED), [name][] (OPTIONAL), render (OPTIONAL), and prompt, (OPTIONAL).

```json
// sample links array
{
  "collection" :
  {
    "version" : "1.0",
    "href" : URI,
    "items" :
    [
      {
        "href" : URI,
        "data" : [ARRAY],
        "links" :
        [
          {"href" : URI, "rel" : STRING, "prompt" : STRING, "name" : STRING, "render" : "image"},
          {"href" : URI, "rel" : STRING, "prompt" : STRING, "name" : STRING, "render" : "link"},
          ...
          {"href" : URI, "rel" : STRING, "prompt" : STRING, "name" : STRING}
        ]
      }
    ]
  }
}
```

### 4. Properties

Properties represent individual name/value pairs for objects within [Collection+JSON][] documents.

#### 4.1. code

The code property MAY be a child property of the error object.

It SHOULD be a STRING type.

#### 4.2. href

The [href][] MAY be a child property of the following: [collection][] object [items][] array elements [links][] array elements and queries array elements.

It MUST contain a valid URI.

#### 4.3. message

The message property MAY be a child property of the error object.

It SHOULD be a STRING type.

#### 4.4. name

The [name][] property MAY be a child element of the [data][] array and the [links][] array.

It SHOULD be a STRING data type.

#### 4.5. prompt

The prompt property MAY appear as a child property of the queries array, the [links][] array, and the [data][] array.

It SHOULD be a STRING data type.

#### 4.6. rel

The rel MAY be a child property of the following: [links][] array elements and queries array elements.

It SHOULD be a STRING data type.

#### 4.7. render

The render MAY be a child property of the [links][] array element.

It SHOULD be a STRING data type. The value MUST be either __image__ or __link__. If the render property does not appear on a [links][] array element, it should be assumed to be set to __link__.

#### 4.8. title

The title property MAY be a child property of the error object.

It SHOULD be a STRING type.

#### 4.9. value

The value property MAY be a child element of [data][] array elements

It MAY contain one of the following data types: STRING NUMBER truev false or null

#### 4.10. version

The version SHOULD be a child property of the [collection][] collection element. It SHOULD be a STRING data type. For this release, the version SHOULD be set to 1.0.

### 5. Link Relations

Link relation values can be used to annotate [links][] array and queries array properties.

#### 5.1. collection

The target IRI points to a resource which represents a list of which the context IRI is a member.

When used in the [Collection+JSON][] media type, this link relation value refers to a [collection][] document.

Logged with the [Microformats Existing Rel Values][].

#### 5.2. item

The target IRI points to a resource that is a member of a [collection][] represented by the context IRI.

When used in the [Collection+JSON][] media type, this link relation refers to an individual [item][] in a collection.

Logged with the [Microformats Existing Rel Values][].

#### 5.3. template

The target IRI points to a resource that is a representation of a valid write template for the data represented by the context IRI.

When use in the [Collection+JSON][] media type, this link relation value refers to the [template][] object of a collection.

#### 5.4 queries

The target IRI points to a resource that is a representation of a list of valid queries that can be executed against the data represented by the context IRI.

When used in the [Collection+JSON][] media type, this link relation value refers to the queries object of a collection.

#### 5.5 Other Link Relation Values

The [Collection+JSON][] media type has a limited set of defined link relations. Implementors are free to use any additional link relation values as needed and are encouraged to use already defined link relation values found in existing registries.

Suggested registries are (but not limited to):

* [IANA Link Relations](http://www.iana.org/assignments/link-relations/link-relations.xml)
* [Microformats Existing Rel Values][]
* [Expressing Dublin Core metadata using HTML/XHTML meta and link elements](http://dublincore.org/documents/dc-html/)

Implementors may also wish to create their own unique link relation values using the standards outlined in [RFC5988](http://tools.ietf.org/html/rfc5988).

### 6. Data Types

Below are the data types used in [Collection+JSON][] documents. The reference for most of the data type definitions below is the [JSON Grammar](http://tools.ietf.org/html/rfc4627#section-2) section of the [RFC4627](http://tools.ietf.org/html/rfc4627).

#### 6.1. OBJECT

An OBJECT structure is represented as a pair of curly brackets surrounding zero or more name/value pairs (or members). A name is a string. A single colon comes after each name, separating the name from the value. A single comma separates a value from a following name. The names within an object SHOULD be unique.

#### 6.2. ARRAY

An ARRAY structure is represented as square brackets surrounding zero or more values (or elements). Elements are separated by commas.

#### 6.3. NUMBER

A NUMBER contains an integer component that may be prefixed with an optional minus sign, which may be followed by a fraction part and/or an exponent part.

Octal and hex forms are not allowed. Leading zeros are not allowed.

A fraction part is a decimal point followed by one or more digits.

An exponent part begins with the letter E in upper or lowercase, which may be followed by a plus or minus sign. The E and optional sign are followed by one or more digits.

#### 6.4. STRING

A STRING begins and ends with quotation marks. All Unicode characters may be placed within the quotation marks except for the characters that must be escaped: quotation mark, reverse solidus, and the control characters (U+0000 through U+001F).

#### 6.5. URI

A URI is defined by [RFC 3986](http://tools.ietf.org/html/rfc3986)

#### 6.6. VALUE

A VALUE data type MUST be a NUMBER, STRING, or one of the following literal names: __false__, __null__, or __true__.

__NOTE__: This release of [Collection+JSON][] does not support OBJECT or ARRAY as a valid VALUE.

### 7. Extensibility

This document describes the [Collection+JSON][] markup vocabulary. Markup from other vocabularies ("foreign markup") can be used in a [Collection+JSON][] document. Any extensions to the [Collection+JSON][] vocabulary MUST not redefine any objects (or their properties), arrays, properties, link relations, or data types defined in this document. Clients that do not recognize extensions to the [Collection+JSON][] vocabulary SHOULD ignore them.

The details of designing and implementing [Collection+JSON][] extensions is beyond the scope of this document.

__NOTE__: It is possible that future forward-compatible modifications to this specification will include new objects, arrays, properties, link-relations, and data types. Extension designers should take care to prevent future modifications from breaking or redefining those extensions.

### 8. Acknowledgements

The author gratefully acknowledges the following individuals who generously contributed corrections, comments, and criticism on the design of this media type:

* [Erlend Hamnaberg](http://twitter.com/hamnis)
* [Nick Pendley](http://twitter.com/pc1oad1etter)
* [David Metcalf](http://metcalf.net/)
* (Qfwfq)

### 9. References

* [RFC2119 - Key words for use in RFCs to Indicate Requirement Levels](http://tools.ietf.org/html/rfc2119)
* [Create-Update-Read-Delete (CRUD) [Wikipedia]](http://en.wikipedia.org/wiki/Create,_read,_update_and_delete)
* [IANA Link Relations](http://www.iana.org/assignments/link-relations/link-relations.xml)
* [Microformats Existing Rel Values](http://microformats.org/wiki/existing-rel-values)
* [Paramsr.us - Registering Link Relation Types](http://paramsr.us/link-relation-types/)
* [Expressing Dublin Core metadata using HTML/XHTML meta and link elements](http://dublincore.org/documents/dc-html/)
* [RFC5988 - Web Linking](http://tools.ietf.org/html/rfc5988)
* [RFC2627 - The application/json Media Type for JavaScript Object Notation (JSON)](http://tools.ietf.org/html/rfc4627)
* [RFC3986 - Uniform Resource Identifier (URI): Generic Syntax](http://tools.ietf.org/html/rfc3986)

[Collection+JSON]: http://amundsen.com/media-types/collection/format/

[query templates]: #12-query-templates

[collection]: #21-collection
[template]: #23-template
[error]: #22-error

[item]: #31-items
[items]: #31-items
[data]: #32-data
[queries]: #33-queries
[link]: #34-links
[links]: #34-links

[code]: #41-code
[href]: #42-href
[message]: #43-message
[name]: #44-name
[prompt]: #45-prompt
[rel]: #46-rel
[render]: #47-render
[title]: #48-title
[value]: #49-value
[version]: #410-version

[link relation]: #5-link-relations
[additional values]: #55-other-link-relation-values
[URI]: #65-uri
[examples]: http://amundsen.com/media-types/collection/examples/
[CRUD]: http://en.wikipedia.org/wiki/Create,_read,_update_and_delete
[Microformats Existing Rel Values]: http://microformats.org/wiki/existing-rel-values#non_HTML_rel_values
