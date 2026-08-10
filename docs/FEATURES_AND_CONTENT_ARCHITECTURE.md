# Care Bangla — Current Features and Content Architecture

> Last verified against the codebase: 10 August 2026  
> Scope: public site, logged-in user portal, admin CMS, API routes, MongoDB models, GridFS media, and static fallbacks.

This document is the current cross-cutting reference for features that affect more than one page or module. Service-specific pricing, booking, availability, payment, and applicant rules remain in the individual service workflow documents.

## 1. Experience map

The application now serves four connected experiences from one Next.js App Router project:

| Experience | Main routes | Responsibility |
|---|---|---|
| Public website | `/`, `/about`, `/service`, `/service/[serviceId]`, `/blog`, `/blog/[blogId]`, `/faq`, `/medical-shop/**` | Discovery, healthcare content, service booking entry points, blog reading, FAQ, and medical shop |
| Logged-in user portal | `/user/dashboard`, `/user/messages`, `/user/orders`, `/user/services`, `/user/profile` | Account-owned history, orders, services, and direct communication with the admin team |
| Admin panel | `/admin/**` | Content, services, bookings, applicants, users, shop, blog, FAQ, SEO, media, and message operations |
| Backend and persistence | `/api/**`, Mongoose models, GridFS | Authentication, validation, business rules, state transitions, structured content, and binary attachments |

The public site generally follows a DB-first/static-fallback strategy. Operational records—bookings, users, conversations, orders, and admin actions—always require MongoDB and never fall back to demo data.

## 2. Product and blog URL resilience

### 2.1 Product canonical URLs

The canonical product format is:

```text
/medical-shop/product/<categorySlug>/<productSlug>
```

`src/app/medical-shop/product/[...slug]/page.js` accepts both the canonical two-segment shape and the older one-segment shape. It resolves a product by ObjectId, current `slug`, or `previousSlugs`, then issues a permanent `308` redirect whenever the requested address is not the current canonical URL. This normalizes:

- legacy flat product URLs;
- ObjectId-based URLs;
- a former slug after an admin changes the public URL;
- a wrong or outdated category segment;
- a product moved to another category.

All storefront links should be built with `productUrl(product)` from `src/lib/productUrl.js`. Do not reconstruct product URLs from `name`, because the stored slug is the authoritative public identifier and may intentionally differ from the display name.

Product seed routes generate unique slugs. Product updates normalize and uniqueness-check the slug before saving, and preserve the outgoing slug in `previousSlugs`.

### 2.2 Missing, unpublished, and deleted products

An unavailable product no longer leaves the visitor on a dead detail page.

1. The admin may nominate up to three replacement products in ranked order through `RedirectTargetsPicker`.
2. The public API selects the first replacement that is still published.
3. If no replacement is available, the visitor is sent to the category from the requested URL or the hidden/deleted record.
4. If no category can be recovered, the visitor is sent to `/medical-shop/all-products`.
5. `MissingEntityNotice` explains the move and removes its temporary query parameters immediately so refreshes and shared URLs stay clean.

When a product is deleted, `preserveRedirectRule('product', record)` snapshots its title, category, and configured target IDs into `RedirectRule`. This is what lets the preference survive after the source document itself is gone.

### 2.3 Blog slug history and missing-post recovery

Blog posts use the same resilience principles:

- a changed slug is appended to `BlogPost.previousSlugs`;
- a request for a previous slug receives a permanent `308` to `/blog/<currentSlug>`;
- an unpublished post uses its live `redirectTo` preferences;
- a deleted post uses its preserved `RedirectRule` snapshot;
- the first still-published nominated article wins;
- otherwise the visitor is sent to `/blog`.

The shared `MissingEntityNotice` distinguishes a replacement detail page from a listing fallback and shows product-specific or article-specific copy for three seconds. The trigger parameters are `missing`, optional `pname`, and optional `to=alt`; the notice strips them with `router.replace(..., { scroll: false })` after reading them.

### 2.4 Redirect persistence model

`src/models/RedirectRule.js` stores:

| Field | Meaning |
|---|---|
| `entity` | `product` or `post` |
| `slug` | Dead public slug being recovered |
| `categorySlug` | Product category fallback; empty for blog posts |
| `title` | Human-readable source title for the notice |
| `targets` | Ordered replacement ObjectIds, maximum three |

Targets are ObjectIds, not slugs, so renaming a replacement does not break the rule. Every request re-checks publication state; an unavailable replacement is skipped rather than becoming a second dead end.

## 3. Medical Shop behavior

The Medical Shop is DB-first with bundled catalogue fallbacks for public presentation.

