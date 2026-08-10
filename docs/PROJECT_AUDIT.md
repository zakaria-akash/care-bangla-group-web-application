<!--
  PROJECT_AUDIT.md — Care Bangla Full-Stack Audit & Improvement Roadmap
  ─────────────────────────────────────────────────────────────────────
  Best viewed in: VS Code → (Ctrl+Shift+V) Markdown Preview
                  JetBrains IDEs → Markdown preview pane
                  Any Markdown viewer that renders embedded HTML

  Note: GitHub.com strips <style> tags for security — the structure
  remains readable there but without colour coding.
-->

<title>Care Bangla — Project Audit & Improvement Roadmap</title>
<style>
:root {
  --bg:#080F1E;--surface:#0F1D35;--s2:#162440;
  --border:rgba(62,207,244,.1);--bdim:rgba(255,255,255,.06);
  --tx:#E2EAF5;--tx2:#7A94B8;--tx3:#4A6080;
  --ac:#3ECFF4;--acd:rgba(62,207,244,.12);
  --crit:#FF4757;--critb:rgba(255,71,87,.1);
  --high:#FF8C42;--highb:rgba(255,140,66,.1);
  --med:#F5C842;--medb:rgba(245,200,66,.1);
  --low:#4CAF82;--lowb:rgba(76,175,130,.1);
  --inf:#7A94B8;--infb:rgba(122,148,184,.1);
  --sans:system-ui,-apple-system,'Segoe UI',sans-serif;
  --mono:'JetBrains Mono','Fira Code','Cascadia Code',Consolas,ui-monospace,monospace;
  --r:6px;--rl:10px;
}
@media(prefers-color-scheme:light){:root{
  --bg:#EEF3FA;--surface:#fff;--s2:#E4ECF6;
  --border:rgba(14,165,233,.15);--bdim:rgba(0,0,0,.07);
  --tx:#0A1628;--tx2:#4A6080;--tx3:#8AA0C0;
  --ac:#0EA5E9;--acd:rgba(14,165,233,.09);
  --crit:#DC2626;--critb:rgba(220,38,38,.08);
  --high:#EA580C;--highb:rgba(234,88,12,.08);
  --med:#B45309;--medb:rgba(180,83,9,.08);
  --low:#16A34A;--lowb:rgba(22,163,74,.08);
  --inf:#4A6080;--infb:rgba(74,96,128,.07);
}}
:root[data-theme="light"]{
  --bg:#EEF3FA;--surface:#fff;--s2:#E4ECF6;
  --border:rgba(14,165,233,.15);--bdim:rgba(0,0,0,.07);
  --tx:#0A1628;--tx2:#4A6080;--tx3:#8AA0C0;
  --ac:#0EA5E9;--acd:rgba(14,165,233,.09);
  --crit:#DC2626;--critb:rgba(220,38,38,.08);
  --high:#EA580C;--highb:rgba(234,88,12,.08);
  --med:#B45309;--medb:rgba(180,83,9,.08);
  --low:#16A34A;--lowb:rgba(22,163,74,.08);
  --inf:#4A6080;--infb:rgba(74,96,128,.07);
}
:root[data-theme="dark"]{
  --bg:#080F1E;--surface:#0F1D35;--s2:#162440;
  --border:rgba(62,207,244,.1);--bdim:rgba(255,255,255,.06);
  --tx:#E2EAF5;--tx2:#7A94B8;--tx3:#4A6080;
  --ac:#3ECFF4;--acd:rgba(62,207,244,.12);
  --crit:#FF4757;--critb:rgba(255,71,87,.1);
  --high:#FF8C42;--highb:rgba(255,140,66,.1);
  --med:#F5C842;--medb:rgba(245,200,66,.1);
  --low:#4CAF82;--lowb:rgba(76,175,130,.1);
  --inf:#7A94B8;--infb:rgba(122,148,184,.1);
}
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
html{scroll-behavior:smooth}
body{background:var(--bg);color:var(--tx);font-family:var(--sans);font-size:.875rem;line-height:1.65;min-height:100vh}
a{color:var(--ac);text-decoration:none}
code{font-family:var(--mono);font-size:.78em;background:var(--s2);padding:.1rem .35rem;border-radius:3px;color:var(--ac)}
.wrap{max-width:1120px;margin:0 auto;padding:0 1.25rem}
.grid{display:grid;grid-template-columns:210px 1fr;gap:0 2.5rem}
.site-hd{padding:2rem 0 1.5rem;border-bottom:1px solid var(--bdim);margin-bottom:2rem;grid-column:1/-1;display:flex;align-items:flex-start;justify-content:space-between;gap:1.5rem;flex-wrap:wrap}
.hd-eye{font-family:var(--mono);font-size:.62rem;letter-spacing:.14em;text-transform:uppercase;color:var(--ac);margin-bottom:.35rem}
.hd-title{font-size:1.75rem;font-weight:800;letter-spacing:-.035em;line-height:1.15;text-wrap:balance}
.hd-sub{color:var(--tx2);font-size:.8rem;margin-top:.3rem}
.chips{display:flex;gap:.5rem;flex-wrap:wrap;align-items:center;flex-shrink:0;padding-top:.15rem}
.chip{display:inline-flex;align-items:center;gap:.4rem;padding:.35rem .7rem;border-radius:100px;font-family:var(--mono);font-size:.7rem;font-weight:700;border:1px solid;white-space:nowrap}
.chip.cr{color:var(--crit);background:var(--critb);border-color:var(--crit)}
.chip.hi{color:var(--high);background:var(--highb);border-color:var(--high)}
.chip.md{color:var(--med);background:var(--medb);border-color:var(--med)}
.chip.lo{color:var(--low);background:var(--lowb);border-color:var(--low)}
.chip-dot{width:6px;height:6px;border-radius:50%;background:currentColor;flex-shrink:0}
.sidebar{position:sticky;top:1.5rem;align-self:start;max-height:calc(100vh - 3rem);overflow-y:auto;padding-bottom:3rem}
.sidebar::-webkit-scrollbar{width:3px}
.sidebar::-webkit-scrollbar-thumb{background:var(--bdim);border-radius:2px}
.nav-grp{margin-bottom:1.25rem}
.nav-grp-lbl{font-family:var(--mono);font-size:.58rem;letter-spacing:.16em;text-transform:uppercase;color:var(--tx3);padding:0 .5rem;margin-bottom:.3rem;display:block}
.nav-a{display:flex;align-items:center;gap:.45rem;padding:.32rem .5rem;border-radius:var(--r);font-size:.78rem;color:var(--tx2);cursor:pointer;transition:background .14s,color .14s}
.nav-a:hover{background:var(--surface);color:var(--tx)}
.nav-a.on{background:var(--acd);color:var(--ac)}
.ndot{width:6px;height:6px;border-radius:50%;flex-shrink:0}
.ndot.cr{background:var(--crit)}.ndot.hi{background:var(--high)}.ndot.md{background:var(--med)}.ndot.lo{background:var(--low)}.ndot.ac{background:var(--ac)}.ndot.fe{background:var(--low)}.ndot.in{background:var(--inf)}
.main{min-width:0}
.sec{margin-bottom:3.5rem;scroll-margin-top:2rem}
.sec-hd{display:flex;align-items:center;gap:.7rem;margin-bottom:1.25rem;padding-bottom:.75rem;border-bottom:1px solid var(--bdim)}
.sec-title{font-size:1.1rem;font-weight:700;letter-spacing:-.02em}
.sec-tag{font-family:var(--mono);font-size:.58rem;font-weight:700;padding:.18rem .55rem;border-radius:100px;letter-spacing:.08em;text-transform:uppercase}
.sec-tag.sec{background:var(--critb);color:var(--crit)}
.sec-tag.rel{background:var(--highb);color:var(--high)}
.sec-tag.prf{background:var(--medb);color:var(--med)}
.sec-tag.arc{background:var(--acd);color:var(--ac)}
.sec-tag.fea{background:var(--lowb);color:var(--low)}
.grp-lbl{font-family:var(--mono);font-size:.6rem;letter-spacing:.14em;text-transform:uppercase;padding:.1rem 0 .55rem;display:flex;align-items:center;gap:.55rem;margin-bottom:.1rem}
.grp-lbl::after{content:'';flex:1;height:1px;background:var(--bdim)}
.grp-lbl.cr{color:var(--crit)}.grp-lbl.hi{color:var(--high)}.grp-lbl.md{color:var(--med)}.grp-lbl.lo{color:var(--low)}
.fc{background:var(--surface);border:1px solid var(--bdim);border-radius:var(--rl);overflow:hidden;margin-bottom:.7rem;display:grid;grid-template-columns:4px 1fr}
.stripe{display:block}
.stripe.cr{background:var(--crit)}.stripe.hi{background:var(--high)}.stripe.md{background:var(--med)}.stripe.lo{background:var(--low)}.stripe.ac{background:var(--ac)}.stripe.in{background:var(--inf)}
.fb{padding:.85rem 1rem}
.fm{display:flex;align-items:center;gap:.55rem;margin-bottom:.4rem;flex-wrap:wrap}
.fid{font-family:var(--mono);font-size:.6rem;color:var(--tx3);letter-spacing:.04em}
.sbadge{font-family:var(--mono);font-size:.58rem;font-weight:700;letter-spacing:.1em;text-transform:uppercase;padding:.15rem .5rem;border-radius:3px}
.sbadge.cr{background:var(--critb);color:var(--crit)}.sbadge.hi{background:var(--highb);color:var(--high)}.sbadge.md{background:var(--medb);color:var(--med)}.sbadge.lo{background:var(--lowb);color:var(--low)}.sbadge.ac{background:var(--acd);color:var(--ac)}
.ft{font-size:.88rem;font-weight:600;color:var(--tx);margin-bottom:.3rem;line-height:1.35}
.fd{font-size:.8rem;color:var(--tx2);line-height:1.55;margin-bottom:.4rem}
.fp{font-family:var(--mono);font-size:.68rem;color:var(--ac);background:var(--acd);padding:.18rem .5rem;border-radius:4px;display:inline-block;margin-bottom:.45rem}
.fix{margin-top:.5rem;padding:.55rem .75rem;background:var(--s2);border-radius:var(--r);border-left:2px solid var(--low)}
.fix-lbl{font-family:var(--mono);font-size:.58rem;letter-spacing:.12em;text-transform:uppercase;color:var(--low);display:block;margin-bottom:.2rem;font-weight:700}
.fix p{font-size:.78rem;color:var(--tx2);line-height:1.5}
.tgrid{display:grid;grid-template-columns:repeat(auto-fill,minmax(260px,1fr));gap:.7rem;margin-top:.5rem}
.tc{background:var(--surface);border:1px solid var(--bdim);border-radius:var(--rl);padding:.95rem 1rem .95rem 1.1rem;border-left:3px solid var(--ac);transition:border-color .15s}
.tc.rel{border-left-color:var(--high)}.tc.prf{border-left-color:var(--med)}.tc.fea{border-left-color:var(--low)}
.tc-tag{font-family:var(--mono);font-size:.58rem;letter-spacing:.12em;text-transform:uppercase;color:var(--tx3);display:block;margin-bottom:.2rem}
.tc-name{font-size:.9rem;font-weight:700;color:var(--tx);line-height:1.2;margin-bottom:.2rem}
.tc-pkg{font-family:var(--mono);font-size:.66rem;color:var(--ac);display:block;margin-bottom:.4rem}
.tc.rel .tc-pkg{color:var(--high)}.tc.prf .tc-pkg{color:var(--med)}.tc.fea .tc-pkg{color:var(--low)}
.tc-desc{font-size:.78rem;color:var(--tx2);line-height:1.5}
.prio{display:inline-flex;align-items:center;gap:.3rem;margin-top:.55rem;font-family:var(--mono);font-size:.6rem;color:var(--tx3);text-transform:uppercase;letter-spacing:.08em}
.pd{width:6px;height:6px;border-radius:50%}
.pd.now{background:var(--crit)}.pd.soon{background:var(--high)}.pd.later{background:var(--low)}
.tbl-wrap{overflow-x:auto;margin-top:.75rem}
table{width:100%;border-collapse:collapse;font-size:.78rem;min-width:580px}
th{text-align:left;padding:.45rem .75rem;font-family:var(--mono);font-size:.6rem;letter-spacing:.1em;text-transform:uppercase;color:var(--tx3);border-bottom:1px solid var(--bdim);font-weight:600;white-space:nowrap}
td{padding:.6rem .75rem;border-bottom:1px solid var(--bdim);color:var(--tx2);vertical-align:top}
tr:last-child td{border-bottom:none}
tr:hover td{background:var(--s2)}
td:first-child{color:var(--tx);font-weight:500}
.wk{font-family:var(--mono);font-size:.68rem;color:var(--tx3);white-space:nowrap}
.effort{font-family:var(--mono);font-size:.68rem;white-space:nowrap}
.callout{background:var(--acd);border:1px solid var(--border);border-radius:var(--rl);padding:.8rem 1rem;margin-bottom:1.25rem;font-size:.8rem;color:var(--tx2);line-height:1.55}
.callout strong{color:var(--ac)}
.health{background:var(--surface);border:1px solid var(--bdim);border-radius:var(--rl);padding:1.1rem 1.25rem;margin-bottom:1.5rem;display:flex;gap:2rem;align-items:center;flex-wrap:wrap}
.h-score{text-align:center;flex-shrink:0}
.h-num{font-family:var(--mono);font-size:2.8rem;font-weight:800;color:var(--high);line-height:1;letter-spacing:-.04em}
.h-lbl{font-family:var(--mono);font-size:.58rem;text-transform:uppercase;letter-spacing:.12em;color:var(--tx3);margin-top:.2rem}
.h-bars{flex:1;min-width:220px;display:flex;flex-direction:column;gap:.5rem}
.hbr{display:flex;align-items:center;gap:.75rem}
.hbr-lbl{font-size:.75rem;color:var(--tx2);width:115px;flex-shrink:0}
.hbr-track{flex:1;height:5px;background:var(--s2);border-radius:3px;overflow:hidden}
.hbr-fill{height:100%;border-radius:3px;transition:width .8s cubic-bezier(.4,0,.2,1)}
.hbr-fill.cr{background:var(--crit)}.hbr-fill.hi{background:var(--high)}.hbr-fill.md{background:var(--med)}.hbr-fill.gd{background:var(--low)}
.hbr-val{font-family:var(--mono);font-size:.68rem;color:var(--tx3);width:30px;text-align:right;flex-shrink:0}
@media(max-width:760px){
  .grid{grid-template-columns:1fr}
  .sidebar{display:none}
  .main{grid-column:1}
  .site-hd{flex-direction:column}
  .chips{padding-top:0}
}
</style>

