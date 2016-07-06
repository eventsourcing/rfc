---
domain: rfc.eventsourcing.com
shortname: 9/RIG
name: Repository Interfaces and Guarantees
status: draft
editor: Yurii Rashkovskii <yrashk@gmail.com>
---

Repository Interface and Guarantees describes interfaces and guarantees a *repository* provides. As a part of this description, it describes the overall
composition and flow of data in a repository.

## License

Copyright (c) 2016 The Editor and Contributors

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version.

This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program; if not, see [http://www.gnu.org/licenses](http://www.gnu.org/licenses).

## Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## 0. Goals

* To scope repository functionality
* To present the flow of data inside a repository
* To establish explicit guarantees a repository provides
* To provide a guideline for independent repository implementations

## 1. Timestamp <a name="timestamp"></a>

A repository MUST have a single [hybrid logical clock](http://rfc.eventsourcing.com/spec:6/HLC) *timestamp* (*T<sub>R</sub>*) associated with it. It indicates the upper temporal boundary of the all applied *write* operations performed on the repository.

When repository is started for the first time, T<sub>R</sub> MUST be initialized with current physical time.

A repository MUST monotonously increase T<sub>R</sub>, which MUST be equal or greater than the timestamp of the latest write operation. A repository MUST NOT modify T<sub>R</sub> unless an operation has succeeded.

At any time, when T<sub>R</sub> is updated, it MUST be serialized to a durable storage. Upon a subsequent restart, T<sub>R</sub> MUST be initialized from that durable storage.

## 2. Locking Provider <a name="locking-provider"></a>

Locking provider (*LP*) is a simple interface intended to provide a [mutex](https://en.wikipedia.org/wiki/Mutual_exclusion) primitive. Superficially, it can be expressed as a function *Lock(Identifier)* returning an *Unlock()* closure.

This functionality enables applications to establish write constraints and ensure the consistency of the event domain model.

A repository MUST provide at least one LP implementation and MAY choose an appropriately fitting implementation when an LP is requested or required.

## 3. Command <a name="command"></a>

For the purpose of this specification, we define command *C* as a composition of:

1. an [RFC1/ELF command entity](http://rfc.eventsourcing.com/spec:1/ELF) *T*
1. a function *E(C, Repository, LP)* that returns a stream of events *E<sub>K</sub>*
1. a function *R\(C\)* that returns an arbitrary value (a *result function*)

## 4. Publishing <a name="publishing"></a>

Publishing is a publicly interfaced *write* operation handled by a repository.

It MUST have an input command *C* and MUST synchronously and/or asynchronously return the value of *R\(C\)* upon completion.

### 4.1. Command Transactional Boundaries

Command processing MUST NOT make any changes visible to other users of the repository until command C is committed, forming a transaction *Tx<sub>C</sub>*.

### 4.2. Command Timestamp

If an input command C doesn't have a timestamp, T<sub>R</sub>' = T<sub>R</sub> is updated with current time and is also assigned as a timestamp of the command (*T<sub>C</sub>*). Otherwise, T<sub>R</sub>' = T<sub>R</sub> is updated with T<sub>C</sub> to ensure updated T<sub>R</sub>' is greater than T<sub>C</sub>.

Repository MUST NOT assign T<sub>R</sub>' value to T<sub>R</sub> at this time.

### 4.3. Event Collection

For every event E<sub>k</sub>, repository MUST perform the following operations:

1. If E<sub>k</sub> does not have a timestamp, T<sub>R</sub>' is updated with current time and is also assigned as a timestamp of the event (*T<sub>E</sub>*). Otherwise T<sub>R</sub>' is updated with T<sub>E</sub> to ensure updated T<sub>R</sub>' is greater than T<sub>E</sub>..
1. Record E<sub>k</sub> and associate it with command C.
1. Index E<sub>k</sub> (see [5. Indexing and Querying](../9/README.md#indexing-and-querying)).
1. Check if there any parties interested in this event should Tx<sub>C</sub> successfuly commit (*entity subscribers*). If there are any, E<sub>k</sub>  should be associated with these matching entity subscribers.
1. Record a corresponding `EventCausalityEstablished` event establishing a causal relationship between command C and event E<sub><k/sub>

Layout name: `rfc.eventsourcing.com/spec:9/RIG/#EventCausalityEstablished`

| Type | Property |
|------|----------|
| UUID | event    |
| UUID | command  |

Repository MAY record [EntityLayoutIntroduced](../9/README.md#EntityLayoutIntroduced) when it first encounters a new layout.

<a name="CommandTerminatedExceptionally"></a> If at any time during event collection, an exception is raised (or an equivalent of such), repository MUST remove all recorded events and replace it with a `CommandTerminatedExceptionally` event:

* Layout name: `rfc.eventsourcing.com/spec:9/RIG/#CommandTerminatedExceptionally`

| Type      | Property       |
|-----------|----------------|


The relationship between `CommandTerminatedExceptionally` and the command
is established through a matching `EventCausalityEstablished` event.

Repository MAY add arbitrary additional informational events referring to the instance of this `CommandTerminatedExceptionally` event to capture details of the failure. For human-readable descriptions, it is RECOMMENDED to use [rfc.eventsourcing.com/spec:3/CEP/#DescriptionChanged](http://rfc.eventsourcing.com/spec:3/CEP/#DescriptionChanged).

### 4.4. Command Colleciton

Repository MUST perform the following operations:

1. Record command C.
1. Index command C (see [5. Indexing and Querying](../9/README.md#indexing-and-querying)).
1. Check if there any parties interested in this command should Tx<sub>C</sub> successfuly commit (*entity subscribers*). If there are any, C should be associated with these matching entity subscribers.

### 4.5. Commit

All changes made within Tx<sub>C</sub> MUST be made available to all other users of the repository.

T<sub>R</sub> MUST be assigned to T<sub>R</sub>' (last event's timestamp or command's timestamp if it produced an empty stream E<sub>K</sub>).

All recorded matches between entity subscribers and entity recorded within Tx<sub>C</sub>, entity subscribers MUST receive matched entities according to their expectations.

## 5. Indexing and Querying <a name="indexing-and-querying"></a>

Repository MUST provide an interface for querying indexed entities. No universal standard is defined at the moment and independent implementations SHOULD seek to provide their own indexing and querying mechanisms.

Should such a standard emerge, implementations MUST adhere to it once it reaches *Stable* level of maturity.
