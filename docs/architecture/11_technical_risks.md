# Risks and Technical Debts

| Risk / debt | Impact | Mitigation |
|-------------|--------|------------|
| Vendor MQTT broker outage | No unlock or return events | Contractual SLO; local dock queue if the vendor supports it; status page for riders |
| Redis as a hot path | Unlock latency and map freshness depend on it | Fallback to PostgreSQL; multi-AZ Redis; alert on hit-rate drop |
| Single API deployable | A bad release hits unlock and billing together | Small release train, fast rollback, feature flags for new paths |
| Electric-bike battery data volume | Could bloat the trip database | Addressed by [ADR 0003](../adr/0003-battery-telemetry-separate-from-trip-store.md) |