<div class="wrap">
<div class="grid">

<header class="site-hd">
  <div>
    <div class="hd-eye">Historical Full-Stack Audit · July 2026</div>
    <h1 class="hd-title">Care Bangla — Project Audit<br>&amp; Improvement Roadmap</h1>
    <p class="hd-sub">Historical baseline plus an August 2026 feature-delivery addendum · re-verify every open finding against current code</p>
  </div>
  <div class="chips">
    <span class="chip cr"><span class="chip-dot"></span>4 Critical</span>
    <span class="chip hi"><span class="chip-dot"></span>4 High</span>
    <span class="chip md"><span class="chip-dot"></span>10 Medium</span>
    <span class="chip lo"><span class="chip-dot"></span>10 Architecture</span>
  </div>
</header>

<nav class="sidebar">
  <div class="nav-grp">
    <span class="nav-grp-lbl">Security</span>
    <a class="nav-a on" href="#critical"><span class="ndot cr"></span>Critical (4)</a>
    <a class="nav-a" href="#high"><span class="ndot hi"></span>High (4)</a>
    <a class="nav-a" href="#medium"><span class="ndot md"></span>Medium (6)</a>
    <a class="nav-a" href="#low"><span class="ndot lo"></span>Low / Quick (4)</a>
  </div>
  <div class="nav-grp">
    <span class="nav-grp-lbl">Improvement</span>
    <a class="nav-a" href="#reliability"><span class="ndot hi"></span>Reliability</a>
    <a class="nav-a" href="#performance"><span class="ndot md"></span>Performance</a>
    <a class="nav-a" href="#architecture"><span class="ndot ac"></span>Architecture</a>
    <a class="nav-a" href="#features"><span class="ndot fe"></span>Features to Add</a>
  </div>
  <div class="nav-grp">
    <span class="nav-grp-lbl">Summary</span>
    <a class="nav-a" href="#august-update"><span class="ndot fe"></span>August 2026 Update</a>
    <a class="nav-a" href="#roadmap"><span class="ndot in"></span>Priority Roadmap</a>
  </div>
