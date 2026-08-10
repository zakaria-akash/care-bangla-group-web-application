# Care Bangla - Next.js App Structure Upgrade Plan

This document began as the strategic roadmap for migrating the original Vite React template to Next.js. The migration, MongoDB backend, admin CMS, logged-in user portal, and many domain modules are now implemented; the early phases below are retained as historical design rationale rather than a description of unfinished work.

> **Current implementation snapshot — 10 August 2026:** Next.js 16 App Router, MongoDB/Mongoose, GridFS, admin and user shells, RTK Query, bilingual scoped translation, service booking modules, Medical Shop, Blog, FAQ, internal Message, structured image metadata, safe inline links, and resilient product/blog URLs are present. See the root [README.md](../README.md) and [FEATURES_AND_CONTENT_ARCHITECTURE.md](FEATURES_AND_CONTENT_ARCHITECTURE.md) for the current source of truth.

> **PWA status:** Phase 4 below is historical. The incompatible `next-pwa` integration has been removed; `public/sw.js` only unregisters the legacy worker and clears its stale-cache path. The web manifest remains, but offline caching is not currently active.

---

## Strategic Objectives
- **Security:** Implement server-side protection, secure headers, and robust authentication.
- **Performance:** Achieve sub-second page loads via SSR, ISR, and optimized asset handling.
- **Offline Readiness:** Enable PWA features for offline browsing and resource caching.
- **SEO:** Leverage Next.js Metadata API for maximum search engine visibility.
- **Scalability:** Prepare for Node.js/MongoDB backend integration with a dedicated admin suite.

---

## Phase 0: Foundation & Environment Setup
*Focus: Aligning dependencies and initializing the modern stack.*

- **Next.js Integration:** Use the App Router architecture for better performance and nested layouts.
- **JavaScript Modernization:** Maintain the app in JavaScript, using ES6+ features and modern React patterns.
- **Sass Optimization:** Maintain existing SCSS logic but migrate to CSS Modules or Global SCSS imports within Next.js.
- **Environment Management:** Set up strictly typed `.env` files for future API keys and database URIs.

## Phase 1: Structural Migration (App Router)
*Focus: Moving from Client-Side Routing to Server-Side Layouts.*

- **Layout Architecture:** Define a root `layout.tsx` for global elements (Header/Footer) and nested layouts for specific sections.
- **Component Refactoring:** Distinguish between **Server Components** (default) for SEO/Data fetching and **Client Components** (`'use client'`) for interactivity (Slick sliders, Mobile menus).
- **Static Assets:** Move `public/assets` to the Next.js `public` directory and implement `next/image` for automatic format conversion and lazy loading.

## Phase 2: SEO & Performance Optimization
*Focus: Speed and Visibility.*

- **Metadata API:** Implement dynamic `generateMetadata` for every page to optimize titles, descriptions, and OpenGraph tags.
- **Font Optimization:** Use `next/font/google` to optimize Poppins and Rubik fonts, eliminating layout shifts.
- **Image Optimization:** Replace all `<img>` tags with `<Image />` to benefit from WebP/AVIF compression and responsive sizing.
- **Streaming & Suspense:** Implement `loading.tsx` files for skeleton screens while data is being prepared.

## Phase 3: Advanced Security & Middleware
*Focus: Protecting the Application.*

- **Secure Headers:** Use Next.js Middleware to inject CSP (Content Security Policy), X-Frame-Options, and HSTS headers.
- **CSRF Protection:** Implement tokens for all form submissions (Appointment, Contact).
- **Route Protection:** Use Middleware to intercept unauthorized access to future admin routes.
- **Input Validation:** Set up Zod for schema validation on all upcoming API endpoints.

## Phase 4: Offline Capabilities (PWA)
*Focus: Resilience and Mobile Experience.*

- **Next-PWA Integration:** Configure `next-pwa` to generate a Service Worker.
- **Caching Strategy:** Implement "Stale-While-Revalidate" for static assets and images.
- **Manifest Setup:** Create a `manifest.json` for "Add to Home Screen" functionality, making the app feel native on mobile devices.

## Phase 5: Backend & Admin Dashboard Architecture
*Focus: Preparing for Node.js, MongoDB, and CMS functionality.*

- **API Route Design:** Establish `app/api/` folder structure for RESTful endpoints handling MongoDB operations via Mongoose.
- **Admin Layout:** Create a dedicated `(admin)` route group with its own layout for the dashboard.
- **Dynamic Table Engine:** Design a reusable "DataTable" component that fetches data from MongoDB and allows CRUD operations (Create, Read, Update, Delete) for every page's content.
  > **Implementation note (superseded for Products):** Blog, Team, Services, and Categories use a shared Ant Design `Table` pattern. The Shop Products manager was instead built as a bespoke per-category card-grid UI: a category selector page (with live product-count stats from `/api/admin/shop/products/counts`) leads to a full-featured card manager with inline order editing, multi-select bulk delete, drag-and-drop reordering via `@dnd-kit`, and silent (no-spinner) mutations via `/api/admin/shop/products/batch-reorder`. A shared DataTable abstraction was deliberately not extracted; the specificity of the product workflow didn't justify a generic abstraction at this stage.
