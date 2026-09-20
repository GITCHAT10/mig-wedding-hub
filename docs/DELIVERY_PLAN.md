# WHYNOT delivery and acceptance plan

All milestones below are pending implementation. Documentation completion does not mark a milestone passed.

| Milestone | Work | Required evidence |
| --- | --- | --- |
| 0 — Private development baseline | Resolve GitHub visibility/access; select hosting and identity; create locked dependencies and CI | Verified access list; reproducible build; isolated synthetic staging data |
| 1 — Couple and guest journey | Wedding setup, invitation redemption, per-event RSVP and calendars | Couple creates event; guest responds independently; revoked invitation rejected; no cross-wedding access |
| 2 — Coordinator operations | Tasks, budget versions, proposals, seating and logistics requests | Coordinator completes event plan; concurrent final-seat assignment admits only one guest; booking requests remain pending until acknowledged |
| 3 — Integration | Approved supplier, accommodation and financial event adapters | Retried event produces one downstream effect; conflicting replay held; failed integration visible and recoverable |
| 4 — Payment pilot | Merchant/provider qualification, hosted checkout, signed callbacks, refunds and reconciliation | Forged callbacks rejected; duplicate callbacks harmless; amounts/currency match approved quote; refunds reconcile |
| 5 — Release | Operational UAT, measured load, restore drill, monitoring, support and rollback | Named users finish core tasks; release commit recorded; backup restored; errors routed to responsible staff |

## Initial engineering checks

- Organization, wedding, vendor and household access isolation.
- Invitation expiry, revocation, rate limits and token leakage checks.
- Concurrent RSVP/plus-one and seating-capacity tests.
- Calendar time zones, escaping, updates and cancellation cases.
- Currency precision, approved quote versions and replay conflict handling.
- Outbox restart/retry and downstream reconciliation tests.
- Responsive keyboard-accessible interfaces and clear error recovery.

## Release workflow

Introduce CI after executable packages exist: frozen dependency install, lint/types, unit and database integration tests, build and staging smoke tests. No pretend deployment workflow is supplied for an application that does not exist yet. Deploy staging before production, identify the exact release, and verify rollback/restore procedures before live use.

No new server, cloud account, production integration or live payment is provisioned by this documentation change.
