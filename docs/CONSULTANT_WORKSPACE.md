# WHYNOT consultant workspace

Status: product specification and example contract; not an implemented screen or API.
Proposed implementation remains TypeScript/Next.js/PostgreSQL as defined in the platform blueprint.

## Consultant role

A consultant translates a couple's preferences into an executable plan, coordinates vendors and resolves day-of exceptions. Consultant access is restricted to assigned weddings. It does not grant platform administration, bank/payment release, or unrestricted access to other clients.

## Intake wizard

| Step | Inputs | User-facing instruction |
| --- | --- | --- |
| 1 — Celebration | Working title, celebration type, preferred dates/time zones, locations, guest estimate | Tell us what you want to celebrate and where. Dates remain provisional until confirmed. |
| 2 — People | Household structure, contact preferences, accessibility requests, dietary requirements | Share only the information needed to look after your guests. |
| 3 — Budget | Currency, target ceiling, included costs, contingency percentage and approval | Choose a visible reserve for unexpected costs. Review it before approving your budget. |
| 4 — Events | Multi-day schedule, explicit invitation groups, capacity, private events | Choose who is invited to each event. Private events appear only for authorized guests. |
| 5 — Travel | Accommodation requests, arrival/departure legs, transfer needs | Add known arrangements; requests remain pending until the provider confirms. |
| 6 — Suppliers | Service categories, proposal milestones, evidence requirements, owners | Identify which services are needed and who approves each commitment. |
| 7 — Review | Summary, unresolved decisions, communication preferences and consent | Review the plan. Submitting starts consultant review; it does not book services or authorize payments. |

Save drafts and resume. Required fields depend on planning stage; unknown travel details must not block initial intake. Validate inputs on the server, explain errors next to fields, and make the wizard usable by keyboard and on mobile.

## Guest permissions and seating

Use explicit invitations and scoped roles for access. Relationship labels may help consultants organize guests but cannot replace server-side authorization. Restrict sensitive interpersonal notes; prefer actionable constraints such as "separate tables" without recording unnecessary explanations.

Seating constraints have type, affected guest IDs, priority (hard/soft), reason visibility and author. Examples: same household preferred together, separate tables required, accessible seat required. Suggestions must satisfy confirmed attendance and capacity. If constraints conflict, show an unresolved exception; do not silently break a hard constraint. Consultants review and approve suggestions. Enforce final allocations atomically.

## Transparent contingency

Treat 10–15% as an optional planning suggestion, not a mandatory or hidden charge. Configure percentage, eligible base, whether the reserve fits within or exceeds the target ceiling, currency precision and rounding policy.

Display base estimate + contingency = planned total, with a separate remaining-budget figure. Record who approved the version. Keep taxes, service fees, contingency, committed costs and paid amounts distinct. Updating a planning reserve cannot change a signed vendor quote or initiate payment.

## Notification pipeline

Proposed offsets such as 60 days before lodging deadlines or 48 hours before arrival are editable templates, not activated schedules. Anchor each trigger to a specific event/deadline and time zone.

Before enqueueing, check recipient authorization, communication preference/consent, current schedule version and cancellation status. Recheck before sending; apply quiet hours where configured. Require a preview and approval for newly activated campaigns.

Use durable jobs/outbox records with a deduplication key based on wedding, recipient, notification rule, anchor occurrence and schedule version. Handle retries with backoff and bounded attempts; record provider message IDs and delivery states. A timeout means delivery is unknown, not necessarily failed. Track superseded jobs when dates change. Redact private event details from messages to uninvited recipients.

No email, SMS or other message is sent by this specification.

## Vendor workspace and evidence

Vendors see only their assigned services and necessary operational details. Catering may receive authorized meal counts and relevant requirements; it does not receive the full guest directory by default.

Track evidence requests, document versions, expiry, review owner and status: REQUESTED, SUBMITTED, UNDER_REVIEW, ACCEPTED, REJECTED or EXPIRED. Upload alone never proves insurance coverage or compliance. Use private storage, bounded file types/sizes, malware scanning and scoped expiring downloads.

Show proposal, approved commitment, deposit schedule, service milestone, invoice review and payment status separately. Milestone completion cannot automatically authorize payout. Approval authority comes from configured policy and authenticated membership. Brain Coral integration requires acknowledgement and reconciliation.

## Consultant dashboard

My Weddings | Intake Reviews | Decisions | Budget | Guests & Seating | Travel |
Vendor Evidence | Timeline | Communications | Exceptions | Reports

Prioritize outstanding decisions, deadlines, evidence gaps and disrupted bookings. Every exception needs an owner, next action and status history.

## Proposed intake API contract

POST /api/weddings/{weddingId}/consultant-intakes
Authenticated consultant with assignment to the wedding; organization derived from trusted session.
Required Idempotency-Key header. Validate the same-key/same-payload retry and reject conflicting reuse.
PATCH edits require expectedVersion to reject stale overwrites. Payload values are validated rather than trusted.

Example using synthetic data:

```json
{
  "schemaVersion": 1,
  "expectedVersion": 0,
  "celebration": {
    "title": "Example Island Celebration",
    "preferredStartDate": "2027-02-10",
    "timeZone": "Indian/Maldives",
    "guestEstimate": 40
  },
  "budget": {
    "currency": "USD",
    "targetCeilingMinor": "3000000",
    "baseEstimateMinor": "2500000",
    "contingencyBasisPoints": 1000,
    "reserveWithinCeiling": true,
    "approvalStatus": "DRAFT"
  },
  "events": [
    {
      "clientReference": "welcome",
      "title": "Welcome gathering",
      "visibility": "INVITATION_ONLY"
    }
  ],
  "notificationTemplates": [
    {
      "anchor": "LODGING_DEADLINE",
      "offsetDays": -60,
      "enabled": false
    },
    {
      "anchor": "ARRIVAL",
      "offsetHours": -48,
      "enabled": false
    }
  ],
  "status": "DRAFT"
}
```

## Acceptance requirements

- A consultant cannot read or edit an unassigned wedding, including through exports.
- Intake replay creates one record; changed payload under the same key is rejected.
- A stale edit cannot overwrite a newer budget or itinerary.
- The couple sees contingency and total before approving; approval does not release money.
- Contradictory hard seating constraints remain unresolved until reviewed.
- Changed/cancelled events invalidate pending reminders; unauthorized guests receive no private details.
- Vendor uploads remain private and unaccepted until reviewed.
- Vendor milestone status cannot bypass financial approval.
- A nontechnical consultant completes intake, assigns a decision and produces an approved plan without API tools.

All checks are pending implementation and execution.