</nav>

<main class="main">

<section class="sec" id="august-update">
  <div class="sec-hd">
    <span class="sec-title">August 2026 Feature Delivery Update</span>
    <span class="sec-tag fea">Documentation Refresh</span>
  </div>
  <div class="callout">
    <strong>Important:</strong> the readiness score, issue counts, findings, and roadmap below are the July 2026 audit baseline, not a new security or performance audit. Several files and features have changed since it was written. Validate a finding against the current branch before acting on it. The current cross-cutting implementation reference is <a href="FEATURES_AND_CONTENT_ARCHITECTURE.md"><code>FEATURES_AND_CONTENT_ARCHITECTURE.md</code></a>.
  </div>
  <div class="tgrid">
    <div class="tc fea"><span class="tc-tag">URL resilience</span><div class="tc-name">Products &amp; Blogs</div><div class="tc-desc">Category-aware product canonicals, former-slug 308 redirects, ranked replacement targets, preserved delete rules, and explanatory missing-content notices.</div></div>
    <div class="tc fea"><span class="tc-tag">Communication</span><div class="tc-name">Internal Message</div><div class="tc-desc">User/admin conversations, rich safe formatting, custom topics, status/priority/unread state, and authorized GridFS image/document attachments.</div></div>
    <div class="tc fea"><span class="tc-tag">Content platform</span><div class="tc-name">Structured Images &amp; Inline Links</div><div class="tc-desc">Backward-compatible image metadata and selection-based narrative hyperlinks now span the admin CMS and public render paths.</div></div>
    <div class="tc fea"><span class="tc-tag">Public content</span><div class="tc-name">About, Service, Blog &amp; FAQ</div><div class="tc-desc">Mission/Vision, CEO &amp; Founder, departments, richer blog listing/articles, FAQ management, shared service previews, and booking-tier-first service pages.</div></div>
    <div class="tc fea"><span class="tc-tag">Portal</span><div class="tc-name">Logged-in User Shell</div><div class="tc-desc">Admin-style responsive layout with collapsible navigation, secondary header, history surfaces, and Message integration while retaining the public header.</div></div>
    <div class="tc fea"><span class="tc-tag">Localization</span><div class="tc-name">Independent Language Scopes</div><div class="tc-desc">Public/user language persists under <code>cb_lang</code>; admin language persists independently under <code>cb_admin_lang</code>.</div></div>
  </div>
</section>

<div class="health">
  <div class="h-score">
    <div class="h-num">38</div>
    <div class="h-lbl">Production<br>Readiness</div>
  </div>
  <div class="h-bars">
    <div class="hbr"><span class="hbr-lbl">Security</span><div class="hbr-track"><div class="hbr-fill cr" style="width:22%"></div></div><span class="hbr-val">22</span></div>
    <div class="hbr"><span class="hbr-lbl">Reliability</span><div class="hbr-track"><div class="hbr-fill hi" style="width:48%"></div></div><span class="hbr-val">48</span></div>
    <div class="hbr"><span class="hbr-lbl">Performance</span><div class="hbr-track"><div class="hbr-fill md" style="width:52%"></div></div><span class="hbr-val">52</span></div>
    <div class="hbr"><span class="hbr-lbl">Architecture</span><div class="hbr-track"><div class="hbr-fill gd" style="width:65%"></div></div><span class="hbr-val">65</span></div>
    <div class="hbr"><span class="hbr-lbl">Feature Depth</span><div class="hbr-track"><div class="hbr-fill gd" style="width:55%"></div></div><span class="hbr-val">55</span></div>
  </div>
</div>

<!-- ═══════════════════════════════ CRITICAL ═══════════════════════════════ -->
<section class="sec" id="critical">
  <div class="sec-hd">
    <span class="sec-title">Security</span>
    <span class="sec-tag sec">Critical &amp; High</span>
  </div>

  <div class="grp-lbl cr">Critical — fix before any production deployment</div>

  <div class="fc">
    <span class="stripe cr"></span>
    <div class="fb">
      <div class="fm"><span class="fid">C-1</span><span class="sbadge cr">Critical</span></div>
      <div class="ft">Security middleware is dead code — no headers or guards are active</div>
      <div class="fd">All CSP, HSTS, X-Frame-Options, and admin JWT gate logic lives in <code>src/proxy.js</code> with a named export <code>export async function proxy</code>. Next.js only loads <code>src/middleware.js</code> with <code>export default</code>. The file is never imported anywhere. Every security header and server-side admin redirect is silently a no-op.</div>
      <div class="fp">src/proxy.js → src/middleware.js</div>
      <div class="fix">
        <span class="fix-lbl">Fix</span>
        <p>Rename <code>proxy.js</code> → <code>middleware.js</code> and change the export to <code>export default async function middleware(request)</code>. Add <code>export const config = { matcher: ['/((?!_next/static|_next/image|favicon.ico).*)'] }</code>. This single rename activates all existing header + redirect logic instantly.</p>
      </div>
    </div>
  </div>

  <div class="fc">
    <span class="stripe cr"></span>
    <div class="fb">
      <div class="fm"><span class="fid">C-2</span><span class="sbadge cr">Critical</span></div>
      <div class="ft">Admin login has zero brute-force protection</div>
      <div class="fd">The <code>POST /api/admin/auth/login</code> handler has no rate limiting, IP lockout, or CAPTCHA. An attacker can make unlimited guesses per second. Admin passwords are plain strings in env vars (not bcrypt-hashed), so a leaked password is immediately usable.</div>
      <div class="fp">src/app/api/admin/auth/login/route.js</div>
      <div class="fix">
        <span class="fix-lbl">Fix</span>
        <p>Add <code>@upstash/ratelimit</code> + <code>@upstash/redis</code>. Limit to 10 attempts per IP per 15 minutes; return 429 with a <code>Retry-After</code> header on breach. Also consider bcrypt-hashing admin passwords at startup so plaintext never sits in runtime process memory.</p>
      </div>
    </div>
  </div>

  <div class="fc">
    <span class="stripe cr"></span>
    <div class="fb">
      <div class="fm"><span class="fid">C-3</span><span class="sbadge cr">Critical</span></div>
      <div class="ft">Admin shell renders fully before any auth check runs</div>
      <div class="fd"><code>src/app/admin/layout.js</code> is a Server Component that renders <code>AdminClientLayout</code> with no server-side session verification. The auth check lives inside a <code>useEffect</code> — meaning the full Ant Design sidebar and page skeleton are sent to the browser before JavaScript redirects unauthenticated users. Since C-1 means the middleware gate also never fires, the admin UI has zero server-side protection.</div>
      <div class="fp">src/app/admin/layout.js · src/Components/Admin/AdminClientLayout.jsx:116</div>
      <div class="fix">
        <span class="fix-lbl">Fix</span>
        <p>In <code>src/app/admin/layout.js</code>, call <code>verifyAdminSession()</code> (server-side, using <code>cookies()</code> from <code>next/headers</code>) at the top of the layout function. On failure, call <code>redirect('/admin/login')</code>. This stops the page from rendering — not just from being interacted with.</p>
      </div>
    </div>
  </div>

  <div class="fc">
    <span class="stripe cr"></span>
    <div class="fb">
      <div class="fm"><span class="fid">C-4</span><span class="sbadge cr">Critical</span></div>
      <div class="ft">Order prices are entirely client-controlled — no server-side validation</div>
      <div class="fd"><code>POST /api/orders</code> accepts <code>items[].price</code> and <code>subtotal</code> directly from the request body and writes them to MongoDB without cross-checking the Product collection. A user can submit <code>{"price": 1}</code> for any item. No authentication requirement, no rate limiting on order creation.</div>
      <div class="fp">src/app/api/orders/route.js:15–27</div>
      <div class="fix">
        <span class="fix-lbl">Fix</span>
        <p>After parsing the body, fetch each <code>productId</code> from the Product collection with <code>Product.find({ _id: { $in: ids } })</code> and replace client-supplied prices with DB prices before inserting the order. Compute <code>subtotal</code> server-side. Return 400 if any ID is missing from the DB.</p>
      </div>
    </div>
  </div>
