# RFC 0/LAYOUT

Eventsourcing Layout is a data storage and exchange serialization used by
compatible (mutually intelligible) implementations.

* State: raw
* Editor: Yurii Rashkovskii <yrashk@gmail.com>

## License

Copyright (c) 2016 Yurii Rashkovskii

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version.

This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program; if not, see [http://www.gnu.org/licenses](http://www.gnu.org/licenses).

## Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## 0. Goals

* To reduce the bandwidth and storage requirements for Eventsourcing entities
* To eliminate a class of errors related to mismanaged entity structure versioning

## 1. Data Types

Although this specification allows for defining an arbitrary number of *custom*
types, there's a set of *standard* types that MUST be supported by
every implementation that aims to conform to this specification.

No data type supports `null` values. An implementation MAY coerce null values
to suggested *default values*.

All standard types use big endian byte order, unless noted otherwise.

### 1.1. Boolean

Boolean serialization is always of a constant size (1 byte).

| Offset (bytes) | Length (bytes) | Value                      |
|----------------|----------------|----------------------------|
| 0              |  1             | 0 if false, 1 if true      |

Default value: `false`

### 1.2. Short

Short serialization is always of a constant size (2 bytes).

| Offset (bytes) | Length (bytes) | Value                      |
|----------------|----------------|----------------------------|
| 0              |  2             | short value                |

Default value: `0`

### 1.3. Integer

Integer serialization is always of a constant size (4 bytes).

| Offset (bytes) | Length (bytes) | Value                      |
|----------------|----------------|----------------------------|
| 0              |  4             | integer value              |

Default value: `0`

### 1.4. Long

Long serialization is always of a constant size (8 bytes).

| Offset (bytes) | Length (bytes) | Value                      |
|----------------|----------------|----------------------------|
| 0              |  8             | long value                 |

Default value: `0`

### 1.5. BigDecimal

BigDecimal is an arbitrary-precision signed decimal numbers. It consists of an arbitrary precision integer (*unscaled value*) and a 32-bit integer (*scale*).
If zero or positive, the scale is the number of digits to the right of the decimal point. If negative, the unscaled value of the number is multiplied by ten to the power of the negation of the scale. The value of the number represented is therefore `(unscaledValue * 10^-scale)`. This definition
mirrors the definition of Java's BigDecimal.

BigDecimal serialization is of a variable size.

| Offset (bytes) | Length (bytes) | Value                                |
|----------------|----------------|--------------------------------------|
| 0              |  4             | *len* = length of the unscaled value |
| 4              |  4             | scale                                |
| 8              |  *len*         | unscaled value                       |

The unscaled value is the The unscaled will contain the minimum number of bytes required to represent the value, including at least one sign bit.

Default value: `0`

### 1.6. Float

Float serialization is always of a constant size (4 bytes).

| Offset (bytes) | Length (bytes) | Value                      |
|----------------|----------------|----------------------------|
| 0              |  4             | float value                |

Default value: `0.0`

### 1.7. Double

Double serialization is always of a constant size (8 bytes).

| Offset (bytes) | Length (bytes) | Value                      |
|----------------|----------------|----------------------------|
| 0              |  8             | double value               |

Default value: `0.0`

### 1.8. Byte

Byte serialization is always of a constant size (1 bytes).

| Offset (bytes) | Length (bytes) | Value                      |
|----------------|----------------|----------------------------|
| 0              |  1             | byte value                 |

Default value: `0`

### 1.9. ByteArray

ByteArray is an array of bytes.

ByteArray serialization is of a variable size.

| Offset (bytes) | Length (bytes) | Value                      |
|----------------|----------------|----------------------------|
| 0              |  4             | *len* = number of bytes    |
| 4              |  *len*         | bytes                      |

Default value: empty array.

### 1.10. Character

The serialization format is based on the original Unicode specification, which defined characters as fixed-width 16-bit entities. Even though it is an outdated
representation superseded by newer Unicode standards, this format is kept
in the sake of simplifying usage of languages that still use this representation.

Character serialization is always of a constant size (2 bytes).

| Offset (bytes) | Length (bytes) | Value                      |
|----------------|----------------|----------------------------|
| 0              |  2             | character value            |

Default value: `0x0000`

### 1.11. String

Character serialization is of variable size.

| Offset (bytes) | Length (bytes) | Value                        |
|----------------|----------------|------------------------------|
| 0              |  4             | *len* = string size in bytes |
| 4              |  *len*         | UTF-8 encoded string         |

Default value: empty string

### 1.12. UUID

UUID serialization is always of a constant size (16 bytes)

| Offset (bytes) | Length (bytes) | Value                        |
|----------------|----------------|------------------------------|
| 0              |  8             | most significant bytes       |
| 8              |  8             | least significant bytes      |

Default value: `00000000-0000-0000-0000-000000000000`

### 1.13. List

List is a parametrized type and can take any other type as a parameter.
It, therefore, delegates to a underlying type's serialization format.

| Offset (bytes) | Length (bytes)           | Value                           |
|----------------|--------------------------|---------------------------------|
| 0              |  4                       | *len* = number of list elements |
| 4              |  *sum(len(serialize_N))* | serialize_N(T) byte array       |

Default value: empty list

### 1.14. Optional

Optional is a type that signifies a value that might be either present or not.

Optional is a parametrized type and can take any other type as a parameter. It, therefore, delegates to a underlying type's serialization format.

| Offset (bytes) | Length (bytes)           | Value                           |
|----------------|--------------------------|---------------------------------|
| 0              |  1                       | 0, if the value is not present, 1 otherwise |

Default value: value not present

### 1.15. Enum

Enum represents a type with a closed set of possible ordinal (integer) values (for example, `OPEN = 0, CLOSED = 1`)

Enum serialization is always of a constant size (4 bytes).

| Offset (bytes) | Length (bytes) | Value                      |
|----------------|----------------|----------------------------|
| 0              |  4             | ordinal (integer) value    |

Default value: `0`

## 2. Fingerprinting

Fingerprinting is a technique that gives a specific, byte-to-byte representation
of type information. Every fingerprint is a sequence of bytes that
MUST uniquely represents the data type.

It is RECOMMENDED that a human readable, latin1 encoded name is specified for types to avoid decentralized allocation problem. However, this is not a requirement but a suggestion, followed by standard types defined in this specification.


| Type        | Fingerprint (hex)             | Fingerprint (Human-readable)  |
|-------------|-------------------------------|-------------------------------|
| Boolean     | 0x426f6f6c65616e              | Boolean                       |
| Short       | 0x53686f7274                  | Short                         |
| Integer     | 0x496e7465676572              | Integer                       |
| Long        | 0x4c6f6e67                    | Long                          |
| BigDecimal  | 0x426967446563696d616c        | BigDecimal                    |
| Float       | 0x466c6f6174                  | Float                         |
| Double      | 0x446f75626c65                | Double                        |
| Byte        | 0x42797465                    | Byte                          |
| ByteArray   | 0x427974654172726179          | ByteArray                     |
| Character   | 0x436861726163746572          | Character                     |
| String      | 0x537472696e67                | String                        |
| UUID        | 0x55554944                    | UUID                          |
| List        | 0x4c6973745b + ? + 5d         | List[?]                       |
| Optional    | 0x4f7074696f6e616c5b + ? + 5d | Optional[?]                   |
| Enum        | 0x456e756d5b + ? + 5d         | Enum[?], where ? takes a form of NAME:ORDINAL[,...]. For example, `OPEN:1,CLOSED:2` |

## 3. Entity Layout

Entity is a data type that consists of its own name and number of typed and named properties. Typically, this would be a a class or a record.

### 3.1. Fingerprint

Entity layout fingerprint is formed by following the following procedure:

1. Create a hasher (using *hashing algorithm* as defined in 4.1.)
1. Update the hasher with the type name, serialized as UTF-8.
1. Sort all entity's properties lexicographically.
1. For every property,
   1. update the hasher with the property name, serialized as UTF-8.
   1. update the hasher with the property type's fingerprint.

The resulting hash is used as a version of the entity layout.

## 4. References

### 4.1. Hashing Algorithm

This specification defines *hashing algorithm* as SHA-1.
