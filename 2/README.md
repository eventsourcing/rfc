---
domain: rfc.eventsourcing.com
shortname: 2/BES
name: Binary Entity Serialization
status: draft
editor: Yurii Rashkovskii <yrashk@gmail.com>
---

Binary Entity Serialization is a data storage and exchange serialization format used in Eventsourcing and other projects.

## License

Copyright (c) 2016 Yurii Rashkovskii

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version.

This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program; if not, see [http://www.gnu.org/licenses](http://www.gnu.org/licenses).

## Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## 0. Goals

* To reduce the bandwidth and storage requirements for Eventsourcing entities
* To be compatible with [RFC1/ELF](../1/README.md)

## 1. Data Types

All standard types use big endian byte order, unless noted otherwise.

### 1.1. Boolean

Boolean serialization is always of a constant size (1 byte).

| Offset (bytes) | Length (bytes) | Value                      |
|----------------|----------------|----------------------------|
| 0              |  1             | 0 if false, 1 if true      |

### 1.2. Short

Short serialization is always of a constant size (2 bytes).

| Offset (bytes) | Length (bytes) | Value                      |
|----------------|----------------|----------------------------|
| 0              |  2             | short value                |

### 1.3. Integer

Integer serialization is always of a constant size (4 bytes).

| Offset (bytes) | Length (bytes) | Value                      |
|----------------|----------------|----------------------------|
| 0              |  4             | integer value              |

### 1.4. Long

Long serialization is always of a constant size (8 bytes).

| Offset (bytes) | Length (bytes) | Value                      |
|----------------|----------------|----------------------------|
| 0              |  8             | long value                 |

### 1.5. BigDecimal

BigDecimal serialization is of a variable size.

| Offset (bytes) | Length (bytes) | Value                                |
|----------------|----------------|--------------------------------------|
| 0              |  4             | *len* = length of the unscaled value |
| 4              |  4             | scale                                |
| 8              |  *len*         | unscaled value                       |

The unscaled value is the The unscaled will contain the minimum number of bytes required to represent the value, including at least one sign bit.

### 1.6. Float

Float serialization is always of a constant size (4 bytes).

| Offset (bytes) | Length (bytes) | Value                      |
|----------------|----------------|----------------------------|
| 0              |  4             | float value                |

### 1.7. Double

Double serialization is always of a constant size (8 bytes).

| Offset (bytes) | Length (bytes) | Value                      |
|----------------|----------------|----------------------------|
| 0              |  8             | double value               |

### 1.8. Byte

Byte serialization is always of a constant size (1 bytes).

| Offset (bytes) | Length (bytes) | Value                      |
|----------------|----------------|----------------------------|
| 0              |  1             | byte value                 |

### 1.9. ByteArray

ByteArray is an array of bytes.

ByteArray serialization is of a variable size.

| Offset (bytes) | Length (bytes) | Value                      |
|----------------|----------------|----------------------------|
| 0              |  4             | *len* = number of bytes    |
| 4              |  *len*         | bytes                      |

### 1.10. String

String serialization is of variable size.

| Offset (bytes) | Length (bytes) | Value                        |
|----------------|----------------|------------------------------|
| 0              |  4             | *len* = string size in bytes |
| 4              |  *len*         | UTF-8 encoded string         |

### 1.11. UUID

UUID serialization is always of a constant size (16 bytes)

| Offset (bytes) | Length (bytes) | Value                        |
|----------------|----------------|------------------------------|
| 0              |  8             | most significant bytes       |
| 8              |  8             | least significant bytes      |

### 1.12. Timestamp

Timestamp serialization is always of a constant size (8 bytes)

| Offset (bytes) | Length (bytes) | Value                        |
|----------------|----------------|------------------------------|
| 0              |  8             | number of milliseconds since January 1, 1970, 00:00:00 GMT |

### 1.13. List

List serialization is of variable size.

| Offset (bytes) | Length (bytes)           | Value                           |
|----------------|--------------------------|---------------------------------|
| 0              |  4                       | *len* = number of list elements |
| 4              |  *sum(len(serialize_N))* | serialize_N(T) byte array       |

### 1.14. Optional

Optional serialization is of variable size.

| Offset (bytes) | Length (bytes)           | Value                           |
|----------------|--------------------------|---------------------------------|
| 0              |  1                       | 0, if the value is not present, 1 otherwise |
| 1              |  ?                       | For Optional[T], binary encoding of T |

### 1.15. Enum

Enum serialization is always of a constant size (4 bytes).

| Offset (bytes) | Length (bytes) | Value                      |
|----------------|----------------|----------------------------|
| 0              |  4             | ordinal (integer) value    |