</section>

<!-- ═══════════════════════════════ HIGH ═══════════════════════════════ -->
<section class="sec" id="high">
  <div class="grp-lbl hi">High — address within first sprint</div>

  <div class="fc">
    <span class="stripe hi"></span>
    <div class="fb">
      <div class="fm"><span class="fid">H-1</span><span class="sbadge hi">High</span></div>
      <div class="ft">Zero security headers on any HTTP response</div>
      <div class="fd">Because <code>proxy.js</code> is never loaded, every page and API response is served with no <code>Content-Security-Policy</code>, no <code>Strict-Transport-Security</code>, no <code>X-Frame-Options</code>, and no <code>X-Content-Type-Options</code>. The admin panel can be clickjacked. Fixing C-1 resolves this simultaneously.</div>
      <div class="fp">next.config.mjs (no headers() function)</div>
    </div>
  </div>

  <div class="fc">
    <span class="stripe hi"></span>
    <div class="fb">
      <div class="fm"><span class="fid">H-2</span><span class="sbadge hi">High</span></div>
      <div class="ft">Any uploaded GridFS file is permanently publicly accessible</div>
      <div class="fd"><code>GET /api/media/[id]</code> has no authentication check — any 24-character hex ObjectId streams the corresponding GridFS file with <code>Cache-Control: public, immutable</code>. If an admin ever uploads a sensitive document (PDF, credentials file, internal report) it becomes a permanent public URL.</div>
      <div class="fp">src/app/api/media/[id]/route.js</div>
      <div class="fix">
        <span class="fix-lbl">Fix</span>
        <p>For sensitive uploads, verify a session cookie before streaming. For public assets the current behavior is acceptable — but add a file-type allowlist so non-image uploads are rejected at upload time. Consider Cloudflare R2 or AWS S3 with signed URLs for access-controlled files.</p>
      </div>
    </div>
  </div>

  <div class="fc">
    <span class="stripe hi"></span>
    <div class="fb">
      <div class="fm"><span class="fid">H-3</span><span class="sbadge hi">High</span></div>
      <div class="ft">No rate limiting on any endpoint — full DoS surface</div>
      <div class="fd">None of these endpoints have any rate limit: admin login, user registration, user change-password, order creation, chat session/message creation, and <code>/api/media/[id]</code> bandwidth streaming. A bot can flood MongoDB with chat sessions, exhaust the PSI API quota via the SEO vitals endpoint, or fill disk via repeated large file uploads.</div>
      <div class="fix">
        <span class="fix-lbl">Fix</span>
        <p>Add <code>@upstash/ratelimit</code> with a sliding window to the middleware layer — different limits per route group: tight for auth (10/15min/IP), moderate for mutations (60/min/IP), loose for reads. A single middleware rule covers all routes without touching individual route handlers.</p>
      </div>
    </div>
  </div>

  <div class="fc">
    <span class="stripe hi"></span>
    <div class="fb">
      <div class="fm"><span class="fid">H-4</span><span class="sbadge hi">High</span></div>
      <div class="ft">NoSQL injection via <code>sessionKey</code> in public chat routes</div>
      <div class="fd">Both <code>/api/chat/message</code> and <code>/api/chat/terminate</code> pass <code>sessionKey</code> from the request body directly into <code>ChatSession.findOne({ sessionKey })</code>. Sending <code>{"sessionKey": {"$gt": ""}}</code> matches the first session in the collection. An unauthenticated attacker can post messages into any user's chat session or terminate any open session.</div>
      <div class="fp">src/app/api/chat/message/route.js:51 · chat/terminate/route.js:15</div>
      <div class="fix">
        <span class="fix-lbl">Fix</span>
        <p>Add: <code>if (typeof sessionKey !== 'string' || sessionKey.length > 128) return err('Invalid session', 400);</code> before any query. This typeof check closes the injection definitively.</p>
      </div>
    </div>
  </div>
</section>

