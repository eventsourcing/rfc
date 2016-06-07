---
domain: rfc.eventsourcing.com
shortname: 3/CEP
name: Common Entities and Protocols
status: raw
editor: Yurii Rashkovskii <yrashk@gmail.com>
---

Common Entities and Protocols is a set of most common events and protocols to be shared across interoperable applications.

## License

Copyright (c) 2016 Yurii Rashkovskii

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version.

This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program; if not, see [http://www.gnu.org/licenses](http://www.gnu.org/licenses).

## Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## 0. Goals

There is a number of common protocols that can be shared by most of applications.

The goals of this specification are:

* To provide a shared language for those common protocols.
* To keep this language as small as possible, as the concepts behind event
  sourcing allow for extension without having o maintain any kind of "global schema" to accommodate different needs.
* To facilitate the development of shared libraries that implement this shared
  language.

## A. Conventions

1. An event that signifies a creation or an introduction of something (for example, `UserCreated`, or `TransactionImported`) is used as a target for polymorphic references (its ID will be referenced).

## 1. Name

Naming is one of a very common concepts across all types of applications. Making
name designation a universal, shared concept allows interoperating libraries and applications to deduce the naming of any model instance.

### 1.1. Events

#### 1.1.1. NameChanged <a name="NameChanged"></a>

This event signifies the name change for a referenced instance.

* Layout name: `rfc.eventsourcing.com/spec:3/CEP/#NameChanged`

| Type   | Property   |
|--------|------------|
| UUID   | reference  |
| String | name       |

### 1.2. Protocol

This protocol allows to deduce a "final" instance name.

| Type   | Property | Query                                      |
|--------|----------|--------------------------------------------|
| String | name     | Latest `NameChanged` with `reference = ID` |


## 2. Description

Just like naming, description is a common concept that allows to provide
a more in-depth explanation of a model instance.

### 2.1. Events

#### 2.1.1. DescriptionChanged <a name="DescriptionChanged"></a>

This event signifies the description change for a referenced instance.

* Layout name: `rfc.eventsourcing.com/spec:3/CEP/#DescriptionChanged`

| Type   | Property    |
|--------|-------------|
| UUID   | reference   |
| String | description |

### 2.2. Protocol

This protocol allows to deduce a "final" instance description.

| Type   | Property    | Query                                             |
|--------|-------------|---------------------------------------------------|
| String | description | Latest `DescriptionChanged` with `reference = ID` |

## 4. Deletion

Model instance deletion is a concept that allows to mark certain instances
deleted. By sharing this language, libraries and applications can delete
instances of models they are not explicitly familiar with

### 4.1. Events

#### 4.1.1. Deleted <a name="Deleted"></a>

Signifies deletion of an instance.

* Layout name: `rfc.eventsourcing.com/spec:3/CEP/#Deleted`

| Type   | Property    |
|--------|-------------|
| UUID   | reference   |

#### 4.1.2. Undeleted <a name="Undeleted"></a>

Signifies a reversal of deletion of an instance. **NB**: instead of referring
to an original instance, it refers to a `Deleted` event. This simplifies querying for this event.

* Layout name: `rfc.eventsourcing.com/spec:3/CEP/#Undeleted`

| Type   | Property    |
|--------|-------------|
| UUID   | deleted     |

### 4.2. Protocol

This protocol allows to determine the deletion status of an instance.

| Type              | Property    | Query                                             |
|-------------------|-------------|---------------------------------------------------|
| Optional[Deleted] | deleted     | `Deleted` with `reference = ID` that is not followed by `Undeleted` referring to it |
