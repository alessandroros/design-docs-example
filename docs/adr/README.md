# Architecture decision records

Format: [MADR 4.0.0](https://adr.github.io/madr/). This example uses the full template for decisions with real trade-offs.

## Index

| ID | Title | Status |
|----|-------|--------|
| [0000](0000-use-markdown-architectural-decision-records.md) | Use Markdown Architectural Decision Records | accepted |
| [0001](0001-postgresql-for-trips-and-inventory.md) | PostgreSQL for trips and inventory | accepted |
| [0002](0002-redis-for-station-availability.md) | Redis for station availability | accepted |
| [0003](0003-battery-telemetry-separate-from-trip-store.md) | Battery telemetry separate from the trip store | accepted |

`0001` and `0002` are the foundation of the first release. `0003` is the decision left behind by [RFC 0001](../rfc/0001-add-electric-bikes.md) when electric bikes were added.
