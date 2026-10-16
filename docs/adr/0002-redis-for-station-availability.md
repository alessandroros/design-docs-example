---
status: accepted
date: 2026-03-12
decision-makers: CityCycle engineering
---

# Redis for station availability

## Context and Problem Statement

Unlock latency and map freshness are the top two quality goals. Reading free-bike counts from PostgreSQL on every map tile and every unlock attempt will not meet the 3 s unlock and 30 s freshness scenarios under morning peak. How do we serve station availability?

## Decision Drivers

* Unlock p95 under 3 seconds
* Map counts at most 30 seconds stale
* Redis must not become the billing ledger
* Operational simplicity for a small team

## Considered Options

* Redis as availability cache and short-lived unlock lock, PostgreSQL remains source of truth
* Materialized view / read replica in PostgreSQL only
* In-process cache per API replica
* Separate "availability service" with its own store

## Decision Outcome

Chosen option: "Redis as availability cache and short-lived unlock lock, PostgreSQL remains source of truth", because it meets the latency and freshness goals without moving trip integrity out of PostgreSQL.

### Consequences

* Good, because unlock can reserve a bike with a TTL lock before talking to the dock
* Good, because the operator city-wide list reads the same snapshot
* Bad, because we must define a clear fallback when Redis is down
* Bad, because cache invalidation bugs can show a bike that is already gone (mitigated by the lock + dock ack)

### Confirmation

Load test the morning-peak unlock scenario against staging with Redis. Chaos-test Redis downtime and confirm unlock still succeeds via PostgreSQL fallback with a degraded map banner.

## Pros and Cons of the Options

### Redis cache + lock (chosen)

* Good, because low-latency reads and atomic reserve
* Good, because well understood managed offering
* Bad, because another moving part in production

### PostgreSQL read replica / materialized view only

* Good, because one database technology
* Bad, because unlock locking and sub-second map fan-out are a poor fit
* Bad, because peak read load lands on the database we also need for billing

### In-process cache per replica

* Good, because no extra infrastructure
* Bad, because replicas disagree; unlock races across instances
* Bad, because deploys flush the cache and stampede PostgreSQL

### Separate availability service

* Good, because a clean boundary
* Bad, because operational cost is too high for the current team
* Bad, because it can wait until a real scaling need appears

## More Information

Unlock flow: [runtime view](../architecture/06_runtime_view.md). Quality scenarios: [section 10](../architecture/10_quality_requirements.md).
