# Cross-cutting Concepts

## Authentication and roles

Riders, operators, and support use the same identity provider with different roles. The API enforces role checks on every mutating endpoint. Dock devices authenticate to MQTT with per-station credentials issued at install time.

## Idempotency of unlock

`POST /trips/unlock` accepts an `Idempotency-Key` from the app. Retries after a flaky mobile network must not create a second trip or a second MQTT unlock for the same key within the lock TTL.

## Observability

Every unlock and return carries a `trip_id` through logs, MQTT correlation, and payment calls. The SLO dashboards watch unlock latency p95, Redis hit rate for the station map, and `pending_payment` age.