- **Authentication:** Prepare for NextAuth.js or JWT-based login for the admin side.

**Implemented beyond the original Phase 5 scope:** customer authentication and `/user/**` portal; unified booking/applicant/user history consoles; internal user↔admin Message conversations with rich formatting and private GridFS attachments; FAQ CMS; bespoke service CMS modules; rich Blog authoring and SEO analysis; category-aware product URLs and redirect recovery; structured `{ src, alt, title, fileName }` images; and safe `{ text, links }` narrative content.

---

**Current next steps:** use the remediation priorities in Phase 6 for performance work, the remaining tasks in [SEO_Optimization_RoadMap.md](SEO_Optimization_RoadMap.md) for search visibility, and the verified maintenance rules in [FEATURES_AND_CONTENT_ARCHITECTURE.md](FEATURES_AND_CONTENT_ARCHITECTURE.md) when extending the CMS.

---

## Phase 6: Performance Audit — Root Causes & Remediation Plan

*Focus: Eliminating the sources of slow `next dev` compile times and slow first-page-load on both the public site and the admin panel.*

This phase is a **historical diagnostic snapshot** from before the August 2026 feature work. Re-measure each item against the current branch before implementation; file counts, component names, and route shapes may have changed. No remediation status should be inferred from this table alone.

---

### 6.1 Dev-Server Compilation Slowness

These causes affect how long `next dev` takes to start up and how long the first page request takes to compile the route bundle.

| # | Root Cause | File / Location | Impact |
|---|------------|-----------------|--------|
| 1 | **Turbopack filesystem cache disabled** | `next.config.mjs` → `turbopackFileSystemCacheForDev: false` | Every `next dev` start re-compiles the entire module graph from scratch in memory. On a cold start this is the single biggest factor — no warm cache means the full dependency tree is re-transformed each time. The setting was forced off to avoid cache-corruption crashes on Windows, but it removes the primary speedup that Turbopack offers in dev. |
| 2 | **`transpilePackages` transforms the entire Ant Design ecosystem** | `next.config.mjs` → `transpilePackages: ['antd', '@ant-design/icons', 'rc-util', 'rc-pagination', 'rc-picker']` | Next.js transpiles every module in these packages through its Babel/SWC pipeline on each cold start. `antd` alone is ~6 MB of source; `@ant-design/icons` contains thousands of individual SVG icon modules. Tree-shaking only eliminates unused icons at production build — in dev, all icons are loaded. |
| 3 | **`next-pwa` wraps the entire config** | `next.config.mjs` → `withPWA(nextConfig)` | Even though PWA is disabled in dev (`disable: process.env.NODE_ENV === 'development'`), the `withPWA` initializer still runs, intercepts the webpack/Turbopack config, patches several module rules, and checks for an existing service worker on every start. This adds a non-trivial synchronous cost to the config-evaluation phase. |
| 4 | **Monolithic SCSS bundle (20+ partials, imported globally)** | `src/sass/style.scss` + `src/app/layout.js` | A single root CSS entry point imports every SCSS partial — fonts, variables, typography, layout, header, footer, all shortcodes, shop styles, user-portal styles — into one bundle. The Sass compiler must resolve and transpile this entire graph on the first route compile. There is no CSS code-splitting; every page, including lightweight admin pages, receives the full stylesheet. |
| 5 | **Bootstrap and Ant Design CSS both loaded globally** | `src/app/layout.js` → `import 'bootstrap/dist/css/bootstrap.min.css'` | Bootstrap (~30 KB minified) is imported in the root layout, so every admin page inherits it even though admin UI is entirely Ant Design. Two separate CSS frameworks are processed together, adding parse cost and creating specificity conflicts that Sass has to work around. |
| 6 | **Vite devDependencies still installed** | `package.json` → `vite`, `@vitejs/plugin-react` | These are left over from the original template. They add ~50 MB to `node_modules` and are processed during `npm install` and certain IDE tooling scans, but they do not directly affect Next.js compilation. The risk is that a developer accidentally invokes the Vite dev server instead of `next dev`. |
| 7 | **`pageExtensions` missing `jsx`** | `next.config.mjs` → `pageExtensions: ['js', 'ts', 'tsx']` | `jsx` is absent from `pageExtensions`. While this does not break component imports (Next.js processes any imported `.jsx` file regardless), it means any accidental route file saved as `.jsx` would be silently ignored rather than compiled as a route, creating confusing debugging sessions. |

