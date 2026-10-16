# Building Block View

See the [container diagram](diagrams/c4-containers.md).

## Whitebox Overall System

Motivation  
Keep the first release as one deployable backend plus two clients. Split only what the quality goals force: a hot cache for station availability, separate from the trip ledger.

Contained Building Blocks

| Building block | Responsibility | Main interfaces |
|----------------|----------------|-----------------|
| Mobile app | Rider unlock, map, trip history | HTTPS → API |
| Operator console | Station fill view, alerts, trip lookup | HTTPS → API |
| CityCycle API | Domain logic: unlock, return, billing orchestration, inventory | MQTT to docks; Redis; PostgreSQL; payment provider |
| PostgreSQL | System of record for stations, bikes, trips, payments | Used only by the API |
| Redis | Station availability snapshot and short-lived unlock locks | Used only by the API |

Important Interfaces  
- `POST /trips/unlock` — rider unlock; returns when the dock acks or times out.  
- `POST /trips/{id}/return` — dock-confirmed return; starts payment capture.  
- MQTT `station/{id}/events` — dock and undock events from hardware.  
- MQTT `station/{id}/commands` — unlock commands to hardware.

### Mobile app

Purpose: let a rider find a bike, unlock it, and see the open trip.  
Interfaces: CityCycle API over HTTPS.  
Quality: unlock latency is dominated by the API and the dock, not by the app.  
Open issues: offline mode is not supported in v1.

### Operator console

Purpose: show live fill levels and support trip lookup.  
Interfaces: CityCycle API over HTTPS.  
Quality: city-wide list under 2 s (reads Redis via the API).

### CityCycle API

Purpose: all domain behavior.  
Interfaces: HTTPS for clients; MQTT for docks; Redis; PostgreSQL; payment provider.  
Directory: `services/api` (not present in this docs-only example).  
Risks: a single deployable unit means a bad release affects unlock and billing together. Mitigated by a small surface and a fast rollback.

### PostgreSQL

Purpose: durable trips, inventory, and payment records.  
See [ADR 0001](../adr/0001-postgresql-for-trips-and-inventory.md).

### Redis

Purpose: availability counts and unlock locks.  
See [ADR 0002](../adr/0002-redis-for-station-availability.md).  
Risk: if Redis is down, unlock falls back to PostgreSQL with higher latency; the map may show a degraded banner.

## Level 2

Not expanded yet. The API is still one module. Split it when a concrete boundary appears (the battery work in [ADR 0003](../adr/0003-battery-telemetry-separate-from-trip-store.md) is the first candidate).
