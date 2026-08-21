# SEO Review — Deterministic Content Health

> How the private application measures the search health of a single page. [Return to the technical overview](README.md) · [SEO roadmap](SEO_Optimization_RoadMap.md)

## Position

Search health here is **deterministic, not advisory**. A shared analyzer reads the record a staff member is editing and returns a scored list of checks, each with a plain-language finding and a concrete next action. The same function runs in three places, so a score cannot drift between them:

- inside the **SEO Review** panel in every content editor,
- when **Care Bangla AI** decides which fields a repair may touch,
- when a proposal is re-scored to project its effect before anything is applied.

Scores are authoring guidance. They are not a ranking promise, and the tool says so.

## Coverage

Every page-level parameter of the industry checklist now produces a real check on dynamic product and blog pages. Measured against live records:

| Reference parameter | Implemented as | Notes |
|---|---|---|
| 1 · Image ALT text | `image-alt`, `image-alt-quality`, `duplicate-image-alt`, `focus-alt` | Missing, generic, repeated and keyphrase-relevant alt text |
| 2 · Content length | `content-length` | Per-type editorial minimums, not one global number |
| 3 · Meta title | `title-length`, `focus-title`, `duplicate-meta-title` | Length band, keyphrase placement, cross-site uniqueness |
| 4–5 · Meta description & length | `description-length`, `focus-description` | Practical 120–160 character band |
| 6 · Internal links | `internal-links` | Detected from structured link ranges, not string matching |
| 7 · External links | `outbound-links` | Applies to every content type, not only articles |
| 8 · URL structure | `url-structure`, `focus-slug` | Length, case, separators, word count — beyond keyphrase presence |
| 9 · Sitemap / indexability | `indexability` | Publish state, `noindex`, and review readiness |
| 14 · Heading structure | `heading-structure` | Exactly one H1, no skipped levels |
| 15 · Keyword optimisation | `focus`, `focus-intro`, `keyword-coverage` | Includes which listed phrases the visible copy actually contains |
| 16 · Schema markup | `schema-markup` | Checks the fields this page type's JSON-LD needs |
| 17–18 · Open Graph & Twitter | `social-cards` | One check; both are built from the same metadata inputs |
| 19 · Canonical tag | `canonical` | Self-referencing by default; flags a mismatched or relative override |
| 20 · Duplicate content | `duplicate-title`, `duplicate-description`, `duplicate-copy` | Compared across the whole site, linking to the competing record |
| 21 · Breadcrumbs | `breadcrumbs` | Whether the parent the trail needs is assigned |
| 22 · Accessibility | `accessibility` | Alt coverage, heading order, and non-descriptive link text |
| 23 · Media optimisation | `image-format`, `embedded-images`, `image-metadata` | Modern formats, no data-URI images, complete metadata |
| 25 · Content freshness | `content-freshness` | Age since last update, escalating past one year |

Four parameters — robots.txt, mobile responsiveness, page speed and analytics — are properties of the **deployment**, not of a record. They are reported so the review covers the whole checklist, but only what can be genuinely verified from the record is asserted: the origin's scheme is read from the canonical base URL, and everything requiring real measurement names the screen that measures it. Inventing a per-page signal for a site-wide fact would make the score dishonest.

Alongside these sit checks the checklist does not name but the domain requires: editorial and medical review readiness, author attribution, price or contact-for-price state, product specifications, service action links, broken-link detection, and orphan-page detection.

## Design decisions worth naming

**Metadata is not page copy.** The text extractor originally walked the stored `seo` object, which meant a focus keyphrase could satisfy the very checks meant to test whether the *visible* copy contained it — most visibly "keyphrase in a supporting heading", which always passed on static pages. Both `seo` and `review` are now excluded. Correcting this **lowered** some previously reported scores, because the earlier numbers were partly self-scored.

**A check must be actionable by someone.** A finding whose only remedy is a developer action says so explicitly rather than implying an admin field exists. On a fixed application route, for example, the URL check states that the route is defined in code and needs a redirect, instead of asking an editor to rename something they cannot reach.

**Free-form content is the body.** When a record uses the block composer, its blocks *are* its body — content depth, keyphrase placement, heading outline and coverage all score against what the page actually renders, not against a legacy field that is no longer displayed.

**AI eligibility is classified per check.** Each finding is marked either AI-repairable, with the specific field paths the model may touch, or manual-only. Structural and infrastructural findings — heading levels, slugs, canonicals, schema prerequisites, breadcrumb parents, freshness, HTTPS — are manual-only, so the assistant never offers an action it cannot honestly perform.

## Where it appears

| Surface | Role |
|---|---|
| SEO Review panel | Live score, search-result preview, colour-coded results, and a concrete action per finding, in English or Bengali |
| Target keyphrases | Focus keyphrase plus supporting phrases, with live coverage against the page's own copy |
| Page introduction | The search summary a page falls back to when no meta description is written |
| Inline summary meter | Character count, keyphrase presence and internal-link count under every summary field, updating as the author types |
| SEO workspace | Indexable-page inventory, publication results, Core Web Vitals history, and merchant status inside the protected CMS |

## Boundaries

- The analyzer measures **stored content**, not the rendered network response. Page weight, real-user performance and crawl behaviour are measured by the dedicated monitoring screens and external providers.
- A green score means the signals this application can verify are present. It does not predict ranking, and no part of the interface claims otherwise.
- Duplicate detection compares site-internal records. It is not a plagiarism or external-content check.
