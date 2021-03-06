---
domain: rfc.eventsourcing.com
shortname: 1/ELF
name: Entity Layout Framework
status: draft
editor: Yurii Rashkovskii <yrashk@gmail.com>
---

Entity Layout Framework is a lightweight protocol for defining structured data
types, intended to be used across different implementations of Eventsourcing
and other software.

## License

Copyright (c) 2016 Yurii Rashkovskii

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version.

This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program; if not, see [http://www.gnu.org/licenses](http://www.gnu.org/licenses).

## Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## 0. Goals

* To define basic data structures
* To eliminate a class of errors related to mismanaged data structures versioning

## 1. Data Types

Although this specification allows for defining an arbitrary number of *custom*
types, there's a set of *standard* types that MUST be supported by
every implementation that aims to conform to this specification.

No data type supports `null` values. An implementation MAY coerce null values
to suggested *default values*.

### 1.1. Boolean

Default value: `false`

### 1.2. Short

Default value: `0`

### 1.3. Integer

Default value: `0`

### 1.4. Long

Default value: `0`

### 1.5. BigDecimal

BigDecimal is an arbitrary-precision signed decimal numbers. It consists of an arbitrary precision integer (*unscaled value*) and a 32-bit integer (*scale*).
If zero or positive, the scale is the number of digits to the right of the decimal point. If negative, the unscaled value of the number is multiplied by ten to the power of the negation of the scale. The value of the number represented is therefore `(unscaledValue * 10^-scale)`. This definition
mirrors the definition of Java's BigDecimal.

Default value: `0`

### 1.6. BigInteger

BigDecimal is an arbitrary precision integer.

Default value: `0`

### 1.7. Float

Default value: `0.0`

### 1.8. Double

Default value: `0.0`

### 1.9. Byte

Default value: `0`

### 1.10. ByteArray

ByteArray is an array of bytes.

Default value: empty array.

### 1.11. String

UTF-8 string.

Default value: empty string

### 1.12. UUID

Default value: `00000000-0000-0000-0000-000000000000`

### 1.13. Timestamp

Default value: January 1, 1970, 00:00:00 GMT

### 1.14. List

List is a parametrized type and can take any other type as a parameter.

Default value: empty list

### 1.15. Optional

Optional is a type that signifies a value that might be either present or not.

Optional is a parametrized type and can take any other type as a parameter.

Default value: value not present

### 1.16. Enum

Enum represents a type with a closed set of possible ordinal (integer) values (for example, `OPEN = 0, CLOSED = 1`). The ordinal values MUST start with 0
and form a consecutive sequence.

Default value: smallest ordinal value (0).

### 1.17. Map

Map represents a collection of (key, value) pairs, such that each possible
key appears at most once in the collection.

Default value: an empty map.

### 1.18. Object

See [3. Object](../1/README.md#object) for the definition.

Object is represented as a sequence of all its (lexicographically sorted) properties representations.

## 2. Fingerprinting

Fingerprinting is a technique that gives a specific, byte-to-byte representation
of type information. Every fingerprint is a sequence of bytes that
MUST uniquely represents the data type.

It is RECOMMENDED that a human readable, latin1 encoded name is specified for types to avoid decentralized allocation problem. However, this is not a requirement but a suggestion, followed by standard types defined in this specification.


| Type          | Fingerprint (hex)             | Fingerprint (Human-readable)  |
|---------------|-------------------------------|-------------------------------|
| Boolean       | 0x426f6f6c65616e              | Boolean                       |
| Short         | 0x53686f7274                  | Short                         |
| Integer       | 0x496e7465676572              | Integer                       |
| Long          | 0x4c6f6e67                    | Long                          |
| BigDecimal    | 0x426967446563696d616c        | BigDecimal                    |
| BigInteger    | 0x426967496e7465676572        | BigInteger                    |
| Float         | 0x466c6f6174                  | Float                         |
| Double        | 0x446f75626c65                | Double                        |
| Byte          | 0x42797465                    | Byte                          |
| ByteArray     | 0x427974654172726179          | ByteArray                     |
| String        | 0x537472696e67                | String                        |
| UUID          | 0x55554944                    | UUID                          |
| Timestamp     | 0x54696d657374616d70          | Timestamp                     |
| List          | 0x4c6973745b + ? + 5d         | List[?]                       |
| Optional      | 0x4f7074696f6e616c5b + ? + 5d | Optional[?]                   |
| Enum          | 0x456e756d5b + ? + 5d         | Enum[?], where ? takes a form of NAME:ORDINAL[,...]. For example, `OPEN:1,CLOSED:2` |
| Map           | 0x4d61705b + ? + 5d5b + ? + 5d| Map[?][?] |
| Entity Layout | ?                             | Layout fingerprint (bytes) |

## 3. Object Layout <a name="object-layout"></a>

Object is a data type that consists of its own name and number of typed and named properties. Typically, this would be a a class or a record.

All entity's properties MUST be sorted lexicographically. This enforces the following:

1. Actual entity deserialization happens without the knowledge of order of supplied properties and always follows the same order across implementations, allowing to avoid supplying property sequence number or type information.
1. Fingerprints are always the same regardless of the order of supplied properties, producing a consistent versioning artifact (see 3.1.)

### 3.1. Fingerprint

Entity layout fingerprint is formed by following the following procedure:

1. Create a hasher (using *hashing algorithm* as defined in 5.1.)
1. Update the hasher with the type name, serialized as UTF-8.
1. For every property,
   1. update the hasher with the property name, serialized as UTF-8.
   1. update the hasher with the property type's fingerprint.

The resulting hash is used as a fingerprint of the entity layout.

## 4. Entity

*Entity* is a top-level [object](../1/README.md#object-layout) that can be recorded by Eventsourcing. Every entity MUST have the following base layout:

| Type                                               | Property       |
|----------------------------------------------------|----------------|
| [HLC](rfc.eventsourcing.com/spec:6/HLC/#Timestamp) | timestamp      |

Any recorded entity MUST be identified with a UUID. UUID does not become
a part of its layout.

There are two types of entities.

### 4.1. Command

This entity type represents a request for changes.

### 4.2. Event

This entity type represents a statement of fact.

## 5. References

### 5.1. Hashing Algorithm

This specification defines *hashing algorithm* as SHA-1.
