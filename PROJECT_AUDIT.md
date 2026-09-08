# Care Bangla — Engineering Audit & Technical Posture

> A public technical review of the private application. [Return to the technical overview](README.md).

## Audit scope

This review is based on the private project’s architecture, route/module inventory, dependency configuration, and documented behavior. It is not a third-party penetration test, compliance certification, load test, or guarantee about the public deployment. No customer records, secrets, production logs, or source archive are published here.

## Technical inventory

| Dimension | Observed implementation |
|---|---|
| Framework | Next.js `16.3.0` + React `18.3.1`, App Router. |
| Application size | ~699 JS/JSX files, 164 reusable components, 50 Mongoose models, 166 route handlers, 283 routes in a production build. |
| Data | MongoDB/Mongoose with pooled connection strategy; GridFS for staff media and controlled attachments. |
| State | Redux Toolkit/RTK Query for remote data; React contexts for cart/language local state. |
| UI | Bootstrap/Sass public system; Ant Design staff system; Recharts and dnd-kit for operations. |
| Domain coverage | Content, shop, user portal, message threads, 5 specialized care booking domains, applicants, SEO/analytics support. |
| Assisted authoring | Supervised AI copilot with its own job, proposal, revision, usage and rate-limit collections; environment-gated and off by default. |
| Automated verification | Deterministic unit suite covering AI safety, content-block and inline-text invariants, legacy conversion, and SEO review localization. |
| Deployment posture | Native Ubuntu 24.04 VPS; Nginx terminates HTTPS and proxies to loopback Next.js; systemd supervises the application; MongoDB 8 and GridFS remain loopback-only. |

## Architecture strengths

| Strength | Evidence in the design |
|---|---|
| Clear domain separation | Dedicated models/routes for nursing, caregiver, baby care, physiotherapy, doctor consultation, commerce, editorial, and messaging. |
| Correct state split | RTK Query owns cacheable server data; cart/language avoid unnecessary server/cache semantics. |
| Content resilience | Server metadata/redirect resolution, structured content primitives, canonical history, and safe editorial fallback. |
| Operational maturity | Staff CMS covers content, media, catalog, profiles, booking queues, applicants, users, messages, and SEO. |
| Authorization layers | Session cookie, server guards, proxy gate, client UX guard, request validation, attachment ownership. |
| Discoverability | Dynamic metadata, canonical recovery, structured image data, JSON-LD patterns, sitemap/robots/monitoring posture. |
| Deterministic content health | One shared analyzer scores every record against the full industry SEO checklist and drives the editor panel, AI field targeting, and post-proposal projection alike, so a score cannot drift between surfaces. |
| Bounded AI authority | The assistant proposes into an explicit field allow-list, must satisfy closed output schemas, and cannot publish, price, message, or apply without a separate capability and confirmation. |

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
| Assisted authoring | Server-only provider key, redaction before every call, closed schemas, field allow-lists, per-admin budget and rate limits, and a full job/usage ledger. | Live-model staging evaluation, an approved retention policy, named medical and editorial reviewers, and periodic prompt/model regression runs. |
| Single-host deployment | Application and database share one VPS, so a host incident can affect both. | Verified logical backups, off-server copies, isolated restore drills, monitoring, capacity review and an upgrade path before sustained production traffic. |

## Verified VPS deployment observation

The native VPS deployment reached owner-authorized commercial production acceptance on **7 September 2026**. A read-only follow-up inspection confirmed the following without exposing credentials, private configuration, customer data or deployable infrastructure files:

| Control | Observed result |
|---|---|
| Runtime | Ubuntu 24.04 LTS, Node.js 24 LTS, MongoDB 8 Community and Nginx are installed. |
| Process supervision | Next.js is enabled and active under systemd, runs as an unprivileged deployment identity, reports a successful main-process exit state and has zero automatic restarts. |
| Network boundary | Nginx alone accepts public HTTP/HTTPS traffic; Next.js and MongoDB listen only on loopback interfaces. |
| Transport security | A valid Let's Encrypt certificate is active and HTTP security headers include HSTS. |
| Application health | Homepage, Bengali route, dynamic sitemap and a public content API returned successful responses. |
| Recovery | The database backup timer is active; a compressed, checksummed off-server backup and an isolated restore comparison were previously completed. |
| Capacity snapshot | Approximately 3.1 GiB memory remained available, swap was unused and root-disk utilization was approximately 15% at inspection time. |

This proves the observed production infrastructure baseline, not indefinite availability, regulatory compliance or penetration-test clearance. Eligible published pages use page-level indexing controls under the canonical `https://www.carebanglabd.tech` origin; private and ineligible routes remain excluded. Authenticated staff/customer workflows still require their recurring acceptance checklist. See [VPS Deployment & Operational Observation](VPS_DEPLOYMENT_OBSERVATION.md).

## Correction to a previously published finding

An earlier revision of this audit reported that the application's security middleware was inert because the file was named `proxy.js` rather than `middleware.js`. **That finding was wrong and has been withdrawn.** Next.js 16 renamed Middleware to Proxy: `proxy.js` exporting `proxy(request)` alongside `config` is the current convention, verified against the framework's own bundled documentation. The admin gate and the security response headers were live throughout.

What survived the re-check is narrower and real, and remains open: the proxy matcher excludes `/api`, so JSON endpoints are served without the frame and content-type protections that page responses receive.

The episode is recorded rather than quietly edited because it illustrates a genuine hazard in reviewing a fast-moving framework: a convention that changed between major versions can make correct code look broken to a reviewer working from older knowledge.

## Test and quality gap analysis

An automated suite now exists and runs green, covering AI safety behavior, inline-text and content-block invariants, legacy content conversion, document parsing, and SEO review localization. It is a foundation rather than full coverage; the domain rules below remain the highest-value additions:

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
| Deployment gate | Commit the HTTPS-aware proxy fix, repeat authenticated acceptance, authorize production indexing, enable external monitoring, then reassess KVM capacity using real traffic. |

## Known limitations

- Offline caching is not an active feature; manifest support does not equal offline-first reliability.
- Staff permissions are less granular than a mature finance/dispatcher/editor/super-admin model, although AI use, application, audit and management are already separate capabilities.
- The AI copilot is disabled by default and depends on a company-owned provider project, billing approval, and organizational review sign-off that are deliberately not part of the codebase.
- Some operational capabilities require manual coordination until calendar, payment, or workforce integrations are explicitly designed.
- This public repository cannot be run locally: application source, private configuration, database, and deployment artifacts are intentionally withheld.

## Conclusion

The project demonstrates full-stack capability beyond a marketing site: route architecture, server data authority, CMS authoring primitives, workflow-specific models, media/security controls, and long-term discoverability are intentionally connected. Maturity now depends chiefly on automated verification, observability, governance, and carefully bounded integrations—not on exposing the proprietary codebase.
