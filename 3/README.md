Common Entities and Protocols is a set of most common events and protocols to be shared across interoperable applications.

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

### 1.1. Events

#### 1.1.1. NameChanged

* Layout name: `rfc.eventsourcing.com/spec:3/CEP/#NameChanged`

| Type   | Property   |
|--------|------------|
| UUID   | reference  |
| String | name       |

### 1.2. Protocol

| Type   | Property | Query                                      |
|--------|----------|--------------------------------------------|
| String | name     | Latest `NameChanged` with `reference = ID` |


## 2. Description

### 2.1. Events

#### 2.1.1. DescriptionChanged

* Layout name: `rfc.eventsourcing.com/spec:3/CEP/#DescriptionChanged`

| Type   | Property    |
|--------|-------------|
| UUID   | reference   |
| String | description |

### 2.2. Protocol

| Type   | Property    | Query                                             |
|--------|-------------|---------------------------------------------------|
| String | description | Latest `DescriptionChanged` with `reference = ID` |

## 3. Deletion

### 3.1. Events

#### 3.1.1. Deleted

* Layout name: `rfc.eventsourcing.com/spec:3/CEP/#Deleted`

| Type   | Property    |
|--------|-------------|
| UUID   | reference   |

#### 3.1.2. Undeleted

* Layout name: `rfc.eventsourcing.com/spec:3/CEP/#Undeleted`

| Type   | Property    |
|--------|-------------|
| UUID   | reference   |

### 3.2. Protocol

| Type    | Property    | Query                                             |
|---------|-------------|---------------------------------------------------|
| boolean | isDeleted   | Is there a `Deleted` with `reference = ID` not followed by `Undeleted` with the same reference, but larger `timestamp` |
| Long    | deletedAt   | `Deleted` with `reference = ID` not followed by `Undeleted` with the same reference, but larger `timestamp`, extract `Deleted#timestamp` |