- Category listing: `/medical-shop/category/[categorySlug]`
- All products: `/medical-shop/all-products`
- Canonical product: `/medical-shop/product/[categorySlug]/[productSlug]`
- Cart and checkout: `/medical-shop/cart`, `/medical-shop/checkout`
- Product list API: `GET /api/shop/products`
- Product detail/hint API: `GET /api/shop/products/[idOrSlug]`

`ProductCard`, featured products, search results, metadata, JSON-LD, and redirect targets use the stored product slug and category through `productUrl()`. Cart snapshots currently retain the product ObjectId and category rather than the slug; their generated links still pass through `absoluteProductUrl()` and are canonicalized by the catch-all product route. This ObjectId fallback is intentional compatibility for already-saved carts, but new storefront callers should pass the full product object whenever it is available.

The product model supports purchase price, rental pricing and tiers, refill pricing, contact-for-price products, multi-image galleries, structured descriptions, linked specification values, stock state, featured state, analytics counters, publication state, order, slug history, and redirect preferences.

## 4. Blog platform

### 4.1 Blog listing page

`/blog` is composed from:

1. `PageBreadcrumb` using `PageContent('blog').pageBgImage`, with Settings fallback;
2. `ServiceIntro` for the two-row editorial introduction;
3. `BlogsSection1` for published article cards;
4. `BlogResources` for service cross-links, searchable topic chips, and a contact block.

The intro and resources content is edited from `/admin/blog` through `BlogSectionsEditor` and stored in the flexible `PageContent('blog')` document. Bundled defaults live in `src/data/blogPageSections.js`.

### 4.2 Individual article page

`BlogArticle` is the shared renderer for MongoDB posts and normalized static fallbacks. Optional blocks render only when they have content:

- article title, category, author, date, estimated reading time, and linked excerpt;
- featured image;
- key takeaways;
- alternating image-and-paragraph content sections;
- legacy/plain paragraph fallback;
- multi-image gallery;
- pull quote/testimonial;
- percentage/stat bars;
- tags and social sharing;
- service CTA;
- recent posts, categories, related posts, and optional sidebar service promotion.

The admin post editor also includes an SEO panel for focus keyword, supporting keywords, metadata overrides, canonical URL, robots directives, Open Graph overrides, and live content analysis. Article metadata and JSON-LD use plain text extracted from structured linked content and render-ready URLs extracted from structured images.

## 5. Internal Message module

### 5.1 Purpose and routes

The Message module is an internal conversation system between a registered user and Care Bangla admins.

| Side | UI route | API routes |
|---|---|---|
| User | `/user/messages` | `GET/POST /api/user/messages`, `GET/POST/PATCH /api/user/messages/[id]` |
| Admin | `/admin/messages` | `GET/POST /api/admin/messages`, `GET/POST/PATCH /api/admin/messages/[id]` |
| Shared attachment delivery | Conversation thread | `GET /api/internal-messages/attachments/[fileId]` |

Client caching and mutations are centralized in `src/store/api/messageApi.js`. RTK Query invalidates both list and individual-thread tags after create, reply, read, priority, or status operations.

### 5.2 Conversation data and workflow

`InternalConversation` stores a generated reference, owning user snapshot, subject, topic, optional custom topic, priority, status, creator, optional assigned admin, embedded messages, last-message summary, unread counters, and resolution metadata.

Topics:

- General Support
- Account & Profile
- Medical Shop Order
- Healthcare Service
- Payment & Invoice
- Feedback
- Other — requires a custom topic

Priorities are `normal`, `high`, and `urgent`. Statuses are `open`, `awaiting_admin`, `awaiting_user`, `resolved`, and `archived`.

User-created threads start as `awaiting_admin`; admin-created threads start as `awaiting_user`. A reply changes the state toward the other participant and updates the relevant unread counter. Admins can filter/search conversations and control priority and lifecycle status. Users can only read and reply to their own conversations.

### 5.3 Rich message body

The compose interface stores a safe structured format instead of arbitrary HTML:

- paragraph, bullet, or numbered blocks;
- left, center, or right alignment;
- bold, italic, and underline runs;
- small, normal, or large type;
- Arial, Georgia, Tahoma, or Times New Roman;
- validated six-digit hex colors.

The server sanitizes the format, caps it at 100 blocks, 500 runs, and 10,000 text characters, and also stores a plain-text body for previews and backward compatibility.

### 5.4 Attachments and authorization

Each message accepts up to five files, each no larger than 8 MB. Supported types are JPEG, PNG, WebP, GIF, PDF, DOC, DOCX, XLS, XLSX, and plain text.

Files are stored in GridFS with `scope: 'internal-message'`, conversation ID, uploader type, and uploader key metadata. Failed message persistence cleans up newly uploaded files. The attachment endpoint authorizes either a valid admin session or the user who owns the containing conversation, verifies the file is referenced by that conversation, and returns unknown/unauthorized files as not found. Images support authorized inline preview; documents download with their safe original names.