<!-- ═══════════════════════════════ MEDIUM ═══════════════════════════════ -->
<section class="sec" id="medium">
  <div class="grp-lbl md">Medium — address within first month</div>

  <div class="fc">
    <span class="stripe md"></span>
    <div class="fb">
      <div class="fm"><span class="fid">M-1</span><span class="sbadge md">Medium</span></div>
      <div class="ft">Admin content GET endpoint is publicly accessible</div>
      <div class="fd">The <code>GET</code> handler in <code>/api/admin/content/[page]</code> has no <code>withAdmin()</code> wrapper. Any unauthenticated user can fetch any CMS content document including the <code>settings</code> page, which may contain internal configuration.</div>
      <div class="fp">src/app/api/admin/content/[page]/route.js:25</div>
      <div class="fix"><span class="fix-lbl">Fix</span><p>Wrap the GET handler with <code>withAdmin()</code> the same way the POST handler is. One line of change.</p></div>
    </div>
  </div>

  <div class="fc">
    <span class="stripe md"></span>
    <div class="fb">
      <div class="fm"><span class="fid">M-2</span><span class="sbadge md">Medium</span></div>
      <div class="ft">Public <code>/api/content/[page]</code> has no page allowlist</div>
      <div class="fd">The route queries <code>PageContent.findOne({ page })</code> for any value from the URL. An attacker can enumerate all page keys in the database. The admin version correctly restricts to 6 known keys; the public route should too.</div>
      <div class="fp">src/app/api/content/[page]/route.js:7</div>
      <div class="fix"><span class="fix-lbl">Fix</span><p>Add <code>const ALLOWED = ['home','about','contact']; if (!ALLOWED.includes(params.page)) return err('Not found', 404);</code> at the top of the handler.</p></div>
    </div>
  </div>

  <div class="fc">
    <span class="stripe md"></span>
    <div class="fb">
      <div class="fm"><span class="fid">M-3</span><span class="sbadge md">Medium</span></div>
      <div class="ft">ReDoS via unescaped regex in public product search</div>
      <div class="fd">The <code>search</code> query param is placed directly into MongoDB <code>$regex</code>: <code>{ name: { $regex: search } }</code>. A pathological pattern like <code>(a+)+$</code> causes exponential backtracking in MongoDB's PCRE engine, potentially hanging the query thread. This endpoint is publicly accessible.</div>
      <div class="fp">src/app/api/shop/products/route.js:21–24</div>
      <div class="fix"><span class="fix-lbl">Fix</span><p>Escape the search string: <code>const safe = search.replace(/[.*+?^${}()|[\]\\]/g, '\\$&');</code>. Or use MongoDB Atlas Search (text index) which is both safer and faster for user-facing search.</p></div>
    </div>
  </div>

  <div class="fc">
    <span class="stripe md"></span>
    <div class="fb">
      <div class="fm"><span class="fid">M-4</span><span class="sbadge md">Medium</span></div>
      <div class="ft">Admin passwords stored as plaintext environment variables</div>
      <div class="fd">Login compares <code>password === process.env.ADMIN_1_PASSWORD</code>. If env vars leak through debug endpoints, server logs, a CI/CD dump, or an error page, admin credentials are immediately usable. Regular user passwords are correctly bcrypt-hashed (cost 12) — admins should be too.</div>
      <div class="fp">src/app/api/admin/auth/login/route.js:10–25</div>
      <div class="fix"><span class="fix-lbl">Fix</span><p>Store <code>ADMIN_1_PASSWORD_HASH</code> in env (generated once with <code>bcrypt.hash()</code>) and use <code>bcrypt.compare(inputPassword, hash)</code> on login.</p></div>
    </div>
  </div>

  <div class="fc">
    <span class="stripe md"></span>
    <div class="fb">
      <div class="fm"><span class="fid">M-5</span><span class="sbadge md">Medium</span></div>
      <div class="ft">MongoDB connection has no timeout configuration</div>
      <div class="fd">The Mongoose connection only sets <code>maxPoolSize: 10</code>. No <code>serverSelectionTimeoutMS</code>, <code>socketTimeoutMS</code>, or <code>connectTimeoutMS</code>. When MongoDB Atlas is unreachable, <code>connectDB()</code> awaits indefinitely — every concurrent API request hangs until Next.js's own timeout fires, amplifying a DB outage into a full application DoS.</div>
      <div class="fp">src/lib/mongodb.js:12–17</div>
      <div class="fix"><span class="fix-lbl">Fix</span><p>Add: <code>serverSelectionTimeoutMS: 5000, socketTimeoutMS: 30000, connectTimeoutMS: 10000</code> to the Mongoose connect options. Outages return 503 quickly instead of indefinitely hanging.</p></div>
    </div>
  </div>

  <div class="fc">
    <span class="stripe md"></span>
    <div class="fb">
      <div class="fm"><span class="fid">M-6</span><span class="sbadge md">Medium</span></div>
      <div class="ft"><code>next-pwa</code> 5.6.0 is abandoned and incompatible with App Router</div>
      <div class="fd">The <code>next-pwa</code> package hasn't been maintained since 2022 and officially doesn't support Next.js App Router. Running it on Next.js 16 is unsupported. It intercepts the Turbopack config even in dev, slowing cold starts, and its generated service worker may behave unexpectedly with App Router streaming.</div>
      <div class="fp">package.json · next.config.mjs (withPWA wrapper)</div>
      <div class="fix"><span class="fix-lbl">Fix</span><p>Replace with <code>@serwist/next</code> — the maintained fork of <code>next-pwa</code> built specifically for Next.js App Router. Drop-in config replacement, full Workbox 7 support, TypeScript-first.</p></div>
    </div>
  </div>
</section>

<!-- ═══════════════════════════════ LOW ═══════════════════════════════ -->
<section class="sec" id="low">
  <div class="grp-lbl lo">Low / Quick — small effort, meaningful tightening</div>

  <div class="fc">
    <span class="stripe lo"></span>
    <div class="fb">
      <div class="fm"><span class="fid">L-1</span><span class="sbadge lo">Low</span></div>
      <div class="ft">Admin cookie should use <code>SameSite=Strict</code> and the <code>__Host-</code> prefix</div>
      <div class="fd">Current cookie: <code>cb_admin_tok</code>, <code>SameSite: lax</code>. Changing to <code>SameSite: strict</code> prevents the cookie from being sent on any cross-origin navigation. The <code>__Host-</code> prefix enforces Secure + no Domain + Path=/ at the browser level, making subdomain cookie shadowing impossible.</div>
      <div class="fp">src/lib/auth.js:5, 37</div>
    </div>
  </div>

  <div class="fc">
    <span class="stripe lo"></span>
    <div class="fb">
      <div class="fm"><span class="fid">L-2</span><span class="sbadge lo">Low</span></div>
      <div class="ft">JWT secret minimum length is only 16 characters</div>
      <div class="fd"><code>env.js</code> enforces <code>z.string().min(16)</code> for <code>JWT_SECRET</code>. HMAC-SHA256 needs ≥32 bytes of entropy for full security. A 16-character ASCII string is below the 128-bit floor for long-lived admin tokens.</div>
      <div class="fp">src/lib/env.js:4</div>
      <div class="fix"><span class="fix-lbl">Fix</span><p>Change to <code>.min(32, 'JWT_SECRET must be ≥32 characters')</code> and regenerate any existing secret that's shorter.</p></div>
    </div>
  </div>

  <div class="fc">
    <span class="stripe lo"></span>
    <div class="fb">
      <div class="fm"><span class="fid">L-3</span><span class="sbadge lo">Low</span></div>
      <div class="ft">File upload validation uses client-supplied MIME type only</div>
      <div class="fd">Upload validation checks <code>file.type</code> (from the multipart <code>Content-Type</code>) which is entirely client-controlled. An admin could upload an HTML or JavaScript file with <code>Content-Type: image/jpeg</code>. The downstream <code>/api/media/[id]</code> route echoes the stored content type back, potentially causing the browser to execute served content.</div>
      <div class="fp">src/app/api/admin/media/route.js:38–40</div>
      <div class="fix"><span class="fix-lbl">Fix</span><p>Use the <code>file-type</code> npm package to read the first 4 KB of the buffer and detect actual magic bytes. Reject anything whose detected type doesn't match the allowed set, regardless of the claimed MIME type.</p></div>
    </div>
  </div>

  <div class="fc">
    <span class="stripe lo"></span>
    <div class="fb">
      <div class="fm"><span class="fid">L-4</span><span class="sbadge lo">Low</span></div>
      <div class="ft">Admin JWT has 7-day expiry with no revocation mechanism</div>
      <div class="fd">JWTs are stateless — a stolen <code>cb_admin_tok</code> remains valid for 7 days even if the admin changes their password. There is no token blocklist, no session store, and no forced-logout capability.</div>
      <div class="fp">src/lib/auth.js:8, 34</div>
      <div class="fix"><span class="fix-lbl">Fix</span><p>Reduce expiry to 2 hours and implement a refresh flow, or store a <code>sessionId</code> in Redis/MongoDB and validate it inside <code>withAdmin()</code>. This allows immediate revocation by deleting the session record.</p></div>
    </div>
  </div>
</section>

