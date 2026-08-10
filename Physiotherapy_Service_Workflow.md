# Care Bangla — Physiotherapy Service Journey

> Public workflow overview · [Return to the project overview](../README.md)

## Purpose

Physiotherapy is represented as a distinct, course-oriented care journey. The private platform can collect and coordinate a request in the context of planned visits, rather than treating a multi-visit service exactly like a one-time purchase or general inquiry.

```mermaid
flowchart LR
  A[Explore physiotherapy support] --> B[Choose service type or visit plan]
  B --> C[Share request information]
  C --> D[Review and submit]
  D --> E[Physiotherapy operations queue]
  E --> F[Assessment, visit coordination, and follow-up]
```

## Workflow characteristics

| Characteristic | Why it matters |
|---|---|
| Course-aware design | A therapy plan may involve more than one visit, so the workflow leaves room for service progression. |
| Dedicated operational path | Physiotherapy requests are not mixed with unrelated nursing or consultation records. |
| Specialist pipeline | Applicant and published-specialist workflows can evolve alongside the customer journey. |
| Shared platform foundations | Validation, authenticated account access, multilingual content, messaging, and CMS controls remain consistent across the product. |

## Operational model

Authorized staff can use a dedicated workspace to review incoming requests, coordinate the next step, and manage status over time. The actual availability, clinical assessment, pricing policy, and assignment decisions remain private operational concerns; they are not exposed through this public repository.

## Extension potential

The modular model can accommodate assessments, visit packages, specialist availability, route planning, recurring appointments, customer progress communication, and feedback. Clinical records or outcome tracking would need explicit consent, access-control, retention, and compliance design before implementation.

## Public-repository boundary

This is a product-level journey map. It intentionally contains no patient information, clinical details, staff schedules, operational policies, private APIs, or source code.
