---
domain: rfc.eventsourcing.com
shortname: 5/YES
name: YAML Entity Serialization
status: raw
editor: Yurii Rashkovskii <yrashk@gmail.com>
---

YAML Entity Serialization is a data storage and exchange serialization format used in Eventsourcing and other projects.

See also: [2/BES](../2/README.md), [1/ELF](../1/README.md)

## License

Copyright (c) 2016 Yurii Rashkovskii

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version.

This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program; if not, see [http://www.gnu.org/licenses](http://www.gnu.org/licenses).

## Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## 0. Goals

* To provide a human-readable format for Eventsourcing entities
* To create a format that will have no or very little changes due to simpler requirements
* To ensure there is a format that can be used to ease cross-version data portability
* To be compatible with [RFC1/ELF](../1/README.md)

## 1. YAML standard

This specification is aiming compatibility with [YAML 1.1](http://www.yaml.org/spec/1.1)

## 2. Data Types

### 2.1. Boolean

Serialized as a string `"true"` or `"false"`

### 2.2. Short

Serialized as a signed integer number, –32768..32767

### 2.3. Integer

Serialized as a signed integer number, –2147483648..2147483647

### 2.4. Long

Serialized as a signed integer number, –9223372036854775808 to 9223372036854775807

### 2.5. BigDecimal

Serialized as a floating point number, no limits.

### 2.6. Float

Serialized as a floating point number, 3.4E +/- 38 (7 digits)

### 2.7. Double

Serialized as a floating point number, 1.7E +/- 308 (15 digits)

### 2.8. Byte

Serialized as an usigned integer number, 0..255

### 2.9. ByteArray

Serialized as a base64 string

### 2.10. String

Serialized as a string

### 2.11. UUID

Serialized as a canonical UUID string representation, xxxxxxxx-xxxx-Mxxx-Nxxx-xxxxxxxxxxxx

### 2.12. Timestamp

Serialized as a number of milliseconds since January 1, 1970, 00:00:00 GMT

### 2.13. List

Serialized as YAML list

### 2.14. Optional

Serialized as an empty map for non-present values:

```yaml
{}
```

Serialized as a map with `present` key for present values:

```yaml
{present: <value>}
```

### 2.15. Enum

Serialized as a string value associated with the ordinal value. If
string value is absent, the ordinal value integer is used.
