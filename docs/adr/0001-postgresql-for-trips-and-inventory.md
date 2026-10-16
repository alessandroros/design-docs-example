---
status: accepted
date: 2026-03-10
decision-makers: CityCycle engineering
---

# PostgreSQL for trips and inventory

## Context and Problem Statement

CityCycle needs a system of record for stations, bikes, trips, and payments. The data is relational (a trip belongs to a bike, a station, and a rider). Billing integrity is a hard constraint. Which primary database should we use?

## Decision Drivers

* Strong consistency for trip state transitions
* Familiarity of the team
* Managed offering in our cloud with point-in-time recovery
* Enough write throughput for Riverside (low thousands of trips per day at launch)

## Considered Options

* PostgreSQL (managed)
* MySQL (managed)
* Document store (e.g. MongoDB) as primary
* Event store as primary with projections

## Decision Outcome

Chosen option: "PostgreSQL (managed)", because trip and inventory data are naturally relational, the team already operates Postgres, and a managed multi-AZ deployment meets the integrity constraint without new operational skills.

### Consequences

* Good, because transactions cover unlock → trip insert → inventory update in one place when we need them
* Good, because reporting and support trip lookup are ordinary SQL
* Bad, because the unlock hot path must not wait on heavy queries; we still need a separate availability cache ([ADR 0002](0002-redis-for-station-availability.md))
* Bad, because schema migrations need discipline as the product grows

## Pros and Cons of the Options

### PostgreSQL (managed)

* Good, because relational model matches trips and inventory
* Good, because team skill and tooling already exist
* Bad, because horizontal write scaling is not trivial later

### MySQL (managed)

* Good, because also relational and managed
* Neutral, because no clear advantage over Postgres for this team
* Bad, because the team would learn a second operational dialect for no gain

### Document store as primary

* Good, because flexible documents for evolving bike attributes
* Bad, because billing integrity across trip and payment records is harder
* Bad, because station inventory queries become awkward

### Event store as primary

* Good, because unlock and dock events are naturally a stream
* Bad, because the team size does not justify the complexity for v1
* Bad, because support still needs a current-state view we would have to build

## More Information

Related: [ADR 0002](0002-redis-for-station-availability.md) for the hot-path cache. Electric-bike telemetry must not land in this database as high-frequency samples; see [ADR 0003](0003-battery-telemetry-separate-from-trip-store.md).
