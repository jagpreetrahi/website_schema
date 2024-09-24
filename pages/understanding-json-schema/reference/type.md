---
title: "Type-specific keywords"
section: docs
---

The `type` [keyword](../../learn/glossary#keyword) is fundamental to JSON Schema. It specifies the data
type for a schema.

At its core, JSON Schema defines the following basic types:

* [string](../../understanding-json-schema/reference/string)
* [number](../../understanding-json-schema/reference/numeric#number)
* [integer](../../understanding-json-schema/reference/numeric#integer)
* [object](../../understanding-json-schema/reference/object)
* [array](../../understanding-json-schema/reference/array)
* [boolean](../../understanding-json-schema/reference/boolean)
* [null](../../understanding-json-schema/reference/null)

These types have analogs in most programming languages, though they may
go by different names.

[tabs-start "Language-specific info"]

[tab "Python"]

The following table maps from the names of JSON types to their
analogous types in Python:

| JSON    | Python       |
|---------|--------------|
| string  | string *1    |
| number  | int/float *2 |
| object  | dict         |
| array   | list         |
| boolean | bool         |
| null    | None         |

#### Footnotes

[#1] Since JSON strings always support unicode, they are
analogous to `unicode` on Python 2.x and `str` on
Python 3.x.

[#2] JSON does not have separate types for integer and
floating-point.

[tab "Ruby"]

The following table maps from the names of JSON types to their
analogous types in Ruby:

| JSON    | Ruby                 |
|---------|----------------------|
| string  | String               |
| number  | Integer/Float *3     |
| object  | Hash                 |
| array   | Array                |
| boolean | TrueClass/FalseClass |
| null    | NilClass             |

#### Footnotes

[#3] JSON does not have separate types for integer and
floating-point.

[tab "Perl"]

The following table maps from the names of JSON types to
their analogous types in Objective-C:

| JSON    | Perl                                             |
|---------|--------------------------------------------------|
| string  | scalar (SV)                                      |
| number  | scalar (NV or IV), or Math::BigNum, Math::BigInt |
| object  | reftype=HASH                                     |
| array   | reftype=ARRAY                                    |
| boolean | scalar or JSON::PP::Boolean                      |
| null    | scalar (`undef`)                                 |

[tab "Objective-C"]

The following table maps from the names of JavaScript types to
their analogous types in Objective-C:

| JSON    | Objective-C  |
|---------|--------------|
| string  | NSString     |
| number  | NSNumber     |
| object  | NSDictionary |
| array   | NSArray      |
| boolean | NSNumber     |
|         | [#4]_        |
| null    | NSNull       |

#### Footnotes

[#4] `NSJSONSerialization` represents JavaScript numbers and booleans
as `NSNumber`. To distinguish them, we need to check an `NSNumber`
value for identity (pointer equality) to `@YES` and `@NO` constants.

[tab "Swift"]

The following table maps from the names of JavaScript types to
their analogous types in Swift:

| JSON    | Swift      |
|---------|------------|
| string  | String     |
| number  | Int/Double |
|         | [#5]_      |
| object  | Dictionary |
| array   | Array      |
| boolean | Bool       |
| null    | Optional   |

#### Footnotes

[#5] While JavaScript does not have separate types for integer and
floating-point, Swift ``JSONDecoder`` throws an error on attempt
to decode ``Int`` from a non-integer number in JSON.

[tabs-end]

The `type` keyword may either be a string or an array:

-   If it\'s a string, it is the name of one of the basic types above.
-   If it is an array, it must be an array of strings, where each string
    is the name of one of the basic types, and each element is unique.
    In this case, the JSON snippet is valid if it matches *any* of the
    given types.

Here is a simple example of using the `type` keyword:

```json
// props { "isSchema": true }
{ "type": "number" }
```

```json
// props { "indent": true, "valid": true }
42
```
```json
// props { "indent": true, "valid": true }
42.0
```

```json
// props { "indent": true, "valid": false }
"42"
```

In the following example, we accept strings and numbers, but not
structured data types:

```json
// props { "isSchema": true }
{ "type": ["number", "string"] }
```
```json
// props { "indent": true, "valid": true }
42
```
```json
// props { "indent": true, "valid": true }
"Life, the universe, and everything"
```
```json
// props { "indent": true, "valid": false }
["Life", "the universe", "and everything"]
```

For each of these types, there are keywords that only apply to those
types. For example, numeric types have a way of specifying a numeric
range, that would not be applicable to other types. In this reference,
these validation keywords are described along with each of their
corresponding types in the following chapters.


## Format[#format]

The `format` keyword allows for basic semantic identification for non string types values also that are 
commonly used. For example, because JSON
doesn\'t have a \"DateTime\" type, dates need to be encoded as strings.
`format` allows the schema author to indicate that the string value
should be interpreted as a date. By default, `format` is just an
annotation and does not effect validation.

Optionally, validator [implementations](../../learn/glossary#implementation) can provide a configuration option
to enable `format` to function as an assertion rather than just an
annotation. That means that validation will fail if, for example, a
value with a `date` format isn\'t in a form that can be parsed as a
date. This can allow values to be constrained beyond what the other
tools in JSON Schema, including [Regular Expressions](../../understanding-json-schema/reference/regular_expressions) can
do.

> Implementations may provide validation for only a subset of the built-in
> formats or do partial validation for a given format. For example, some
> implementations may consider a string an email if it contains a `@`,
> while others might do additional checks for other aspects of a well
> formed email address.

There is a bias toward networking-related formats in the JSON Schema
specification, most likely due to its heritage in web technologies.
However, custom formats may also be used, as long as the parties
exchanging the JSON documents also exchange information about the custom
format types. A JSON Schema validator will ignore any format type that
it does not understand.

### Built-in formats[#built-in-formats]

The following is the list of formats specified in the JSON Schema
specification.

#### Dates and times

Dates and times are represented in [RFC 3339, section 5.6](https://tools.ietf.org/html/rfc3339#section-5.6). This is a subset
of the date format also commonly known as [ISO8601 format](https://www.iso.org/iso-8601-date-and-time-format.html).

- `"date-time"`: Date and time together, for example,
  `2018-11-13T20:20:39+00:00`.
- `"time"`: <StarInline label="New in draft 7" /> Time, for example, `20:20:39+00:00`
- `"date"`: <StarInline label="New in draft 7" /> Date, for example, `2018-11-13`.
- `"duration"`: <StarInline label="New in draft 2019-09" /> A duration as defined by the [ISO 8601 ABNF for \"duration\"](https://datatracker.ietf.org/doc/html/rfc3339#appendix-A).

```json
// props { "isSchema": true }
    {
      "type": "string",
      "format": "date-time",
    }
```
  For example, `PT1` expresses a duration of 1 minute.

```json
  {
    "event": "Conference Call",
    "scheduledTime": "2024-09-24T10:00:00Z",
    "duration": "PT1H30M",
    "unixTimestamp": 1695553200
  } 
```   
  This example represents an event with start and end times, including both ISO 8601 date-time formats and a Unix timestamp.

<Keywords label="single: email single: idn-email single: format; email single: format; idn-email" />

#### Email addresses

- `"email"`: Internet email address, see [RFC 5321, section 4.1.2](http://tools.ietf.org/html/rfc5321#section-4.1.2).
- `"idn-email"`: <StarInline label="New in draft 7" /> The internationalized form of an Internet email
  address, see [RFC 6531](https://tools.ietf.org/html/rfc6531).

```json
// props { "isSchema": true }
     {
       "type": "string",
       "format": "email"
     }
```  
For example, represents a user with a valid email.

```json
     {
        "username": "johndoe",
        "email": "johndoe@example.com"
     }
```     



<Keywords label="single: hostname single: idn-hostname single: format; hostname single: format; idn-hostname" />

#### Hostnames

- `"hostname"`: Internet host name, see [RFC 1123, section 2.1](https://datatracker.ietf.org/doc/html/rfc1123#section-2.1).
- `"idn-hostname"`: <StarInline label="New in draft 7" /> An internationalized Internet host name, see
  [RFC5890, section 2.3.2.3](https://tools.ietf.org/html/rfc5890#section-2.3.2.3).

```json
// props { "isSchema": true }
    {
      "type": "string",
      "format": "hostname"
    }

```
For example, represents a configuration object with a valid hostname.

```json
    {
       "service": "webserver",
       "hostname": "example.com"
    }
```

<Keywords label="single: ipv4 single: ipv6 single: format; ipv4 single: format; ipv6" />

#### IP Addresses

- `"ipv4"`: IPv4 address, according to dotted-quad ABNF syntax as
  defined in [RFC 2673, section 3.2](http://tools.ietf.org/html/rfc2673#section-3.2).
- `"ipv6"`: IPv6 address, as defined in [RFC 2373, section 2.2](http://tools.ietf.org/html/rfc2373#section-2.2).

```json
// props { "isSchema": true }
    {
      "type": "string",
      "format": "ipv4" or "ipv6"
    }
```
For example,  represents a configuration object that includes both IPv4 and IPv6 addresses.

```json
    {
      "service": "database",
      "ipv4": "192.168.1.1",
      "ipv6": "2001:0db8:85a3:0000:0000:8a2e:0370:7334"
    }
```

<Keywords label="single: uuid single: uri single: uri-reference single: iri single: iri-reference single: format; uuid single: format; uri single: format; uri-reference single: format; iri single: format; iri-reference" />

#### Resource identifiers

- `"uuid"`: <StarInline label="New in draft 2019-09" /> A Universally Unique Identifier as defined by [RFC 4122](https://datatracker.ietf.org/doc/html/rfc4122). Example:
  `3e4666bf-d5e5-4aa7-b8ce-cefe41c7568a`
- `"uri"`: A universal resource identifier (URI), according to
  [RFC3986](http://tools.ietf.org/html/rfc3986).
- `"uri-reference"`: <StarInline label="New in draft 6" /> A URI Reference (either a URI or a
  relative-reference), according to [RFC3986, section 4.1](http://tools.ietf.org/html/rfc3986#section-4.1).
- `"iri"`: <StarInline label="New in draft 7" /> The internationalized equivalent of a \"uri\", according to
  [RFC3987](https://tools.ietf.org/html/rfc3987).
- `"iri-reference"`: <StarInline label="New in draft 7" /> The internationalized equivalent of a
  \"uri-reference\", according to
  [RFC3987](https://tools.ietf.org/html/rfc3987)

```json
// props { "isSchema": true }
    {
      "type": "string",
      "format": "uuid" or "uri" or "iri"
    }
```
For example, represents a configuration object that includes a UUID and a URI.

```json

    {
       "service": "resource-manager",
       "uuid": "3e4666bf-d5e5-4aa7-b8ce-cefe41c7568a",
       "uri": "https://api.example.com/resource"
    }
```

If the values in the schema have the ability to be relative to a
particular source path (such as a link from a webpage), it is generally
better practice to use `"uri-reference"` (or `"iri-reference"`) rather
than `"uri"` (or `"iri"`). `"uri"` should only be used when the path
must be absolute.

<Keywords label="single: uri-template single: format; uri-template" />

#### URI template

- `"uri-template"`: <StarInline label="New in draft 6" /> A URI Template (of any level) according to
  [RFC6570](https://tools.ietf.org/html/rfc6570). If you don\'t
  already know what a URI Template is, you probably don\'t need this
  value.

```json
// props { "isSchema": true }
    {
      "type": "string",
      "format": "uri-template"
    }
```
For Example,

```json
     "/users/{userId}/posts/{postId}"
```

<Keywords label="single: json-pointer single: relative-json-pointer single: format; json-pointer single: format; relative-json-pointer" />

#### JSON Pointer

- `"json-pointer"`: <StarInline label="New in draft 6" /> A JSON Pointer, according to
  [RFC6901](https://tools.ietf.org/html/rfc6901). There is more
  discussion on the use of JSON Pointer within JSON Schema in
  [Structuring a complex schema](../../understanding-json-schema/structuring). Note that this should be used only when
  the entire string contains only JSON Pointer content, e.g.
  `/foo/bar`. JSON Pointer URI fragments, e.g. `#/foo/bar/` should use
  `"uri-reference"`.
- `"relative-json-pointer"`: <StarInline label="New in draft 7" /> A [relative JSON pointer](https://tools.ietf.org/html/draft-handrews-relative-json-pointer-01).

```json
// props { "isSchema": true }
    {
      "type": "string",
      "format": "json-pointer"
    }
```
For Example,

```json
    "/foo/bar"
```

<Keywords label="single: regex single: format; regex" />

#### Regular Expressions

- `"regex"`: <StarInline label="New in draft 7" /> A regular expression, which should be valid according to
  the [ECMA 262](https://www.ecma-international.org/publications-and-standards/standards/ecma-262/)
  [dialect](../../learn/glossary#dialect).

Be careful, in practice, JSON schema validators are only required to
accept the safe subset of [regular expressions](../../understanding-json-schema/reference/regular_expressions) described elsewhere in this document.

