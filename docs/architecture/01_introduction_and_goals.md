# Introduction and Goals

CityCycle lets residents and visitors of Riverside rent a bike from a docked station, ride it, and return it to any CityCycle station. The first release covers mechanical bikes only. Electric bikes arrive later (see [RFC 0001](../rfc/0001-add-electric-bikes.md)).

## Requirements Overview

- A rider finds a nearby station with available bikes, unlocks one with the mobile app, rides, and docks it at any station with a free slot.
- Billing is per trip, charged at the end of the ride through a payment provider.
- Operators see station fill levels and receive alerts when a station is nearly empty or nearly full, so they can redistribute bikes.
- Support staff can look up a trip and refund or close it when something goes wrong.

Out of scope for the first release: free-floating bikes, reservations more than a few minutes ahead, multi-city roaming, and operator white-labeling.

## Quality Goals

| Priority | Quality goal | Scenario |
|----------|--------------|----------|
| 1 | Availability of unlock | A rider who is next to a station with a free bike can unlock it in under 3 seconds at the 95th percentile during weekday morning peaks. |
| 2 | Station data freshness | The map of nearby stations shows bike and slot counts that are at most 30 seconds behind the last dock or unlock event. |
| 3 | Operability for redistribution | An operator can open a live view of all stations and filter stations that need restocking within one screen load (under 2 seconds for the city-wide list). |

Integrity of billing (no lost trips, no double charges) is a hard constraint, not a quality goal we trade away. It appears under [architecture constraints](02_architecture_constraints.md).

## Stakeholders

| Role/Name | Contact | Expectations |
|-----------|---------|--------------|
| City transport office | product owner | Reliable service during peaks; clear reporting of usage |
| Riders | end users | Fast unlock, trustworthy map of free bikes |
| Fleet operators | operations team | Live station fill levels and alerts |
| Support | customer care | Trip lookup and refund without engineering help |
| Engineering | CityCycle team | A system that can add electric bikes without a rewrite |