<!-- ═══════════════════════════════ RELIABILITY ═══════════════════════════════ -->
<section class="sec" id="reliability">
  <div class="sec-hd">
    <span class="sec-title">Reliability &amp; Resilience</span>
    <span class="sec-tag rel">Reliability</span>
  </div>

  <div class="callout">The project has good DB-first/static-fallback discipline on the public site. These gaps are about what happens when things <strong>do</strong> go wrong — crash recovery, observability, and graceful degradation.</div>

  <div class="tgrid">
    <div class="tc rel">
      <span class="tc-tag">Missing</span>
      <div class="tc-name">React Error Boundaries</div>
      <span class="tc-pkg">error.js (App Router convention)</span>
      <p class="tc-desc">There are zero <code>error.js</code> files anywhere in <code>src/app/</code>. A runtime crash in any component — a third-party library throwing, a null dereference — kills the entire page with no recovery. App Router's <code>error.js</code> provides automatic per-route error boundaries that show a recovery UI instead of a blank screen.</p>
      <span class="prio"><span class="pd soon"></span>Add to 5 key routes: blog/, service/, doctors/, medical-shop/, admin/</span>
    </div>
    <div class="tc rel">
      <span class="tc-tag">Missing</span>
      <div class="tc-name">Loading Skeletons</div>
      <span class="tc-pkg">loading.js (App Router convention)</span>
      <p class="tc-desc">No <code>loading.js</code> files exist in the app directory. On slow connections, data-dependent pages show nothing until the full server component resolves. React Suspense boundaries (via <code>loading.js</code>) show instant skeletons while the page streams in. Without them, navigations feel frozen.</p>
      <span class="prio"><span class="pd soon"></span>Add to blog/, service/, doctors/, medical-shop/</span>
    </div>
    <div class="tc rel">
      <span class="tc-tag">Replace</span>
      <div class="tc-name">Error Monitoring</div>
      <span class="tc-pkg">@sentry/nextjs</span>
      <p class="tc-desc">Currently no application error tracking. When something breaks in production, you find out from users. Sentry's Next.js SDK captures server-side exceptions, client-side React crashes, API route errors, and Core Web Vitals regressions — with full stack traces, user context, and source maps.</p>
      <span class="prio"><span class="pd now"></span>High value, ~30min setup</span>
    </div>
    <div class="tc rel">
      <span class="tc-tag">Add</span>
      <div class="tc-name">Structured Logging</div>
      <span class="tc-pkg">pino + pino-pretty</span>
      <p class="tc-desc">API routes currently use <code>console.error</code> or no logging at all. Pino is the fastest Node.js logger — JSON-structured by default, compatible with Vercel Log Drains, CloudWatch, and Datadog. Add request IDs so you can trace a client error back to the exact server-side chain that produced it.</p>
      <span class="prio"><span class="pd soon"></span>Replace console.* calls progressively</span>
    </div>
    <div class="tc rel">
      <span class="tc-tag">Add</span>
      <div class="tc-name">DB Health Check Endpoint</div>
      <span class="tc-pkg">/api/health (custom route)</span>
      <p class="tc-desc">Add a <code>GET /api/health</code> route that pings MongoDB and returns <code>{"status":"ok","db":"connected"}</code> or a 503. Vercel, uptime monitors (BetterStack, UptimeRobot), and load balancers can poll this to detect outages before users do.</p>
      <span class="prio"><span class="pd soon"></span>~15 minutes to implement</span>
    </div>
    <div class="tc rel">
      <span class="tc-tag">Add</span>
      <div class="tc-name">Redis Caching Layer</div>
      <span class="tc-pkg">@upstash/redis · ioredis</span>
      <p class="tc-desc">High-read, low-mutation data (published services, team members, categories) hits MongoDB on every ISR miss and every <code>force-dynamic</code> route. A Redis cache with a 5-minute TTL serves these in ~1ms instead of ~50ms, and shields the DB during traffic spikes. Upstash Redis is serverless-native with a generous free tier.</p>
      <span class="prio"><span class="pd later"></span>Week 3–4</span>
    </div>
  </div>
</section>

<!-- ═══════════════════════════════ PERFORMANCE ═══════════════════════════════ -->
<section class="sec" id="performance">
  <div class="sec-hd">
    <span class="sec-title">Performance</span>
    <span class="sec-tag prf">Performance</span>
  </div>

  <div class="tgrid">
    <div class="tc prf">
      <span class="tc-tag">~94 instances</span>
      <div class="tc-name">Migrate raw &lt;img&gt; to next/image</div>
      <span class="tc-pkg">next/image (already in project)</span>
      <p class="tc-desc">About 94 raw <code>&lt;img&gt;</code> tags remain across Components and Pages. Each one sends the full original file size to every device, offers no responsive <code>srcset</code>, and causes layout shift without explicit dimensions. The hero banner alone is likely 3–4× larger than necessary on mobile. Product cards already use <code>next/image</code> — extend the pattern to hero sections, team photos, blog thumbnails, and service icons.</p>
      <span class="prio"><span class="pd soon"></span>Biggest LCP/CWV win in the project</span>
    </div>
    <div class="tc prf">
      <span class="tc-tag">Font Loading</span>
      <div class="tc-name">Migrate Poppins &amp; Rubik to next/font</div>
      <span class="tc-pkg">next/font/google (already used for Hind Siliguri)</span>
      <p class="tc-desc">Poppins and Rubik are served as raw <code>.ttf</code> files from <code>/assets/fonts/</code>. TTF has no compression — WOFF2 equivalents are 30–40% smaller. More importantly, <code>next/font</code> auto-injects <code>&lt;link rel="preload"&gt;</code>, eliminates FOIT/FOUT, and self-hosts the font so there are no Google Fonts DNS lookups. Hind Siliguri already uses <code>next/font</code> — bring the other two in line.</p>
      <span class="prio"><span class="pd soon"></span>~2hrs, measurable FCP improvement</span>
    </div>
    <div class="tc prf">
      <span class="tc-tag">Media Pipeline</span>
      <div class="tc-name">CDN / Edge Cache for GridFS Images</div>
      <span class="tc-pkg">Cloudflare R2 · Vercel Blob · AWS S3</span>
      <p class="tc-desc"><code>/api/media/[id]</code> is <code>force-dynamic</code> — every image request runs through the full Next.js handler → MongoDB GridFS stream chain. On a page with 10+ images, that's 10 round-trips to Atlas. Migrating to an object store (Cloudflare R2: free for 10GB egress) serves images from a CDN edge node ~200ms faster globally.</p>
      <span class="prio"><span class="pd later"></span>Week 4–6</span>
    </div>
    <div class="tc prf">
      <span class="tc-tag">Admin Build Speed</span>
      <div class="tc-name">Isolate Ant Design to Admin Routes Only</div>
      <span class="tc-pkg">Route groups · transpilePackages scoping</span>
      <p class="tc-desc"><code>transpilePackages: ['antd', '@ant-design/icons', ...]</code> applies globally — the entire Ant Design + icon ecosystem is transformed on every route bundle, including public pages that import zero Ant Design code. Moving admin into a <code>(admin)</code> route group allows conditional imports. <code>@ant-design/icons</code> alone initialises ~800 icon modules in dev.</p>
      <span class="prio"><span class="pd later"></span>Dev experience improvement</span>
    </div>
  </div>
</section>