## 6. Shared content-authoring primitives

### 6.1 Safe inline hyperlinks

Narrative admin fields use `InlineLinkTextEditor`. Titles, button labels, tags, phone numbers, email addresses, postal addresses, URLs/slugs, identifiers, and other fields where inline links have no meaning remain plain inputs.

Editor workflow:

1. Select a word, phrase, or sentence.
2. Choose the globe/link action.
3. Enter an internal path, anchor, telephone/email link, or HTTP(S) URL.
4. Choose same-tab (`_self`) or new-tab (`_blank`).
5. Edit or remove saved links from the link chips below the field.

Storage is deliberately not HTML:

```js
{
  text: 'Talk to our nursing team today.',
  links: [{ start: 12, end: 24, href: '/service/nursing-care', target: '_self' }]
}
```

Plain legacy strings remain valid. `toInlineTextValue()` collapses content back to a string when no links exist. Link ranges are normalized, capped, required to be non-overlapping and within the text, and restricted to safe `/internal`, `#anchor`, `mailto:`, `tel:`, `http://`, or `https://` destinations. `InlineLinkedText` is the only public renderer; it adds `rel="noopener noreferrer"` for new-tab links.

The main helpers are in `src/lib/inlineLinks.js`; Zod validation lives in `src/lib/inlineLinkValidation.js` and `src/lib/validation.js`.

### 6.2 Structured image metadata

Image fields accept either a legacy URL string or:

```js
{
  src: '/api/media/<gridfs-id>',
  alt: 'Nurse assisting an elderly patient at home',
  title: 'Home nursing care in Dhaka',
  fileName: 'home-nursing-care.webp'
}
```

The shared admin image controls provide:

- upload or media-library selection;
- a read-only source URL;
- alt text with accessibility guidance;
- optional title text;
- a normalized SEO file name;
- completeness feedback and a file-name-to-alt shortcut;
- per-image metadata in single- and multi-image editors.

`src/lib/imageMeta.js` supplies the compatibility contract:

- `normalizeImage()` — string/object to one stable shape;
- `toImageValue()` — collapse metadata-free values to legacy strings;
- `imageSrc()` — render-ready URL;
- `imageAttrs()` — safe `src`, `alt`, and optional `title`;
- `hasImage()` and `pickImage()` — safe presence/fallback checks;
- `resolveCmsImages()` — flatten structured values for legacy presentation props;
- `imageMetaScore()` — admin completeness status.

For GridFS images, `fileName` is appended only at render time, producing `/api/media/<id>/<descriptive-name.ext>`. The stored source remains stable, and `src/app/api/media/[id]/[[...seo]]/route.js` serves both URL forms.

## 7. Current page compositions

### 7.1 About page

`/about` and `/bn/about` use the same content shape. Current order:

1. breadcrumb/banner;
2. Mission and Vision introduction using `ServiceIntro`;
3. CEO & Founder message;
4. Our Team department presentation;
5. shared Counter section (same data source as the homepage);
6. blue CTA section;
7. bottom book-appointment bar.

The CEO and Founder are represented as one person. Up to three admin-managed photos fade automatically every 30 seconds without buttons, dots, swipes, or slider gestures. The department section represents HR & Management, IT & Developer, and Delivery & Helping Staff as teams rather than exposing individual internal employee profiles.

All About-specific fields live in `PageContent('about')` and are edited at `/admin/content/about`. Counter content is shared from `PageContent('home')`, preventing About and Home statistics from drifting apart.

### 7.2 All Services page

`/service` uses:

1. breadcrumb;
2. `ServiceIntro`;
3. `ServiceProcess` (“How it works”) as a responsive non-sliding card grid;
4. `ServiceHighlights`;
5. `ServiceStats`;
6. the same `Service` offerings component used on the homepage;
7. `ServiceFaq`;
8. `ServiceCta`.

The page is edited through `/admin/content/services` and stored as `PageContent('services')` with bundled resolver defaults.

### 7.3 Bespoke service detail pages

The route dispatcher in `src/app/service/[serviceId]/page.js` selects bespoke pages for Doctor Consultation, Nursing Care, Caregiver Service, Ambulance Service, Baby Care, and Physiotherapy. Other MongoDB services use `ServiceDetailsDynamic`.

For every bookable bespoke service, the booking-tier card section now comes immediately after the intro title and description:

