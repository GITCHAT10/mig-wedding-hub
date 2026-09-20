# WHYNOT platform blueprint

Status: proposed implementation, not an operational certification.

## Product scope

1. Wedding pages: couple-approved story, gallery, venue and event schedule with explicit public/private content controls.
2. Guest management: household invitations, bounded plus-ones, per-event RSVP, attendance deadlines, seating and restricted dietary/accessibility notes.
3. Planning: tasks, owners, budget revisions, vendor quotes, booking requests and coordinator approvals.
4. Destination logistics: accommodation requests, arrival/departure details, transfer coordination and guest support. A request is not a confirmed room or transfer booking.
5. Calendar export: downloadable ICS with stable event identifiers, time zones, update sequence and cancellation handling.
6. Later phases: verified payments/refunds, vendor marketplace, optional AI planning assistance and AR/VR venue content.

## Architecture decision

Proposed baseline: TypeScript, Next.js, PostgreSQL and Prisma. Begin with one application and clear modules; split services only when operational requirements justify it. Package versions, hosting and identity provider must be selected and validated during implementation.

| Proposed path | Responsibility |
| --- | --- |
| apps/web | Public pages, guest portal and authenticated couple/coordinator interfaces |
| apps/admin | Optional separate administrative interface when justified |
| packages/database | Prisma models, migrations and synthetic seed data |
| packages/ui | Accessible shared UI components |
| packages/contracts | Validated API/event contracts |
| packages/utils | Date, currency and calendar utilities |
| docs | Decisions, source register and acceptance evidence |

These paths describe the future layout; application packages are not implemented yet. API handlers can initially live within apps/web.

## Data boundaries

Every wedding belongs to an organization. Membership roles and wedding assignments scope all reads and writes on the server. A couple sees their assigned weddings; a vendor sees only assigned proposals/bookings; a guest sees only their invited household and permitted events.

Proposed entities: Organization, Membership, Wedding, WeddingMember, Household, Guest, Invitation, Event, EventInvitation, RSVP, SeatingTable, SeatAssignment, Vendor, Proposal, Booking, BudgetItem, PaymentReference, AuditEvent and OutboxEvent.

Use composite tenant/wedding foreign keys where relevant. Enforce one RSVP per invited guest/event and one seating assignment per guest/event. Table-capacity checks must be transaction-safe. Derive tenant membership from authenticated context, never trust a request-supplied company ID alone.

## Invitation and privacy requirements

Generate high-entropy invite tokens, store only hashes, support expiry/revocation and rate-limit redemption. Exchange invitations for scoped sessions. Never embed guest lists, invite secrets or private venue details in public JSON, source code or static bundles. Prevent tokens entering analytics/referrer logs. Staff accounts require individual identity and audited role changes.

Keep dietary/accessibility information visible only to people who need it. Define consent, retention and deletion handling before collecting real guest records. Use synthetic data until private staging access is verified.

## RSVP and seating

RSVP updates validate event membership, deadlines and plus-one limits. Seating updates check event attendance, wedding ownership and capacity in one database transaction. Concurrent requests must never exceed table capacity. Record who changed attendance or seating and when.

## Money and payments

Represent money as integer minor units plus currency, with explicit currency precision; use decimal strings across JSON where values could exceed JavaScript's safe integer range. Payment amounts come from server-approved quote versions.

Select a provider only after confirming merchant eligibility, settlement, refund support and fees. Do not promise fee-free payments. Use hosted payment collection, signed provider callbacks, deduplicated provider events and reconciled refunds. A browser success redirect is not proof of payment. No live gateway or payment release is enabled by this blueprint.

## Brain Coral integration

WHYNOT owns wedding plans, invitations, RSVP and seating. Explicit integration contracts assign authoritative ownership of supplier commitments, hospitality reservations and financial postings to the appropriate Brain Coral module or approved specialist system.

Use versioned events with event ID, organization, wedding ID, source reference, schema version and trace ID. Write outbox events atomically with business state; consumers deduplicate and reject conflicting replays. Retries must not duplicate bookings or charges. Present pending/failed synchronization to coordinators and reconcile source acknowledgements.

## Hosting and reuse

Static hosting can support public wedding content, but private RSVP and administrative functions require authenticated backend controls. Free service tiers are not a guaranteed zero-cost production service. Google Sheets may support a limited prototype/export; it is not the proposed authoritative multi-tenant data store.

AI may draft checklists or budget suggestions from authorized data. It cannot confirm bookings, send guest messages, approve expenditure or change payment records without the defined workflow.
