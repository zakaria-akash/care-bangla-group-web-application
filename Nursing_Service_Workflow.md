# Care Bangla — Home Nursing Module Specification

> Developer-facing module reference. [Return to the technical overview](README.md).

## Module scope

Home nursing is a roster-capable, date-range booking domain. Families can select a nursing tier or an eligible named nurse, submit a 12/24-hour care request, and continue through checkout. Staff manage content, nurse profiles, bookings, documents, lifecycle state, and applicant records through protected route families.

```mermaid
flowchart LR
  Discover[/service/nursing-care] --> Tier[Tier or nurse selection]
  Tier --> Form[/nurses/book/:nurseType]
  Form --> Checkout[/nurses/checkout]
  Checkout --> API[Booking API]
  API --> Guard[Pricing + conflict + status guard]
  Guard --> DB[(Booking / nurse models)]
  DB --> Admin[/admin/nurses/bookings]
```

## Data model family

| Model | Responsibility |
|---|---|
| `Booking` | Customer-owned nursing request, date range, shift, derived price snapshot, lifecycle/payment state, documents, optional nurse relationship. |
| `NurseTier` | Tier label, qualification/experience, 12-hour rate, eligibility/content shown in tier cards. |
| `NurseMember` | Published roster profile, specialty/designation, public metadata, availability context, ordering and visibility. |
| `NursingService` | Admin-managed service-catalogue items. |
| `NursingGalleryTab` | Alternating feature/preview rows on the public service page. |
| `NurseApplicant` | Nursing recruitment submission and review data. |
| `PageContent` | Service-page editorial composition and CTA content. |

## Server-authoritative pricing

The browser can display an estimate, but the booking handler recomputes and snapshots price. This prevents a client-edited amount becoming the persisted charge context.

| Input | Rule |
|---|---|
| Shift | 12-hour rate from `NurseTier`; 24-hour coverage is calculated as two 12-hour shifts. |
| Duration | Inclusive day-count calculation from start/end dates. |
| Total | Derived daily rate × inclusive days, snapshotted on `Booking` creation. |
| Later edits | Derived fields are immutable booking history, not recomputed from today’s tier values. |

```ts
// Conceptual only: trusted pricing belongs on the server.
const tier = await nurseTierRepository.requirePublished(input.tierId);
const dailyRate = input.shift === '24h' ? tier.rate12h * 2 : tier.rate12h;
const total = dailyRate * inclusiveDays(input.startDate, input.endDate);
```

## Conflict and lifecycle engine

The server evaluates overlapping active bookings before persistence using the requested range, selected nurse when applicable, and non-cancelled existing records. Client feedback can help the form but cannot be the authority.

| Rule | Implementation intent |
|---|---|
| Payment starts due/unpaid | Submission does not make a service paid. |
| Mark paid | Valid only for confirmed, in-progress, or completed work. |
| Paid record | Cannot return to pending/cancelled, preserving delivery/payment history. |
| Completed record | Operational detail locks; payment reconciliation remains allowed. |
| Cancellation | Preserve record/history rather than delete an operational request. |

## Route and component map

| Layer | Route/module family | Responsibility |
|---|---|---|
| Discovery | `service/[serviceId]` → `ServiceDetailsNursingCare` | Banner, intro, tiers, gallery rows, service grid, roster slider, recruitment CTA. |
| Booking | `nurses/book/[nurseType]`, `nurses/nurse-details/[nurseId]`, `nurses/checkout` | Category/named-nurse path, form, review, checkout. |
| Public API | `api/bookings`, `api/nurses/*` | Published tier/roster reads and authenticated booking operations. |
| Staff bookings | `admin/nurses/bookings`, admin nursing handlers | Lifecycle, payment, documents, customer selection, manual booking. |
| Staff roster | `admin/nurses`, admin nurse handlers | Profile CRUD, order, publication. |
| Service CMS | Nursing content/tier/service/gallery handlers | Editable service copy, tiers, visual rows, catalogue items. |
| Media | GridFS and SEO-aware media route | Managed uploads and structured image output. |

`PageBreadcrumb`, `SectionHeading`, `TierCard`, `ServicePreviewGallery`, `MedicalTeamSection`, and shared `Service` cards compose the public page. Defined public editorial records can use bundled fallback data; bookings/identity data never do.

## Upgrade candidates

| Current boundary | Upgrade path |
|---|---|
| Manual payment reconciliation | Verified gateway adapter with failed/refund handling. |
| Admin-led lifecycle | Nurse portal/mobile shift and availability workflow. |
| Fixed date range | Recurring booking/subscription model with conflict expansion. |
| Manual assignment | Location/specialty/capacity matching service. |
| Flat staff role | Dispatcher/finance/editor/super-admin roles with audit events. |
| Manual verification emphasis | Unit/integration tests for pricing, overlap, and transition matrix. |

## Public boundary

Actual rates, customer data, staff data, documents, schema implementation, and private procedures are not published.
