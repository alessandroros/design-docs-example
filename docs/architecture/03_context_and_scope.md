# Context and Scope

See the [context diagram](diagrams/c4-context.md).

## Business Context

CityCycle is the city’s shared bike layer. Riders use it through the CityCycle app. Fleet operators redistribute bikes between stations. The city transport office owns the product. Dock hardware and payments stay with external providers.

| Neighbor | Direction | Meaning |
|----------|-----------|---------|
| Rider | in | Unlocks, rides, pays |
| Fleet operator | in | Watches stations, plans redistribution |
| Support agent | in | Looks up and closes trips |
| Dock controller network | bi | Lock and unlock commands; dock and undock events |
| Payment provider | out | Authorize and capture trip charges |
| Push notification service | out | Ride receipts and operator alerts |

## Technical Context

| Interface | Protocol | Notes |
|-----------|----------|-------|
| Mobile app → API | HTTPS / JSON | Session token from the identity provider |
| Operator console → API | HTTPS / JSON | Role-based access for operators and support |
| API → dock network | MQTT | Per-station topics; vendor-defined payload |
| API → payment provider | HTTPS | Provider SDK / REST |
| API → push service | HTTPS | Provider REST |

## Scope

**In scope for the first release:** docked mechanical bikes, unlock and return, per-trip billing, station map, operator fill-level view, support trip lookup.

**Out of scope:** electric bikes (planned; see [RFC 0001](../rfc/0001-add-electric-bikes.md)), free-floating, multi-city, advanced dynamic pricing.
