# Care Bangla — Doctor Consultation Module Specification

> Developer-facing module reference. [Return to the technical overview](README.md).

## Mode-aware consultation domain

Doctor consultation supports **home** and **virtual** modes. The chosen mode drives trusted pricing, required intake fields, appointment validation, and fulfillment data: a home visit needs address context; a virtual session needs a deliverable email/meeting path.

```mermaid
flowchart LR
  Discover[/service/doctor-consultation] --> Tier[Doctor tier + mode]
  Tier --> Book[/doctors/book/:doctorType]
  Book --> Slots[Normalize consultation slots]
  Slots --> Guard[Hours + clash + price guard]
  Guard --> Checkout[/doctors/checkout]
  Checkout --> API[Doctor booking API]
  API --> Admin[/admin/bookings]
```

## Model family and pricing

| Model | Role |
|---|---|
| `DoctorBooking` | User-owned consultation request, mode, slots, derived quote, lifecycle/payment, visit/virtual context, meeting links where applicable. |
| `DoctorTier` | Tier data with home and virtual fees, qualification/presentation, and publication state. |
| `DoctorService` | CMS-managed consultation/service catalogue. |
| `DoctorApplicant` | Practitioner recruitment record. |
| `TeamMember` | Published doctor profile/directory data where appropriate. |

| Rule | Implementation |
|---|---|
| Mode fee | Home or virtual fee is chosen from `DoctorTier` server-side. |
| Count | One appointment or a normalized short course of follow-up slots. |
| Total | Mode fee × trusted consultation count, snapshotted on `DoctorBooking`. |
| Hours | Consultation slots are accepted only within 08:00–22:00. |
| Conflict | Internal slot clashes and conflict with committed booking slots are checked before persistence. |

## Mode-specific validation and meeting links

| Mode | Required/validated context |
|---|---|
| Home | Visit address and location-relevant request data. |
| Virtual | Email required to deliver the session information. |
| Google Meet | Only valid Google Meet URL shapes are accepted; a bare code is normalized to HTTPS; a follow-up course can carry a link per appointment. |

Meeting-link validation is intentional: it prevents arbitrary URL injection in a clinical workflow and avoids promising a video link that cannot be safely delivered. The platform handles coordinator-entered links; automatic provider-calendar generation is a separate future integration.

## Lifecycle and module map

The shared booking lifecycle applies: payment can move to paid only after confirmation/in-progress/completion; paid records cannot revert to pending/cancelled; completion locks operational fields while allowing payment reconciliation; cancellation retains history.

| Layer | Route/module family | Responsibility |
|---|---|---|
| Discovery | `service/[serviceId]` → `ServiceDetailsDoctorConsultation` | Service narrative, tiers, public doctor/service content. |
| Booking | `doctors/book/[doctorType]`, `doctors/checkout` | Mode selection, slot collection, pricing preview, authenticated review. |
| Profiles/applicants | `doctors/[doctorId]`, `doctors/apply-as-doctor` | Published profile and recruitment paths. |
| APIs | Doctor booking/applicant/public content handler families | Server-authoritative quote, scheduling rules, owned history. |
| Staff UI | Unified bookings/admin doctor/content areas | Review, manual entry, lifecycle/payment/documents, CMS/profile management. |
| Helpers | Doctor tier, availability, Google Meet, email utilities | Fee computation, clash checks, safe meeting normalisation, notification delivery. |

## Upgrade candidates

- provider availability calendars and appointment holds;
- approved video-provider OAuth/calendar integration;
- payment capture/refund workflow;
- reminders, cancellations, and rescheduling policy;
- clinician-side portal and outcome/notes system subject to privacy/compliance design;
- automated tests for slot conflicts, mode field validation, fee calculation, and meeting URLs.

## Public boundary

Patient records, provider schedules, meeting links, actual prices, source code, and staff procedures are private. This document demonstrates the module’s technical shape only.