<!-- ═══════════════════════════════ ARCHITECTURE ═══════════════════════════════ -->
<section class="sec" id="architecture">
  <div class="sec-hd">
    <span class="sec-title">Architecture &amp; Future-Proofing</span>
    <span class="sec-tag arc">Architecture</span>
  </div>

  <div class="tgrid">
    <div class="tc">
      <span class="tc-tag">Migration Path</span>
      <div class="tc-name">TypeScript</div>
      <span class="tc-pkg">tsconfig.json · @types/node · @types/react</span>
      <p class="tc-desc">The project is 100% JavaScript. TypeScript would have caught the <code>sessionKey</code> NoSQL injection (H-4), the order price issue (C-4), and many silent <code>undefined</code> prop bugs without running the app. Next.js supports mixed <code>.js</code>/<code>.ts</code> — start with <code>lib/</code> and <code>models/</code> (Mongoose 9 ships full TS types), then spread outward. <code>strict: true</code> for new files; <code>allowJs: true</code> for existing ones.</p>
      <span class="prio"><span class="pd later"></span>Start incrementally — lib/ first</span>
    </div>
    <div class="tc">
      <span class="tc-tag">Testing</span>
      <div class="tc-name">Automated Test Suite</div>
      <span class="tc-pkg">Vitest · @testing-library/react · Playwright</span>
      <p class="tc-desc">There are currently zero automated tests. For this project: Vitest for unit tests on <code>lib/auth.js</code>, <code>lib/validation.js</code>, and order price logic; React Testing Library for critical flows (cart add, language toggle); Playwright for E2E (admin login → create product → public site shows it). Prioritise security-critical paths first.</p>
      <span class="prio"><span class="pd later"></span>Start with 3 critical unit tests</span>
    </div>
    <div class="tc">
      <span class="tc-tag">CI/CD</span>
      <div class="tc-name">GitHub Actions Pipeline</div>
      <span class="tc-pkg">github/actions · Lighthouse CI</span>
      <p class="tc-desc">No CI pipeline exists. A basic pipeline runs: lint → type-check → unit tests → Lighthouse CI on preview URL. Lighthouse CI can enforce a minimum SEO score (≥90) and LCP threshold (&lt;2.5s) — making SEO a hard gate rather than a periodic audit. The <code>.lighthouserc.js</code> file already exists per the SEO roadmap.</p>
      <span class="prio"><span class="pd soon"></span>High leverage once tests exist</span>
    </div>
    <div class="tc">
      <span class="tc-tag">API Design</span>
      <div class="tc-name">API Versioning</div>
      <span class="tc-pkg">/api/v1/* route prefix</span>
      <p class="tc-desc">The public API has no versioning. Once external consumers exist (a mobile app, a partner integration), breaking changes to <code>/api/services</code> become deployment-blocking events. Moving to <code>/api/v1/</code> now costs one route rename while the app is still small. A middleware rewrite keeps existing client-side calls working without changes.</p>
      <span class="prio"><span class="pd later"></span>Before any mobile app work</span>
    </div>
    <div class="tc">
      <span class="tc-tag">Admin UX</span>
      <div class="tc-name">Admin Audit Log</div>
      <span class="tc-pkg">Custom AuditLog Mongoose model</span>
      <p class="tc-desc">There is no record of who changed what in the admin panel. In a healthcare platform, this is both a trust and a compliance issue. A lightweight audit log model (<code>{ action, entity, entityId, adminEmail, before, after, createdAt }</code>) written inside the shared <code>withAdmin()</code> hook means every mutation is logged automatically without touching individual route handlers.</p>
      <span class="prio"><span class="pd later"></span>Compliance value, low complexity</span>
    </div>
    <div class="tc">
      <span class="tc-tag">Consistency</span>
      <div class="tc-name">API Response Envelope</div>
      <span class="tc-pkg">src/lib/apiHelpers.js (extend existing)</span>
      <p class="tc-desc">Public routes return raw arrays; admin routes return <code>{ success, data, error }</code>; some routes return mixed shapes. A consistent envelope — <code>{ ok: true, data: [...] }</code> — makes client-side error handling uniform, enables API gateways to parse responses reliably, and allows RTK Query <code>transformResponse</code> to normalize data in one place instead of per-component.</p>
      <span class="prio"><span class="pd later"></span>Migrate public routes progressively</span>
    </div>
  </div>
</section>

<!-- ═══════════════════════════════ FEATURES ═══════════════════════════════ -->
<section class="sec" id="features">
  <div class="sec-hd">
    <span class="sec-title">Essential Features to Add</span>
    <span class="sec-tag fea">Product</span>
  </div>

  <div class="tgrid">
    <div class="tc fea">
      <span class="tc-tag">E-commerce</span>
      <div class="tc-name">Payment Gateway</div>
      <span class="tc-pkg">SSLCommerz · ShurjoPay · bKash API</span>
      <p class="tc-desc">The shop has a full cart and checkout UI but no actual payment processing. For Bangladesh, SSLCommerz is the most widely-used gateway (supports bKash, Nagad, Rocket, Visa, Mastercard). ShurjoPay is a modern REST-first alternative. Integrate at the checkout step: call the gateway API server-side to protect API keys, redirect to the payment page, handle the callback webhook to mark orders as paid.</p>
      <span class="prio"><span class="pd now"></span>Blocking for any real revenue</span>
    </div>
    <div class="tc fea">
      <span class="tc-tag">Notifications</span>
      <div class="tc-name">Email Transactional Notifications</div>
      <span class="tc-pkg">Resend · @react-email/components</span>
      <p class="tc-desc">Order confirmations, appointment reminders, and booking receipts are not sent. Resend is the modern transactional email API with a generous free tier (3,000 emails/month) and a React Email SDK for type-safe, styled templates. Trigger from the relevant API routes using a background job pattern so the API response doesn't wait for email delivery.</p>
      <span class="prio"><span class="pd now"></span>Core trust-building feature</span>
    </div>
    <div class="tc fea">
      <span class="tc-tag">Notifications</span>
      <div class="tc-name">SMS / WhatsApp Notifications</div>
      <span class="tc-pkg">Twilio · MSG91 · Infobip</span>
      <p class="tc-desc">In Bangladesh, SMS and WhatsApp are primary communication channels — email open rates are low. Appointment confirmations, order status updates, and payment receipts via SMS have significantly higher engagement. MSG91 and Infobip have Bangladesh carrier coverage. WhatsApp Business API is increasingly expected for healthcare bookings.</p>
      <span class="prio"><span class="pd soon"></span>High user-trust impact</span>
    </div>
    <div class="tc fea">
      <span class="tc-tag">User System</span>
      <div class="tc-name">Customer Auth &amp; Order History</div>
      <span class="tc-pkg">NextAuth.js v5 (Auth.js)</span>
      <p class="tc-desc">There's a partially-implemented user auth system. Upgrading to Auth.js v5 gives: phone/OTP login (critical for BD market), Google OAuth, persistent sessions, and a user portal for order history and appointment tracking. Auth.js v5 is fully App Router native and drops the older NextAuth v4 adapter complexity.</p>
      <span class="prio"><span class="pd soon"></span>Enables repeat-purchase flows</span>
    </div>
    <div class="tc fea">
      <span class="tc-tag">Analytics</span>
      <div class="tc-name">Privacy-First Analytics</div>
      <span class="tc-pkg">Plausible · PostHog (self-hosted)</span>
      <p class="tc-desc">No analytics beyond the existing product view/click tracking. Understanding which services are most viewed, where users drop off in checkout, and which blog posts drive appointment bookings is essential. Plausible is GDPR-compliant, cookie-free, and integrates with Next.js in 5 minutes. PostHog adds session replay, A/B testing, and feature flags.</p>
      <span class="prio"><span class="pd soon"></span>Plausible: 5min integration</span>
    </div>
    <div class="tc fea">
      <span class="tc-tag">Appointment System</span>
      <div class="tc-name">Live Appointment Booking with Availability</div>
      <span class="tc-pkg">Custom + doctor timetable integration</span>
      <p class="tc-desc">The appointment form currently <code>console.log</code>s and resets — no data is persisted. The timetable page exists but isn't connected to the booking flow. A complete booking system: doctor availability slots managed via admin, patient selects date/time, slot reserved in MongoDB, both parties receive confirmation. This is the highest-value incomplete feature in the project.</p>
      <span class="prio"><span class="pd now"></span>Core product feature — not yet implemented</span>
    </div>
    <div class="tc fea">
      <span class="tc-tag">Documents</span>
      <div class="tc-name">Invoice &amp; Receipt PDF Generation</div>
      <span class="tc-pkg">@react-pdf/renderer · jsPDF</span>
      <p class="tc-desc">Healthcare customers need printable order invoices and appointment summaries. <code>@react-pdf/renderer</code> generates PDFs from React components server-side — the same component tree used to render the web view can produce the PDF. Generate on demand via <code>/api/orders/[id]/invoice</code> and attach to confirmation emails.</p>
      <span class="prio"><span class="pd later"></span>Professional differentiator</span>
    </div>
    <div class="tc fea">
      <span class="tc-tag">Real-Time</span>
      <div class="tc-name">Real-Time Admin Notifications</div>
      <span class="tc-pkg">Pusher · Ably · Vercel KV + polling</span>
      <p class="tc-desc">When a new appointment is submitted or an order is placed, the admin has no real-time signal — they must manually refresh. A Pusher integration pushes a browser notification to the active admin session. The existing chat widget already polls — upgrading it to WebSocket removes the polling overhead and enables instant message delivery.</p>
      <span class="prio"><span class="pd later"></span>Pusher free tier: 200 connections</span>
    </div>
  </div>
</section>

<!-- ═══════════════════════════════ ROADMAP ═══════════════════════════════ -->
<section class="sec" id="roadmap">
  <div class="sec-hd">
    <span class="sec-title">Priority Roadmap</span>
    <span class="sec-tag sec">Ordered by Impact</span>
  </div>

  <div class="callout">
    <strong>Read this table first.</strong> The findings above are grouped by domain — this table re-orders everything by urgency. Items in Week 1 are blocking production deployment. Items in Week 2–3 are pre-launch polish. Week 4+ are post-launch compounding investments.
  </div>

  <div class="tbl-wrap">
    <table>
      <thead>
        <tr>
          <th>Task</th>
          <th>ID</th>
          <th>Effort</th>
          <th>Timeline</th>
          <th>Unlocks</th>
        </tr>
      </thead>
      <tbody>
        <tr><td>Rename <code>proxy.js</code> → <code>middleware.js</code>, fix export</td><td><span class="sbadge cr">C-1 / H-1</span></td><td class="effort">30 min</td><td class="wk">Week 1 Day 1</td><td>All security headers + admin page guard instantly active</td></tr>
        <tr><td>Add server-side JWT check to <code>admin/layout.js</code></td><td><span class="sbadge cr">C-3</span></td><td class="effort">1 hr</td><td class="wk">Week 1 Day 1</td><td>Admin shell never renders for unauthenticated users</td></tr>
        <tr><td>Fix order item price validation — look up from DB</td><td><span class="sbadge cr">C-4</span></td><td class="effort">2 hrs</td><td class="wk">Week 1 Day 2</td><td>Revenue integrity; no-one can buy anything at ৳1</td></tr>
        <tr><td>Sanitize <code>sessionKey</code> in chat routes (typeof check)</td><td><span class="sbadge hi">H-4</span></td><td class="effort">15 min</td><td class="wk">Week 1 Day 2</td><td>Closes NoSQL injection in public endpoint</td></tr>
        <tr><td>Add rate limiting to admin login + auth endpoints</td><td><span class="sbadge cr">C-2</span></td><td class="effort">3 hrs</td><td class="wk">Week 1</td><td>Brute-force protection; Upstash Redis setup for future use</td></tr>
        <tr><td>Add MongoDB connection timeouts</td><td><span class="sbadge md">M-5</span></td><td class="effort">15 min</td><td class="wk">Week 1</td><td>DB outage → 503 in &lt;5s instead of indefinite hang</td></tr>
        <tr><td>Escape regex in product search endpoint</td><td><span class="sbadge md">M-3</span></td><td class="effort">15 min</td><td class="wk">Week 1</td><td>Closes public ReDoS vector</td></tr>
        <tr><td>Add public content page allowlist</td><td><span class="sbadge md">M-4</span></td><td class="effort">10 min</td><td class="wk">Week 1</td><td>Stops DB enumeration via content route</td></tr>
        <tr><td>Add <code>withAdmin()</code> to admin content GET</td><td><span class="sbadge md">M-1</span></td><td class="effort">5 min</td><td class="wk">Week 1</td><td>Admin CMS data not publicly accessible</td></tr>
        <tr><td>Set up Sentry error monitoring</td><td>—</td><td class="effort">30 min</td><td class="wk">Week 1</td><td>Know when things break in production before users do</td></tr>
        <tr><td>Add <code>error.js</code> to 5 key route segments</td><td>—</td><td class="effort">2 hrs</td><td class="wk">Week 2</td><td>Crashes show recovery UI instead of blank page</td></tr>
        <tr><td>Add <code>loading.js</code> to 4 data-dependent routes</td><td>—</td><td class="effort">1 hr</td><td class="wk">Week 2</td><td>Instant feedback; no more frozen navigations</td></tr>
        <tr><td>Replace <code>next-pwa</code> with <code>@serwist/next</code></td><td><span class="sbadge md">M-6</span></td><td class="effort">2 hrs</td><td class="wk">Week 2</td><td>Supported PWA on App Router; faster dev builds</td></tr>
        <tr><td>Migrate Poppins &amp; Rubik to <code>next/font</code></td><td>—</td><td class="effort">2 hrs</td><td class="wk">Week 2</td><td>Eliminates FOIT/FOUT; auto-preload hints injected</td></tr>
        <tr><td>Integrate payment gateway (SSLCommerz / ShurjoPay)</td><td>—</td><td class="effort">3–5 days</td><td class="wk">Week 2–3</td><td>Real revenue from the shop</td></tr>
        <tr><td>Wire appointment form to DB; connect timetable</td><td>—</td><td class="effort">4–6 days</td><td class="wk">Week 2–3</td><td>Core product feature — currently a no-op</td></tr>
        <tr><td>Add transactional email (Resend + React Email)</td><td>—</td><td class="effort">1 day</td><td class="wk">Week 3</td><td>Order &amp; appointment confirmations sent automatically</td></tr>
        <tr><td>Migrate ~94 raw <code>&lt;img&gt;</code> tags to <code>next/image</code></td><td>—</td><td class="effort">4–6 hrs</td><td class="wk">Week 3</td><td>LCP, CLS, bandwidth — biggest single Core Web Vitals win</td></tr>
        <tr><td>Hash admin passwords (bcrypt) instead of plaintext env vars</td><td><span class="sbadge md">M-4</span></td><td class="effort">2 hrs</td><td class="wk">Week 3</td><td>Admin credentials survive env var exposure</td></tr>
        <tr><td>Add magic-bytes file type check on upload</td><td><span class="sbadge lo">L-3</span></td><td class="effort">1 hr</td><td class="wk">Week 3</td><td>Upload security beyond client-supplied MIME type</td></tr>
        <tr><td>Add admin audit log (who changed what)</td><td>—</td><td class="effort">1 day</td><td class="wk">Week 4</td><td>Compliance &amp; accountability for a healthcare platform</td></tr>
        <tr><td>Upgrade cookie: <code>__Host-</code> prefix + <code>SameSite=strict</code></td><td><span class="sbadge lo">L-1</span></td><td class="effort">20 min</td><td class="wk">Week 4</td><td>Closes subdomain shadowing + CSRF vector</td></tr>
        <tr><td>Add Redis caching for high-read public endpoints</td><td>—</td><td class="effort">1 day</td><td class="wk">Week 4–5</td><td>DB shielding under traffic; &lt;1ms cached reads</td></tr>
        <tr><td>Begin TypeScript migration (lib/ + models/ first)</td><td>—</td><td class="effort">Ongoing</td><td class="wk">Month 2+</td><td>Type safety catches entire class of runtime bugs</td></tr>
        <tr><td>Add Vitest unit tests for auth, validation, orders</td><td>—</td><td class="effort">Ongoing</td><td class="wk">Month 2+</td><td>Regression safety for security-critical paths</td></tr>
        <tr><td>Migrate media to Cloudflare R2 / Vercel Blob</td><td>—</td><td class="effort">2–3 days</td><td class="wk">Month 2+</td><td>CDN-served images; removes MongoDB media load entirely</td></tr>
        <tr><td>Integrate SMS notifications (MSG91 / Twilio)</td><td>—</td><td class="effort">1 day</td><td class="wk">Month 2+</td><td>BD-appropriate notification channel; higher open rate</td></tr>
        <tr><td>Upgrade to Auth.js v5 for customer auth</td><td>—</td><td class="effort">2–3 days</td><td class="wk">Month 3+</td><td>Phone/OTP login; Google OAuth; full user portal</td></tr>
      </tbody>
    </table>
  </div>
</section>

</main>
</div>
</div>

<script>
const sections = document.querySelectorAll('.sec[id]');
const links = document.querySelectorAll('.nav-a[href^="#"]');
const obs = new IntersectionObserver(entries => {
  entries.forEach(e => {
    if (e.isIntersecting) {
      links.forEach(l => l.classList.toggle('on', l.getAttribute('href') === '#' + e.target.id));
    }
  });
}, { rootMargin: '-20% 0px -70% 0px' });
sections.forEach(s => obs.observe(s));
document.querySelectorAll('.hbr-fill').forEach(b => {
  const w = b.style.width; b.style.width = '0';
  requestAnimationFrame(() => requestAnimationFrame(() => { b.style.width = w; }));
});
</script>
