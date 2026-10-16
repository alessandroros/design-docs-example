# Quality Requirements

The three driving scenarios are in [section 1](01_introduction_and_goals.md). This page keeps the fuller tree for reviews.

## Quality Requirements Overview

| Quality | Priority | Notes |
|---------|----------|-------|
| Unlock latency | high | p95 < 3 s at morning peak |
| Freshness of station counts | high | ≤ 30 s behind last event |
| Operator list responsiveness | medium | city-wide list < 2 s |
| Billing integrity | constraint | no lost or double-charged trips |
| Privacy | constraint | GDPR retention and access control |

## Quality Scenarios

1. **Peak unlock.** At 08:30 on a weekday, 200 concurrent unlock attempts. 95% complete (dock ack or clear error) within 3 seconds.
2. **Stale map.** After a bike is docked, every client map refresh within 30 seconds shows the new free-bike count for that station.
3. **Redis outage.** Redis is unavailable. Unlock still works via PostgreSQL fallback; latency may exceed the 3 s goal; the map shows a degraded banner. Billing continues.