| Service | Opening order after breadcrumb |
|---|---|
| Nursing Care | Intro → Nursing Care Tiers → Service Preview rows → Services & Solutions |
| Caregiver Service | Intro → Attendant Tier → Service Preview rows → Services & Solutions |
| Baby & Newborn Care | Intro → Nany Tier → Service Preview rows → Services & Solutions |
| Physiotherapy | Intro → Specialist Tier → Service Preview rows → Services & Solutions |
| Doctor Consultation | Intro → Consultation Tiers → Services & Solutions → Consultation Modes |
| Ambulance | Intro → ambulance types; call-to-contract only, with no public booking tier |

`ServicePreviewGallery.jsx` is intentionally not a slider despite its historical data naming. It renders every item as an alternating image/text row, uses each item’s former tab title as the small blue eyebrow, adds an admin-editable heading, supports linked description text, and places mobile copy inside an accessible expand/collapse disclosure. There are no slide buttons, gestures, autoplay, or tab state.

### 7.4 FAQ page

`/faq` is DB-first with static fallback data and emits `FAQPage` JSON-LD. Published `FaqCategory` records define the filter chips; published `FaqItem` records define ordered questions and answers. The admin page at `/admin/faq` supports:

- category create/edit/delete, publish state, and drag ordering;
- question create/edit/delete, category assignment, publish state, and per-category drag ordering;
- safe inline links in answer text;
- a CMS-managed breadcrumb image through `PageContent('faq')`.

## 8. State, storage, and API ownership

| Concern | State/persistence owner | Notes |
|---|---|---|
| Public MongoDB reads | Server Components and RTK Query | DB-first content with explicit static fallbacks where applicable |
| Internal messages | RTK Query `messageApi` + `InternalConversation` | Tag invalidation keeps list/thread/unread state synchronized |
| Shopping cart | `CartContext` + localStorage | Client-owned until order submission |
| Public/user language | `LanguageProvider`, key `cb_lang` | Shared by public pages and the logged-in user portal |
| Admin language | nested `LanguageProvider`, key `cb_admin_lang` | Independent from public/user language in the same browser |
| Flexible page CMS | `PageContent` | Keys include `home`, `about`, `contact`, `settings`, `services`, `blog`, service slugs, and `faq` |
| Images/documents | GridFS | Public media and conversation attachments use different authorization rules |
| URL history | `previousSlugs` on Product/BlogPost | Resolves renamed live records |
| Deleted URL preferences | `RedirectRule` | Preserves replacement targets after source deletion |

## 9. Maintenance rules

### When changing a product URL

- Change the explicit Product URL field in the admin editor; do not derive it in a caller from the product name.
- Let the update API normalize the slug and append the old value to `previousSlugs`.
- Verify both the old and new public URLs; the old one should return a `308` to the new category-aware URL.

### Before hiding or deleting a product/post

- Optionally select up to three ordered alternatives.
- Ensure alternatives are published.
- Test the unavailable URL after the change and confirm either the first live alternative or the correct listing opens with the explanatory notice.

### When adding a new narrative admin field

- Use `InlineLinkTextEditor` when an inline hyperlink has semantic value.
- Keep identifiers, names/titles, tags, button labels, phone/email/address, and raw URL fields plain.
- Accept both string and structured values in the schema/validation layer.
- Render with `InlineLinkedText`; use `inlineTextToPlainText()` for metadata, search, comparisons, exports, or JSON-LD.

### When adding a new image field

- Use the shared image editor and metadata controls.
- Make the Mongoose/Zod field accept legacy string or structured image values.
- Render through `imageAttrs()`, `imageSrc()`, `pickImage()`, or `resolveCmsImages()` as appropriate.
- Never interpolate the structured object directly into `src`, CSS `url()`, metadata, email, or JSON-LD.

### When adding a Message attachment type

- Update the MIME allowlist and extension mapping together.
- Preserve the five-file and 8 MB limits unless product requirements explicitly change.
- Keep attachment delivery behind conversation authorization; never reuse the public media route for private message files.

## 10. Related documents

- [`README.md`](../README.md) — project overview, routes, APIs, database, setup, and deployment.
- [`Nursing_Service_Workflow.md`](Nursing_Service_Workflow.md)
- [`Caregiver_Service_Workflow.md`](Caregiver_Service_Workflow.md)
- [`BabyNewBornCare_Service_Workflow.md`](BabyNewBornCare_Service_Workflow.md)
- [`Physiotherapy_Service_Workflow.md`](Physiotherapy_Service_Workflow.md)
- [`Doctor_Consultation_Service_Workflow.md`](Doctor_Consultation_Service_Workflow.md)
- [`Language_Translation_Process.md`](Language_Translation_Process.md) and [`TRANSLATION_GUIDE.md`](TRANSLATION_GUIDE.md)
- [`SEO_Optimization_RoadMap.md`](SEO_Optimization_RoadMap.md)
- [`PROJECT_AUDIT.md`](PROJECT_AUDIT.md) — historical audit baseline; validate each finding against current code before acting.
