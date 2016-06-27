---
domain: rfc.eventsourcing.com
shortname: 4/JLER
name: JSON Layout and Entity Representation
status: raw
editor: Yurii Rashkovskii <yrashk@gmail.com>
---

JSON Layout and Entity Representation is a JSON structure for representing [1/ELF](../1/README.md) layouts.

See also: [5/YES](../5/README.md)

## License

Copyright (c) 2016 Yurii Rashkovskii

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version.

This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program; if not, see [http://www.gnu.org/licenses](http://www.gnu.org/licenses).

## Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## 0. Goals

To provide a concise, human-readable, machine-parseable universal representation of layouts to be understood and re-used across projects.

This specification is aiming compatibility with [ECMA 404](http://www.ecma-international.org/publications/files/ECMA-ST/ECMA-404.pdf)

## 1. Layout Format

JSON representation of a layout MUST consist of a single-property object definition. The key is layout fingerprint (binary) and the value MUST be a list that:

* MUST have its first element as a layout name
* MAY contain one or more *property entries*

A *property entry* is a single-property object with the property name as a key and its type's fingerprint as a value. The fingerprint MUST be
either a string, or a hexadecimal representation (`0x...`). It is RECOMMENDED
to only use the hexadecimal representation for fingerprints that are not human-readable.

For example, a `NameChanged` layout from [3/CEP](../3/README.md) can be represented as:

```json
{
  "fKib4x0LmQsjx+LwtY99+jBZMqM=": [
    "rfc.eventsourcing.com/spec:3/CEP/#NameChanged",
    {
      "name": "String"
    },
    {
      "reference": "UUID"
    },
    {
      "timestamp": "<rfc.eventsourcing.com/spec:6/HLC/#Timestamp fingerprint>"
    }
  ]
}
```

The order of property entries is unimportant as lexicographical sorting for the purpose of hashing is done transparently to the user.

## 2. Entity Format

Entity is a layout instance associated with a UUID. This UUID is not represented by any layout individual property. Following the lexicographical order of properties of the layout, an individual entity MUST be serialized as a single-property object where the key is entity's UUID and the value MUST be a list that:

* MUST have its first element as a binary layout fingerprint
* MUST contain the same number of *property values* as the number of *property entries* in the layout, in the same order

For example, an instance of a `NameChanged` entity from [3/CEP](../3/README.md) can be represented as:

```json
{
  "4782a2cc-365f-4ec5-9ba4-4523744ffc1f": [
    "fKib4x0LmQsjx+LwtY99+jBZMqM=",
    "John Doe",
    "27cb36ac-ef48-47ff-b565-a263c4140aa8",
    "15783086287502613943.0"
  ]
}
```

Multiple entities MAY be combined into a multiple-properties object when the order of these entities is not significant.