---

### 6.2 Public Page — First Load Latency

These causes affect how long it takes for a user to see content after hitting a public URL.

| # | Root Cause | File / Location | Impact |
|---|------------|-----------------|--------|
| 1 | **Fonts served as uncompressed TTF from `/assets/fonts/`** | `src/sass/default/_fonts.scss` | 8 `@font-face` declarations (4 weights × Poppins + Rubik) reference `.ttf` files. TTF has no compression; the equivalent WOFF2 files would be 30–40% smaller. On a slow connection, these block text rendering (FOIT/FOUT) until all font weights download. They are also served as static files with no cache-busting hash, so browsers re-validate them on every visit. `next/font` is not used for these fonts — only for Hind Siliguri — so there is no preload hint injected into `<head>` for Poppins or Rubik. |
| 2 | **Every GridFS image hits the full Next.js API pipeline on first load** | `src/app/api/media/[id]/route.js` → `export const dynamic = 'force-dynamic'` | `force-dynamic` opts the media route out of all Next.js caching. Every image request — for each blog thumbnail, team photo, hero banner — goes: browser → Next.js handler → `connectDB()` → MongoDB GridFS stream. On a page with 10+ images, that is 10 serial (or burst) round-trips to MongoDB Atlas. The browser `Cache-Control: immutable` header helps on repeat visits, but the first paint is gated on all of them. There is no CDN or edge layer in front of GridFS. |
| 3 | **Root layout blocks on a MongoDB query before any page renders** | `src/app/layout.js` → `getLayoutData()` / `unstable_cache` | The root layout must resolve `connectDB()` + `PageContent.findOne()` + `Service.find()` before it can stream any HTML to the browser. The first request after a cold DB connection (e.g. after `next dev` start or MongoDB Atlas auto-pause) adds 200–600 ms of TCP/TLS handshake and authentication latency before any byte is sent. `unstable_cache` with a 300-second TTL only helps after the first request warms the cache. |
| 4 | **Entire home page is a `'use client'` component** | `src/Pages/HomePage/MainHome.jsx` | `MainHome.jsx` is marked `'use client'` so it can use `useLanguage()` context. The server component (`page.js`) fetches data and serialises it to JSON, but the full home page client bundle — including all 13 `dynamic()` imports — must be shipped to and parsed by the browser before interactivity. The initial server-rendered HTML is correct, but Time-to-Interactive is gated on the full React hydration of a very large component tree. |
| 5 | **`Section` component is dynamically imported inside `MainHome`** | `src/Pages/HomePage/MainHome.jsx` → `const Section = dynamic(...)` | `Section` is a thin layout wrapper used as a structural container around every section of the page. Lazy-loading it means that until the dynamic import resolves, none of the below-fold section containers exist in the DOM — which can cause layout shifts and delayed rendering of child sections that depend on it. |
| 6 | **AOS animation library configured with 1 500 ms duration** | `src/app/ClientInit.js` → `Aos.init({ duration: 1500 })` | Every element with a `data-aos` attribute is invisible until it enters the viewport AND the 1 500 ms animation completes. On low-powered devices this compounds with hydration time: content that is technically rendered is visually hidden for up to 1.5 s after the page loads. |
| 7 | **No `<img>` tags replaced with `next/image`** | Throughout `src/Components/`, `src/Pages/` (~94 raw `<img>` tags) | Raw `<img>` tags have no built-in lazy loading (browser default varies by vendor), no WebP/AVIF conversion, no responsive `srcset`, and no LCP hint for the largest image on the page. Hero images and team photos are above the fold and downloaded at full original resolution regardless of the user's screen width. |
| 8 | **Multiple separate `PageContent` queries per server component page** | e.g. `src/app/service/page.js`, `src/app/blog/page.js` | Pages now fetch both `page: 'services'` and `page: 'settings'` in a `Promise.all`. While parallel, each is a distinct MongoDB round-trip. A single query with `{ page: { $in: ['services', 'settings'] } }` would halve the DB round-trips for those pages. |
| 9 | **Redux `StoreProvider` and `CartProvider` mounted on every page** | `src/app/layout.js` | `StoreProvider` initialises the RTK Query store (including middleware) and `CartProvider` mounts a context with local-storage persistence on every page — including purely informational pages (About, Contact, Blog) that make no shop queries. This adds hydration overhead even when the user has no interaction with either system. |
| 10 | **`FloatingChatWidget` polls the server every 4 seconds when open** | `src/Components/FloatingChatWidget/index.jsx` → `POLL_MS = 4000` | The widget is mounted in the root layout and included on every public page. While polling only starts when the chat is open, the widget itself calls `useSession()` and runs path-detection logic on every route change, adding a small but measurable overhead to every navigation event. |

