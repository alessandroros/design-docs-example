---
status: accepted
date: 2026-06-18
decision-makers: CityCycle engineering
consulted: City transport office (retention), fleet operations
---

# Battery telemetry separate from the trip store

## Context and Problem Statement

[RFC 0001](../rfc/0001-add-electric-bikes.md) adds electric bikes that emit battery samples as often as every 10 seconds. Where do those samples live? PostgreSQL already holds trips and payments and must stay reliable for billing.

## Decision Drivers

* Protect trip-store write latency and size
* Keep latest battery percent available for unlock and map (≤ 30 s freshness still applies)
* Give operators enough history to plan depot charging
* Stay within the small team's operational budget

## Considered Options

* All samples in PostgreSQL (same database as trips)
* Latest value only on the `bike` row; no sample history
* Separate managed time-series store for samples; latest value denormalized on `bike` for unlock/map
* Object storage files of raw samples, queried ad hoc

## Decision Outcome

Chosen option: "Separate managed time-series store for samples; latest value denormalized on `bike` for unlock/map", because it keeps high-frequency writes off the billing database while still serving the unlock check and operator history.

### Consequences

* Good, because PostgreSQL write rate stays tied to trips, not to sample frequency
* Good, because operators can chart battery drain for depot planning
* Bad, because one more data store to provision, secure, and monitor
* Bad, because latest value and history can briefly diverge; unlock trusts the denormalized latest on `bike`, refreshed by the same consumer

### Confirmation

In staging, publish 10 s samples from 200 simulated bikes for one hour. PostgreSQL TPS for trips stays comparable to baseline. Unlock still enforces the 15% rule from the `bike` row.

## Pros and Cons of the Options

### Separate time-series store (chosen)

* Good, because workload isolation
* Good, because retention (90 days) can differ from trip retention (24 months)
* Bad, because new operational surface

### All samples in PostgreSQL

* Good, because one database
* Bad, because sample volume threatens the billing store
* Bad, because trip backups and restores become huge

### Latest value only

* Good, because simplest
* Bad, because the city asked for charging diagnostics over time
* Bad, because debugging "bike died mid-trip" needs history

### Object storage dumps

* Good, because cheap
* Bad, because operator queries and alerts become batch jobs
* Bad, because poor fit for "list bikes under 20% now"

## More Information

Produced by [RFC 0001](../rfc/0001-add-electric-bikes.md). Updates the [container diagram](../architecture/diagrams/c4-containers.md).
