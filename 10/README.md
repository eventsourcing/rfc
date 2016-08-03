---
domain: rfc.eventsourcing.com
shortname: 10/LIBRE
name: Lazy Introduction By Referencing Entity
status: raw
editor: Yurii Rashkovskii <yrashk@gmail.com>
---

Lazy Introduction By Referencing Entity describes a lightweight method to introducing domain models.

## License

Copyright (c) 2016 The Editor and Contributors

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version.

This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program; if not, see [http://www.gnu.org/licenses](http://www.gnu.org/licenses).

## Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## 0. Goals

The goal of this specification is to help avoiding the creation of synthetic
entities for the purpose of anchoring domain model unique identifiers.

## 1. Rationale

There are cases where an application architect might feel compelled to create
an entity that is used as a reference point for other events. While this in itself is not a problem, it pins the domain model early and sometimes in a suboptimal way.

A common example of such case would be an introduction of a `UserCreated` event
followed by other events to set user's credentials, profile information, etc. This event pins down a domain model of `User` early, even if such model is not necessarily required in the application. This is also a very synthetic event
because no user "creation" is actually taking place.

Even if the design was to be improved and a more accurate model was to be used (for example, an identity), it would still require a synthetic event of some kind (for example, `IdentityEstablished`). While in some cases this the event that takes place (and then this event is what should be recorded), in a lot of cases no actual events take place.

## 2. Method

Lazy introduction method is very simple in its nature. Instead of introducing
a synthetic event, the convention is that the model in question is introduced
by referencing its unique identifier in another entity for the first time. Such identifier MUST NOT resolve to any known entity.

### 2.1. Example

For example, if at a certain point in time, a person's identity becomes known to the system (for example, through a third-party authentication), the following process MUST happen:

1. A unique identifier I is generated
1. `AuthenticationTokenAcquired(reference=I, token=...)` event is produced
1. `NameChanged(reference=I, name=...)` event is produced
1. `EmailChanged(reference=I, email=...)` event is produced

## 3. Benefits

The most important benefit of this method is the reduction or removal of synthetic event that haven't actually happened.

The other benefit is that this method is that it delays the necessity to pin down the nomenclature of the domain model. As a consequence, it also allows for plurality of the domain model, where one unique identifier can serve as a unique
identifier for different domain models (considering an example in 2.1., identifier I can be used as identifier for a `Person` as well as a `Recipient`, because of the use of the `EmailChanged` event)

## 4. Downsides

It is more difficult to establish the fact of a "model" presence in a generic way as all known referencing indices have to be checked for a matching identifier.

Abuse of model's nature fluidity can lead to an application design that is hard to comprehend. Use of machine-readable source code or runtime object annotations
or code analysis tooling of some kind is RECOMMENDED for keeping track of events that are understood by a particular model implementation.