---

### 6.3 Admin Panel — Load Latency

| # | Root Cause | File / Location | Impact |
|---|------------|-----------------|--------|
| 1 | **All admin pages are `'use client'` with no server-side prefetch** | `src/app/admin/*/page.js` — all files | Every admin page renders an initial loading spinner while it fires its own `fetch('/api/admin/...')` call after mount. The user sees a blank skeleton for 200–500 ms on every navigation. Since the Next.js App Router can run server components inside `/admin`, the data could be prefetched on the server and passed as props — eliminating the spinner entirely. |
| 2 | **`@ant-design/icons` loads all icons in dev** | `src/Components/Admin/AdminClientLayout.jsx` and all admin pages | Individual icon components (`DashboardOutlined`, `EditOutlined`, etc.) are imported by name, but in dev mode (no tree-shaking), the entire `@ant-design/icons` package initialises. This package contains ~800 icon modules, each a small React component wrapping an SVG. During cold compilation of the first admin route, all 800 modules are transformed. |
| 3 | **Admin layout wraps an entire client component tree** | `src/app/admin/layout.js` → `<AdminClientLayout>` | `AdminClientLayout` is `'use client'` and contains all sidebar, header, and navigation logic. Because the layout is a client component, every admin page rendered inside it is also forced into the client component tree — none can be server components even if they only display static data. |
| 4 | **No `loading.js` for any admin route** | `src/app/admin/*/` — no `loading.js` files exist | Without a `loading.js`, React Suspense boundaries are not placed automatically around admin page content. When navigating between admin pages, the old page content stays visible until the new page's `useEffect` fetch completes and state updates. There is no progressive skeleton, so users perceive navigation as frozen. |
| 5 | **`AdminClientLayout` checks auth and fetches stats on every mount** | `src/Components/Admin/AdminClientLayout.jsx` | The layout fetches `/api/admin/auth/me` and `/api/admin/stats` on every mount (including soft navigations that re-mount the layout). On a slow connection this delays the sidebar from rendering and triggers additional spinner states. |
| 6 | **`transpilePackages` for Ant Design applies to every route** | `next.config.mjs` | The `transpilePackages` list is global — it applies even to public routes that import zero Ant Design code. This means the Ant Design transform pass runs across the full module resolution graph, not just admin routes. |

---

### 6.4 Remediation Priority Order

The following fixes are ordered by estimated impact-to-effort ratio. **No implementation has been done yet — this is the plan only.**

1. **Re-enable Turbopack filesystem cache with a dedicated `.next/cache` path** *(Compile speed — highest gain)*
   Convert to a stable non-default cache directory so Windows kill-signals cannot corrupt it.

2. **Serve fonts as WOFF2 and add `next/font` preload for Poppins & Rubik** *(First paint)*
   Either convert TTF files to WOFF2 in-place, or migrate to `next/font/local` so Next.js auto-injects `<link rel="preload">` for the critical font weights.

3. **Replace `<img>` with `next/image` for all above-the-fold images** *(LCP / Core Web Vitals)*
   Hero banners and team photos. This is the single biggest Core Web Vitals win and was deferred in the earlier plan.

4. **Add an image CDN or a caching proxy in front of the `/api/media/[id]` route** *(Repeat-visit image load)*
   Change `force-dynamic` to `force-static` and add `Cache-Control` to a reverse proxy (Cloudflare, Vercel Edge, or even a simple Next.js `rewrite` with ISR) so GridFS images are edge-cached after the first request.

5. **Convert admin pages to server components with thin client shells** *(Admin first paint)*
   The list views (services, team, blog, products) only need a client component for the action buttons — the data table itself can be a server component with `await fetch(...)` at render time.

6. **Add `loading.js` to every admin route segment** *(Admin navigation feel)*
   Instant skeleton screens make navigation feel synchronous even on slow connections.

7. **Move Bootstrap import to public-only layout; remove it from admin routes** *(Bundle size)*
   Use a route group `(public)` layout for Bootstrap/global CSS and a separate `(admin)` layout that only loads Ant Design styles.

8. **Replace multiple `PageContent` queries with a single `$in` query** *(DB round-trips)*
   One MongoDB call instead of two per page that needs both `settings` and page-specific content.

9. **Replace RTK Query with `fetch` + React `cache()` for read-only public data** *(Bundle size + hydration)*
   Pages that only read team members, services, or blog posts do not need a Redux store. `React.cache()` and Next.js `fetch()` with `revalidate` handles deduplication natively.

10. **Remove `react-router-dom` and `vite` from `package.json`** *(Bundle hygiene)*
    These Vite-era packages add dead weight to `node_modules` and risk accidentally being imported.
