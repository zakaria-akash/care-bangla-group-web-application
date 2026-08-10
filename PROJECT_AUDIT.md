# Care Bangla — Engineering Audit & Technical Posture

> A public technical review of the private application. [Return to the technical overview](README.md).

## Audit scope

This review is based on the private project’s architecture, route/module inventory, dependency configuration, and documented behavior. It is not a third-party penetration test, compliance certification, load test, or guarantee about the public deployment. No customer records, secrets, production logs, or source archive are published here.

## Technical inventory

| Dimension | Observed implementation |
|---|---|
| Framework | Next.js `16.2.7` + React `18.3.1`, App Router. |
| Application size | ~630 JS/JSX files, 149 reusable components, 40 Mongoose models, 140 route handlers. |
| Data | MongoDB/Mongoose with pooled connection strategy; GridFS for staff media and controlled attachments. |
| State | Redux Toolkit/RTK Query for remote data; React contexts for cart/language local state. |
| UI | Bootstrap/Sass public system; Ant Design staff system; Recharts and dnd-kit for operations. |
| Domain coverage | Content, shop, user portal, message threads, 5 specialized care booking domains, applicants, SEO/analytics support. |

## Architecture strengths

| Strength | Evidence in the design |
|---|---|
| Clear domain separation | Dedicated models/routes for nursing, caregiver, baby care, physiotherapy, doctor consultation, commerce, editorial, and messaging. |
| Correct state split | RTK Query owns cacheable server data; cart/language avoid unnecessary server/cache semantics. |
| Content resilience | Server metadata/redirect resolution, structured content primitives, canonical history, and safe editorial fallback. |
| Operational maturity | Staff CMS covers content, media, catalog, profiles, booking queues, applicants, users, messages, and SEO. |
| Authorization layers | Session cookie, server guards, proxy gate, client UX guard, request validation, attachment ownership. |
| Discoverability | Dynamic metadata, canonical recovery, structured image data, JSON-LD patterns, sitemap/robots/monitoring posture. |

## Risk register

| Area | Current posture | Recommended control |
|---|---|---|
| Healthcare/personal data | Private database/authorization boundary exists. | Data inventory, retention, consent, breach/incident process, jurisdiction-specific review. |
| Booking rules | Server-side pricing, conflict, and lifecycle validation. | Unit/integration tests for rule matrices and a regression suite per service. |
| Authentication | JWT/httpOnly cookie and middleware-style page gate. | Session rotation/revocation policy, dependency monitoring, role granularity, audit event review. |
| Rich CMS content | Structured links/images reduce raw HTML risk. | Strict schema evolution tests and editorial validation policy. |
| External services | Email, maps, translation, search/performance providers are server-configured. | Timeouts, retry/backoff, observability, vendor/privacy review, graceful fallback. |
| Performance | App Router, loading states, modern image settings, cache design. | Real-user monitoring, page budgets, image governance, mobile/network testing. |
| Authorization scope | Admin vs user vs public route families are separated. | Automated ownership/negative authorization tests. |

## Test and quality gap analysis

The private project has rich runtime/domain behavior that benefits from automated proof. Highest-value coverage is:

1. Pricing snapshots for nursing/caregiver/baby/physiotherapy/doctor paths.
2. Date range, visit-gap, and appointment conflict edge cases.
3. Payment/status transition rejection matrix.
4. Canonical slug, replacement target, noindex, and missing-content fallback behavior.
5. Admin and user ownership guards, especially private message attachments.
6. EN/BN dictionary parity, preference isolation, mobile typography, and metadata output.
7. Checkout/request flows under mobile viewport and slow/error responses.

## Delivery recommendations

| Horizon | Recommendation |
|---|---|
| Immediate | CI lint/static checks, critical route smoke tests, dependency update cadence, test harness for pure domain helpers. |
| Next | Integration tests against isolated MongoDB fixtures, role/audit model, error tracking, performance and accessibility budgets. |
| Integration phase | Adapter boundaries for payment/calendar/video/SMS, contract tests, vendor failure behavior, consent/retention assessment. |
| Scale phase | Observability dashboards, capacity-aware staff scheduling, analytics warehouse, feature flags for operational rollout. |

## Known limitations

- Offline caching is not an active feature; manifest support does not equal offline-first reliability.
- Staff permissions are less granular than a mature finance/dispatcher/editor/super-admin model.
- Some operational capabilities require manual coordination until calendar, payment, or workforce integrations are explicitly designed.
- This public repository cannot be run locally: application source, private configuration, database, and deployment artifacts are intentionally withheld.

## Conclusion

The project demonstrates full-stack capability beyond a marketing site: route architecture, server data authority, CMS authoring primitives, workflow-specific models, media/security controls, and long-term discoverability are intentionally connected. Maturity now depends chiefly on automated verification, observability, governance, and carefully bounded integrations—not on exposing the proprietary codebase.
