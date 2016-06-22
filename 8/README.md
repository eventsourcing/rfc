---
domain: rfc.eventsourcing.com
shortname: 8/EMT
name: Entity Migration and Transformation
status: raw
editor: Yurii Rashkovskii <yrashk@gmail.com>
---

Entity Migration and Transformation is a set of commands, events, protocols and other contracts to be used to enable migration and transformation of recorded entities.

## License

Copyright (c) 2016 Yurii Rashkovskii

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version.

This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program; if not, see [http://www.gnu.org/licenses](http://www.gnu.org/licenses).

## Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## 0. Goals

Once entity is recorded, it can't be modified. However, since entity (command, event) layouts evolve over time (more information becomes available, etc.) there needs to be a mechanism that will describe the evolution of the layouts.

The goals of this specification are:

* To provide a shared, universal language for entities migration and
  transformation.
* To keep this language as small as possible.
* To facilitate the development of libraries that implement this shared
  language.
* To enable standardized transformation of entities so that up-to-date
  queries can be issued against them.
* To enable seamless integration with the language defined in
  [3/CEP](../3/README.md).

## 1. Migration

### 1.1. Events

#### 1.1.1. EntityLayoutIntroduced <a name="EntityLayoutIntroduced"></a>

* Layout name: `rfc.eventsourcing.com/spec:8/EMT/#EntityLayoutIntroduced`

| Type                                               | Property       |
|----------------------------------------------------|----------------|
| ByteArray                                          | fingerprint    |
| Optional[[Layout](http://rfc.eventsourcing.com/spec:7/LDL/#Layout)] | layout |

This event describes an introduction of an entity layout. Fingerprint MUST be recorded in `fingerprint`.

The new layout itself MAY be recorded in `layout`.


#### 1.1.2. EntityLayoutReplaced <a name="EntityLayoutReplaced"></a>

* Layout name: `rfc.eventsourcing.com/spec:8/EMT/#EntityLayoutReplaced`

| Type      | Property       |
|-----------|----------------|
| ByteArray | fingerprint    |
| UUID      | replacement    |

This event describes a replacement of an entity layout with a new one. Old
layout's fingerprint MUST be recorded in `fingerprint`, and a corresponding
`EntityLayoutIntroduced`'s UUID MUST be recorded in `replacement`.

## 2. Transformation

### 2.1. Events

When event replacement happens, event->command causality is lost and for some types of applications it means information loss. In order to mitigate this, [EventCausalityEstablished](http://rfc.eventsourcing.com/spec:9/RIG/#EventCausalityEstablished) signals an establishment of a new causality relationship for an event.

### 2.2. Commands

In some cases, command transformation can be also beneficial (for example, when causal information contained in commands can be taken into an account). However, a command can't produce new commands.

It is RECOMMENDED to run a process that queries the journal for commands of a specific type and issues individual "matching" replacement commands for each command to be replaced with new events.
