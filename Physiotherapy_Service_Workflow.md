# Care Bangla — Physiotherapy Module Specification

> Developer-facing module reference. [Return to the technical overview](README.md).

## Domain difference: visit schedule, not shift range

Physiotherapy is a course-oriented booking domain. It models a normalized list of 45–60 minute `{ date, time }` visits rather than a 12/24-hour date range. There is currently no public or staff specialist roster; specialist coordination remains an internal operational step.

```mermaid
flowchart LR
  Discover[/service/physiotherapy] --> Builder[/physiotherapy/book/:type]
  Builder --> Normalize[Normalize/dedupe date-time slots]
  Normalize --> Price[Course price + discount engine]
  Price --> Checkout[/physiotherapy/checkout]
  Checkout --> API[Booking handler + gap guard]
  API --> Queue[/admin/bookings]
```

## Model, schedule, and pricing

| Area | Technical behavior |
|---|---|
| Models | `PhysiotherapyBooking` stores user-owned schedule, derived price/payment timing, status, documents; `PhysiotherapyApplicant` owns recruitment. |
| Slot normalisation | Sort/deduplicate submitted date-time values and reject malformed/out-of-window visits. |
| Visiting hours | Valid appointments fall between 06:00 and 22:00. |
| Same-day spacing | Minimum 60 minutes between visits, both within a requested schedule and across the customer’s non-cancelled bookings. |
| Base price | Rate per visit is derived from the specialist tier definition. |
| Course rule | A qualifying 15+ visit course within its allowed consecutive-day span receives a configured whole-booking discount and advance-payment timing. |
| Trusted total | Server persists normalized visits, subtotal, discount, total, and payment timing; the client estimate is not trusted. |

```ts
// Conceptual pricing pipeline; not private code.
const visits = normalizeVisits(input.visits);
assertWithinOperatingHours(visits);
assertMinGap(visits, 60);
await assertNoConflictWithCustomerBookings(userId, visits);
const quote = computeCoursePrice(visits, tier);
```

## Lifecycle and conflict rules

Payment begins due. It may be marked paid only once work is confirmed, in progress, or complete. Paid records cannot return to pending/cancelled. Completed bookings remain operationally locked while payment reconciliation remains possible. Cancellation preserves history instead of deleting the record.

`findVisitGapConflict()` protects a submitted schedule; `findConflictWithExistingBookings()` protects against overlaps with the same customer’s active history. The two checks exist because a valid internal schedule can still conflict with a previously created booking.

## Module map

| Layer | Route/module family | Responsibility |
|---|---|---|
| Discovery | `service/[serviceId]` → `ServiceDetailsPhysiotherapy` | Service narrative, tier, previews, service grid, booking/recruitment entry. |
| Booking UI | `physiotherapy/book/[type]` → schedule builder | Date/time/repeat builder, grouped visits, removal, live estimate, discount feedback. |
| Checkout | `physiotherapy/checkout` | Account details, day-by-day schedule review, documents, server-matched summary. |
| Recruitment | `physiotherapy/apply-as-specialist` | Specialist applicant form and review pipeline. |
| Public API | `api/physiotherapy-bookings`, `api/physiotherapy-applicants` | Owned history/creation and application submission. |
| Staff console | Unified `admin/bookings` → `PhysiotherapyBookingsSection` | Review, manual booking, guarded state update, document append. |
| Domain helpers | Physiotherapy tier data + visit-conflict helper | Normalisation, schedule shape, course price, shared conflict message. |

## Engineering boundaries and future work

The current design protects the customer’s own schedule, not a full therapist capacity roster. Future specialist calendars, route/availability optimization, online payment, clinical outcome records, and reminders require explicit capacity, consent, retention, and compliance decisions. Candidate tests: visit normalisation, time-window boundaries, same-day gaps, cross-booking conflict, discount threshold, and transition matrix.

## Public boundary

Exact fees, patient details, applicant records, specialist assignment, source implementation, and private schedules remain closed.
