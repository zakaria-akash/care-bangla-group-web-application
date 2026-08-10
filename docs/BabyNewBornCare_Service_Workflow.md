# Care Bangla — Baby & Newborn Care Service Journey

> Public workflow overview · [Return to the project overview](../README.md)

## Purpose

Baby and newborn care is presented as a family-centered service journey. It gives parents and guardians a clear way to learn about support, select an appropriate option, and begin a private request without reducing a sensitive care need to a single generic form.

```mermaid
flowchart LR
  A[Learn about newborn support] --> B[Choose a care option]
  B --> C[Provide family requirements]
  C --> D[Review request]
  D --> E[Submit securely]
  E --> F[Specialist staff review]
  F --> G[Coordination and service follow-up]
```

## Experience design

| Area | Product behavior |
|---|---|
| Public content | Explains the service and provides an approachable route into the booking journey. |
| Service selection | Supports care-option and duration context that can be tailored to newborn-care operations. |
| Request handling | Creates a dedicated operational record rather than merging the request into unrelated service queues. |
| Applicant pathway | Keeps relevant care-professional applications separate from family requests. |
| Administration | Gives authorized staff service-specific visibility for coordination and status management. |

## Why a dedicated workflow matters

The product intentionally avoids assuming that every home-care service has identical intake, scheduling, or fulfillment needs. Baby/newborn care can evolve its own content, service tiers, validation, operational queue, and reporting while preserving shared account, localization, CMS, and security capabilities.

## Future potential

The modular design can accommodate recurring schedules, availability matching, care-plan notes, consent-aware document exchange, optional home-visit coordination, and family feedback loops. Any future feature involving sensitive health or child-related information must receive dedicated privacy, consent, and retention review.

## Public-repository boundary

No family data, care details, staff records, internal policy, private implementation, or access controls are published in this showcase.
