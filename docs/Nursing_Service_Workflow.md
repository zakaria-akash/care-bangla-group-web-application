# Care Bangla — Home Nursing Service Journey

> Public workflow overview · [Return to the project overview](../README.md)

## Purpose

The home-nursing experience helps families move from understanding a care option to submitting a structured service request. It is one of the private platform's dedicated care workflows, built to retain the operational context a generic contact form would lose.

```mermaid
flowchart LR
  A[Discover service] --> B[Choose care type and duration]
  B --> C[Provide care context]
  C --> D[Review request]
  D --> E[Submit booking intent]
  E --> F[Internal review and coordination]
  F --> G[Service outcome and follow-up]
```

## Customer experience

1. A visitor learns about nursing support, scope, and the relevant care tier.
2. The booking journey collects details appropriate to at-home care, rather than treating all services as the same form.
3. The visitor reviews the request and continues through the applicable checkout or confirmation step.
4. The request becomes a durable operational record for authorized staff follow-up.

## Operations experience

The staff workspace provides a service-specific booking queue. Team members can review the submitted context, progress the request through its operational lifecycle, and retain a clear history rather than relying on informal channels alone. Supporting applicant management helps the care-team pipeline remain distinct from the customer booking journey.

## Product safeguards

| Concern | Design intent |
|---|---|
| Appropriate data | Capture care-relevant information at the point of request and minimize unnecessary collection. |
| Clear lifecycle | Keep booking status and payment/fulfillment state understandable to the operations team. |
| Record integrity | Preserve the submitted booking context as an operational record; follow-up changes are traceable through the workflow. |
| Access | Restrict operational views to authorized staff and avoid exposing booking details publicly. |
| Adaptability | Allow tier, duration, pricing policy, and intake fields to evolve as the service matures. |

## Extension potential

Potential next steps include capacity-aware nurse assignment, availability calendars, secure document exchange, payment settlement, automated reminders, and outcome reporting. Those integrations require deliberate clinical, privacy, and operational design; they are not implied by this public workflow overview.

## Public-repository boundary

Booking rules, data fields, operational thresholds, staff identities, customer records, and source implementation are private. This document describes the product journey only.
