# Architecture Constraints

- **Regulatory.** Rider identity and payment data are personal data under GDPR. Trip history is retained for 24 months for dispute handling, then anonymized.
- **Hardware.** Dock controllers are third-party devices that speak a vendor MQTT topic per station. We do not own that firmware.
- **Payments.** The city contract requires the existing payment provider (card + Apple/Google Pay). No second PSP in the first release.
- **Team.** One small backend team. Prefer one primary datastore and boring operational choices over a microservice mesh.
- **Budget.** Single cloud region for the first year. Multi-region is out of scope.
