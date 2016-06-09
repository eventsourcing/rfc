---
domain: rfc.eventsourcing.com
shortname: 4/YLR
name: YAML Layout Representation
status: raw
editor: Yurii Rashkovskii <yrashk@gmail.com>
---

YAML Layout Representation is a YAML structure for representing [1/ELF](../1/README.md) layouts.

## License

Copyright (c) 2016 Yurii Rashkovskii

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version.

This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program; if not, see [http://www.gnu.org/licenses](http://www.gnu.org/licenses).

## Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## 0. Goals

To provide a concise, human-readable, machine-parseable universal representation of layouts to be understood and re-used across projects.

This specification is aiming compatibility with [YAML 1.1](http://www.yaml.org/spec/1.1)

## 1. Format

YAML representation of a layout MUST consist of a single-property object definition. The key is layout name and the value is a list that:

* MUST have its first element as a hexadecimal representation of the layout's fingerprint
* MAY contain one or more *property entries*

A *property entry* is a single-property object with the property name as a key and its type's fingerprint as a value. The fingerprint MUST be
either a string, or a hexadecimal representation (`0x...`). It is RECOMMENDED
to only use the hexadecimal representation for fingerprints that are not human-readable.

For example, a `NameChanged` layout from [3/CEP](../3/README.md) can be represented as:

```yaml
"rfc.eventsourcing.com/spec:3/CEP/#NameChanged":
  - 0x7ca89be31d0b990b23c7e2f0b58f7dfa305932a3
  - reference: UUID
  - name: String
```

The order of property entries is unimportant as lexicographical sorting for the purpose of hashing is done transparently to the user.
