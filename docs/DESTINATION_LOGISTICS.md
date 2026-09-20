# WHYNOT destination logistics specification

Status: planned development; no live booking, flight feed or payment connection is implemented.

## Guest itinerary and access

Use explicit EventInvitation records for each invited guest/event, scoped by organization and wedding. VIP tags support organization and presentation; they never grant access by themselves. Filter private events on the server, including calendar exports and notifications.

Store event start/end instants with an IANA display time zone. Represent RSVP as PENDING, ACCEPTED or DECLINED, not a Boolean that confuses no response with rejection. Maintain versioned schedules, stable calendar UIDs and update/cancellation sequence numbers.

## Proposed relational model

Every row below is scoped by organizationId and weddingId. Composite foreign keys must prevent linking records from different weddings.

| Entity | Important fields and relationships | Required invariant |
| --- | --- | --- |
| Household / Guest | Household membership; optional contact email; guest ID | Email is not a globally unique guest identity; families may share contacts |
| Event / EventInvitation / RSVP | Time zone, start/end; guest invitation; response status | One invitation and one current response per guest/event |
| TravelLeg / TravelPartyMember | Transport mode, carrier, service number, service date, origin, destination, scheduled/estimated/actual times, sourceUpdatedAt | Multiple arrival and departure legs per guest; stale updates cannot overwrite newer evidence |
| AccommodationBlock / BlockNight | Property/room type reference, stay date, contracted quantity, cutoff and quote currency | Inventory is date-specific; authoritative supplier acknowledgement required |
| AccommodationRequest | Household/guest allocations, arrival/departure, requested rooms, source booking reference, status | REQUESTED is distinct from HELD and CONFIRMED; no allocation above acknowledged capacity |
| TransferRun / TransferPassenger | Mode, route, pickup window, operator, capacity and assigned guests | Atomic capacity allocation; no duplicate passenger per run; track luggage/accessibility needs separately |
| Quote / QuoteLine / PaymentReference | Version, amountMinor, currency, provider reference, settlement currency and fees | Exact monetary representation; no addition of unrelated currencies |
| LogisticsException | Source, affected guests, owner, deadline, resolution and evidence | Unresolved disruption remains visible until acknowledged and resolved |

A single hotelBooked string and one arrivalDate/flightNumber on Guest cannot represent connecting flights, split family travel, multi-property stays or returns. Use travel legs and assignments.

## Maldives operating flow

Support airport arrival, onward boat/seaplane/domestic-air or road legs, hotel check-in and return transfer as distinct services. Record operator-confirmed operating windows, connection buffers, passenger/luggage limits and weather disruption status. Obtain operating constraints from approved operators rather than hardcoding universal assumptions.

Coordinator workflow: review arrivals → group compatible passengers → request capacity → obtain operator acknowledgement → issue guest instructions. Delay signals create a replanning exception; they must not silently confirm a different paid transfer.

## Flight status adapter contract

Proposed authenticated endpoint: POST /api/integrations/flight-events.

Required fields: eventId, schemaVersion, providerServiceId, serviceDate, origin, destination, observedAt, status and optional estimated/actual departure/arrival. Provider identity and authorized wedding subscriptions are resolved server-side. Flight number alone is insufficient to identify a service.

Validate provider authentication, payload size, schema and timestamps. Deduplicate by provider/eventId; hold conflicting duplicate payloads for review. Preserve raw source evidence under restricted retention, use monotonic provider versions where available, and record processing results. Explicitly handle cancelled services, diversions, missing feeds and out-of-order messages.

Feed access requires a separately selected provider and tested contract. Self-reported guest flight details remain labelled unverified until matched.

## Accommodation and transfer reliability

Create local requests and outbox records atomically. Treat remote timeouts as unknown outcomes; reconcile supplier references before retrying or issuing compensations. Retrying a request must not create a second booking. Room block release/cancellation must update the same authoritative inventory path used for allocation.

## Currency and registry handling

Keep quoted, charged and settled amounts/currencies separately. Record applied FX rate, source, timestamp, rounding policy, fees and refund references. Estimates are labelled estimates; the payable amount is fixed to an accepted quote version. Multi-currency support depends on the chosen merchant account and provider capabilities.

Do not equate registry bank-detail display with payment processing. Public gift pages must not expose internal bank credentials or guest contribution histories.

## Required acceptance scenarios

1. A guest cannot read a private event or export its calendar through a guessed ID.
2. One household travels on two different flights and returns by a different route.
3. A stale flight update does not undo a more recent cancellation.
4. Two concurrent requests compete for the final transfer seat; only one is assigned.
5. A hotel block has no capacity on a middle night; no partial reservation remains.
6. A supplier confirms but the response is lost; retry/reconciliation produces one booking.
7. Multi-currency payment/refund records reconcile to their original quote and settlement.
8. A coordinator resolves a delay and records guest notification acknowledgement.
9. Cross-wedding guest, travel, accommodation and transfer references are rejected.

These are implementation acceptance requirements, not tests already passed.
