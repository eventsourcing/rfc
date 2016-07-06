---
domain: rfc.eventsourcing.com
shortname: 7/LDL
name: Layout Describing Layout
status: draft
editor: Yurii Rashkovskii <yrashk@gmail.com>
---

Layout Describing Layout is a layout used to describe layouts.

See also: [1/ELF](../1/README.md), section 3.

## License

Copyright (c) 2016 Yurii Rashkovskii

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version.

This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program; if not, see [http://www.gnu.org/licenses](http://www.gnu.org/licenses).

## Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## 0. Goals

The goals of this specification is to provide a reusable way to describe a layout using a layout. This is useful for storing metainformation for layouts.

## 1. Property <a name="Property"></a>

* Layout name: `rfc.eventsourcing.com/spec:7/LDL/#Property`

| Type      | Property       |
|-----------|----------------|
| String    | name           |
| ByteArray | fingerprint    |

This layout describes sufficient information about property's name and type.

## 2. Layout <a name="Layout"></a>

* Layout name: `rfc.eventsourcing.com/spec:7/LDL/#Layout`

| Type           | Property       |
|----------------|----------------|
| String         | name           |
| List[Property] | properties     |

The lexicographical order of properties is not significant as it is enforced in the software.
