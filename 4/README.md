![raw](http://rfc.unprotocols.org/spec:2/COSS/raw.svg)

YAML Layout Representation is a YAML structure for representing [1/ELF](,./1/README.md) layouts.

* Status: raw
* Editor: Yurii Rashkovskii <yrashk@gmail.com>

## License

Copyright (c) 2016 Yurii Rashkovskii

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version.

This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program; if not, see [http://www.gnu.org/licenses](http://www.gnu.org/licenses).

## Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## 0. Goals

To provide a concise, human-readable, machine-parseable universal representation of layouts to be understood and re-used across projects.

## 1. Format

YAML representation of a layout consists of a single object definition and zero or more property objects. A property object is an object that contains one entry with the property name as a key and type's fingerprint. The fingerprint MUST be
either a string, or a hexadecimal representation (`0x...`). It is RECOMMENDED
to only use the hexadecimal representation for fingerprints that are not human-readable.

For example, a `NameChanged` layout from [3/CEP](../3/README.md) can be represented as:

```yaml
"rfc.eventsourcing.com/spec:3/CEP/#NameChanged":
  - reference: UUID
  - name: 0x537472696e67 # hexadecimal version of the `String` fingerprint
```

The order of properties is unimportant as lexicographical sorting for the purpose of hashing is done transparently to the user.
