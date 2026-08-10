# CareBangla — EN/BN Translation System Guide

> For the verified language-provider, persistence, and runtime-fallback architecture, also see [Language_Translation_Process.md](Language_Translation_Process.md).

A complete reference for the English ↔ Bengali translation system built into this project.  
Covers architecture, day-to-day usage, adding new content, backend integration, and the Google Cloud setup.

---

## Table of Contents

1. [How it works](#1-how-it-works)
2. [File structure](#2-file-structure)
3. [Translation namespaces](#3-translation-namespaces)
4. [Using translations in a component](#4-using-translations-in-a-component)
5. [Adding new static content](#5-adding-new-static-content)
6. [Adding a new page](#6-adding-a-new-page)
7. [Integrating MongoDB / backend data](#7-integrating-mongodb--backend-data)
8. [Google Cloud Translation API setup](#8-google-cloud-translation-api-setup)
9. [Running the translation script](#9-running-the-translation-script)
10. [API key security](#10-api-key-security)
11. [Bengali font](#11-bengali-font)
12. [Language toggle UI](#12-language-toggle-ui)
13. [Troubleshooting](#13-troubleshooting)

---

## 1. How it works

The translation system is **hybrid**:

- reviewed English/Bengali dictionaries are the preferred source for stable public copy;
- `GoogleCloudTranslationFallback` translates remaining rendered UI text, placeholders, titles, alt text, and ARIA labels through the server-side `/api/translate` proxy;
- public/user and admin language preferences are intentionally independent.

```
User clicks "বাং"
      │
      ▼
The active LanguageContext sets lang = 'bn'
      │
      ├─► public/user: localStorage['cb_lang'] = 'bn'
      ├─► admin: localStorage['cb_admin_lang'] = 'bn'
      ├─► public provider updates <html lang>; admin provider is locally scoped
      └─► Every component reading useLanguage() re-renders with Bengali strings
          and the scoped runtime fallback translates remaining English UI
```

**Key design decisions:**
- Reviewed dictionary strings require no runtime API call; uncovered English UI uses the translation proxy
- Language preferences are remembered separately in `localStorage`, so an admin choice never changes the public/user site in the same browser
- Static non-translatable data (image URLs, hrefs, dates) is kept separate from translatable text
- Components that call `useLanguage()` must be Client Components; Server Components may pass translated/static content into client views
- Visitor-entered textarea content and contenteditable rich text are never machine-translated; their UI placeholders can still be translated

---

## 2. File structure

```
src/
└── i18n/
    ├── LanguageContext.jsx          # React Context Provider — wraps the entire app
    ├── useLanguage.js               # Custom hook used in every component
    ├── GoogleCloudTranslationFallback.jsx # Scoped runtime coverage for untranslated UI
    └── translations/
        ├── en.js                    # English strings — the source of truth
        ├── bn.js                    # Bengali strings — hand-crafted, authoritative
        └── bn.generated.js          # Google API output — reference only, not imported

scripts/
└── generate-translations.mjs       # Developer script: sends en.js to Google Cloud, writes bn.generated.js

src/app/admin/layout.js              # Nested provider: cb_admin_lang + admin-only translation scope
src/app/api/translate/route.js       # Server-only runtime translation proxy
```

> **Important:** `bn.js` is the active Bengali file. `bn.generated.js` is a draft output from the Google API. After running the script, review the generated output, copy the good parts into `bn.js`, and keep it as the canonical file.

---

## 3. Translation namespaces

Both `en.js` and `bn.js` export a single object with the following top-level namespaces:

| Namespace | Used by | Contents |
|---|---|---|
| `common` | Shared across all pages | Button labels, link texts, shared phrases |
| `nav` | Header | All navigation menu labels and CTA text |
| `footer` | Footer | All footer sections, link texts, copyright |
| `counter` | CounterSection | Stat boxes (number + title + iconUrl) |
| `home` | HomePage | Hero, services grid, tabs, blog preview, etc. |
| `about` | AboutPage | Page title, about text, stats, CTA section |
| `services` | ServicePage | Services listing page content |
| `contact` | ContactPage | Contact page headings and hotline info |
| `appointments` | Appointments | Form labels, placeholders, validation errors, service options |
| `blog` | BlogsPage | Blog listing — post titles, excerpts, meta |
| `medicalShop` | ServiceDetailsMedicalEquipment, MedicalEquipmentPartners | All medical shop / equipment page content |

---

## 4. Using translations in a component

### Basic usage

```jsx
'use client';                          // required for any component using Context

import useLanguage from '../../i18n/useLanguage';

const MyComponent = () => {
  const { lang, setLang, t } = useLanguage();

  return (
    <div>
      <h1>{t.home.heroTitle}</h1>
      <p>{t.common.learnMore}</p>
    </div>
  );
};
```

### Mixing static data with translated text

Some data objects contain both static values (image URLs, hrefs) and translatable text.  
Keep them separate — static data at module level, translated text from `t.*`, merged inside the component:

```jsx
'use client';

import useLanguage from '../../i18n/useLanguage';

// Static — never changes between languages
const serviceImages = [
  '/images/service-1.jpg',
  '/images/service-2.jpg',
  '/images/service-3.jpg',
];

const ServicesSection = () => {
  const { t } = useLanguage();

  // Merge static + translated on every render
  const services = t.services.items.map((item, i) => ({
    ...item,              // title, description from translations
    image: serviceImages[i],   // static — same in both languages
  }));

  return (
    <ul>
      {services.map((svc) => (
        <li key={svc.title}>
          <img src={svc.image} alt={svc.title} />
          <h3>{svc.title}</h3>
          <p>{svc.description}</p>
        </li>
      ))}
    </ul>
  );
};
```

---

## 5. Adding new static content

### Step 1 — Add English strings to `en.js`

Open `src/i18n/translations/en.js` and add your strings under the appropriate namespace.  
If the content doesn't fit an existing namespace, add a new one at the bottom.

```js
// src/i18n/translations/en.js

export const en = {
  // ... existing namespaces ...

  // New namespace example
  faq: {
    pageTitle: 'Frequently Asked Questions',
    subtitle: 'Find answers to common questions about our services',
    items: [
      {
        question: 'How do I book a home nurse?',
        answer: 'You can book through our Appointments page or call our 24/7 hotline.',
      },
      {
        question: 'What areas do you cover?',
        answer: 'We currently serve all major districts of Dhaka division.',
      },
    ],
  },
};
```

### Step 2 — Add Bengali strings to `bn.js`

Option A — Translate manually (recommended for best quality):
```js
// src/i18n/translations/bn.js

export const bn = {
  // ... existing namespaces ...

  faq: {
    pageTitle: 'সাধারণ জিজ্ঞাসা',
    subtitle: 'আমাদের সেবা সম্পর্কে সাধারণ প্রশ্নের উত্তর খুঁজুন',
    items: [
      {
        question: 'হোম নার্স কীভাবে বুক করব?',
        answer: 'আপনি আমাদের অ্যাপয়েন্টমেন্ট পেজের মাধ্যমে বা আমাদের ২৪/৭ হটলাইনে কল করে বুক করতে পারবেন।',
      },
      {
        question: 'আপনারা কোন কোন এলাকায় সেবা দেন?',
        answer: 'আমরা বর্তমানে ঢাকা বিভাগের সব প্রধান জেলায় সেবা প্রদান করি।',
      },
    ],
  },
};
```

Option B — Use the Google Cloud script to generate a draft (see [Section 9](#9-running-the-translation-script)), then copy and refine the output.

### Step 3 — Use in your component

```jsx
const { t } = useLanguage();
return <h1>{t.faq.pageTitle}</h1>;
```

---

## 6. Adding a new page

1. Create your App Router page file, e.g. `src/app/example/page.js`
2. Add `'use client'` at the very top (first line)
3. Import and use the hook:

```jsx
'use client';

import useLanguage from '@/i18n/useLanguage';

const ExamplePage = () => {
  const { t } = useLanguage();

  return (
    <div>
      <h1>{t.nav.home}</h1>
    </div>
  );
};

export default ExamplePage;
```

> Pages that don't use any translations don't need `'use client'` — only add it when you use the hook.

---

## 7. Integrating MongoDB / backend data

When the backend is connected and content comes from MongoDB, you have two options:

### Option A — Field-per-language convention (recommended)

Store each translatable field in both languages in the database:

```json
{
  "_id": "...",
  "slug": "home-nursing",
  "title_en": "Registered Home Nursing",
  "title_bn": "নিবন্ধিত হোম নার্সিং",
  "description_en": "Professional nursing care at home...",
  "description_bn": "বাড়িতে পেশাদার নার্সিং সেবা..."
}
```

Then select the right field in your component:

```jsx
'use client';

import useLanguage from '../../i18n/useLanguage';

const ServiceCard = ({ service }) => {
  const { lang } = useLanguage();

  const title = lang === 'bn' ? service.title_bn : service.title_en;
  const description = lang === 'bn' ? service.description_bn : service.description_en;

  return (
    <div>
      <h3>{title}</h3>
      <p>{description}</p>
    </div>
  );
};
```

Or write a small helper to avoid repetition:

```js
// src/i18n/useLocalizedField.js
import useLanguage from './useLanguage';

export function useLocalizedField() {
  const { lang } = useLanguage();
  return (obj, field) => obj[`${field}_${lang}`] ?? obj[`${field}_en`];
}

// Usage:
// const loc = useLocalizedField();
// loc(service, 'title')   → service.title_bn when lang is 'bn'
```

### Option B — Deep merge into LanguageContext (advanced)

The `LanguageProvider` in `src/i18n/LanguageContext.jsx` can be extended to accept a `dynamicTranslations` prop.  
These would deep-merge over the static `en.js` / `bn.js` objects, letting API data override specific keys:

```jsx
// In LanguageContext.jsx — extended version
export function LanguageProvider({ children, dynamicTranslations = {} }) {
  // ... existing state ...

  const mergedTranslations = useMemo(() => {
    const base = translations[lang];
    const dynamic = dynamicTranslations[lang] ?? {};
    return deepMerge(base, dynamic);   // your own deepMerge util
  }, [lang, dynamicTranslations]);

  return (
    <LanguageContext.Provider value={{ lang, setLang: changeLang, t: mergedTranslations }}>
      {children}
    </LanguageContext.Provider>
  );
}
```

Then in `layout.js`, fetch CMS content server-side and pass it down:

```jsx
// src/app/layout.js
import { LanguageProvider } from '../i18n/LanguageContext';

export default async function RootLayout({ children }) {
  // fetch CMS translations from your backend
  const cmsTranslations = await fetchCMSTranslations();

  return (
    <html>
      <body>
        <LanguageProvider dynamicTranslations={cmsTranslations}>
          {children}
        </LanguageProvider>
      </body>
    </html>
  );
}
```

---

## 8. Google Cloud Translation API setup

The API key is stored in `.env.local` and is used in two server-side contexts:

1. `scripts/generate-translations.mjs` for offline dictionary drafting;
2. `/api/translate` for the runtime fallback when rendered UI copy is not covered by the reviewed dictionaries.

The browser calls the application proxy, never Google directly, so the private key stays out of the client bundle.

### First-time setup for a new developer

1. Ask the project owner for the API key (it is not committed to git)

2. Create `.env.local` in the project root (if it doesn't exist):
   ```
   GOOGLE_TRANSLATE_API_KEY=your_key_here
   ```

3. Make sure `.env.local` is in `.gitignore` — it should already be, but verify:
   ```bash
   cat .gitignore | grep env.local
   ```

### Setting up your own Google Cloud key (if you need a fresh one)

1. Go to [console.cloud.google.com](https://console.cloud.google.com)
2. Select your project (or create one)
3. Navigate to **APIs & Services → Library**
4. Search for **"Cloud Translation API"** and click **Enable**
5. Go to **APIs & Services → Credentials → + Create Credentials → API Key**
6. Copy the generated key into `.env.local`:
   ```
   GOOGLE_TRANSLATE_API_KEY=AIza...your_new_key
   ```
7. **Restrict the key immediately** (see [Section 10](#10-api-key-security))

### Free tier limits

| Metric | Free tier |
|---|---|
| Characters per month | 500,000 |
| Entire CareBangla site (~374 strings) | ~25,000 chars |
| Estimated cost per full site run | $0.00 |
| Cost beyond free tier | $20 per 1,000,000 chars |

Running the script once for the whole site uses about 5% of the monthly free allowance.

---

## 9. Running the translation script

The script reads `en.js`, sends all strings to the Google Cloud Translation API, and writes the result to `bn.generated.js`.

### Prerequisites

- Node.js 18 or later (supports `--env-file` natively — no `dotenv` needed)
- A valid `GOOGLE_TRANSLATE_API_KEY` in `.env.local`
- Google Cloud Translation API enabled for your project

### Run the script

From the project root:

```bash
node --env-file=.env.local scripts/generate-translations.mjs
```

Expected output:

```
✅  Found 374 translatable strings
🌐  Translating en → bn via Google Cloud Translation API …
  Batch 1/4 (100 strings) … ✓
  Batch 2/4 (100 strings) … ✓
  Batch 3/4 (100 strings) … ✓
  Batch 4/4 (74 strings) … ✓
🎉  Done! Output written to: .../src/i18n/translations/bn.generated.js
```

### After running the script

1. Open `src/i18n/translations/bn.generated.js` — this is the raw API output
2. Open `src/i18n/translations/bn.js` alongside it
3. For **new strings you added**, copy those keys from `bn.generated.js` into `bn.js`
4. Review the phrasing — Google's translation is a good starting point but may use more formal or Indian-influenced Bengali. In particular:
   - Prefer **"সেবা"** over "পরিষেবা" for services in a Bangladeshi healthcare context
   - Prefer **"ডাক্তারগণ"** over "ডাক্তাররা" for a professional medical brand
   - Prefer **"যোগাযোগ"** over "যোগাযোগ করুন" for navigation labels
5. `bn.js` is the authoritative file — `bn.generated.js` is a draft aid only

### When to re-run the script

- After adding new static content to `en.js`
- Before a major release, to catch any missing Bengali translations
- You do **not** need to re-run it just to switch between English and Bengali in the browser

---

## 10. API key security

### Restrict your key in Google Cloud Console

1. Go to **APIs & Services → Credentials**
2. Click your Translation API key
3. Under **API restrictions** → select **Restrict key** → choose **Cloud Translation API**
4. Click **Save**

This ensures the key can only be used for translation — not Maps, Gmail, or any other Google service.

### What NOT to do

- Never commit `.env.local` to git
- Never use `NEXT_PUBLIC_GOOGLE_TRANSLATE_API_KEY` — the `NEXT_PUBLIC_` prefix would expose it in the browser bundle
- Never paste the key in Slack, GitHub issues, or chat messages

### Key rotation

If a key is ever accidentally exposed:
1. Go to Google Cloud Console → Credentials
2. Click the key → **Regenerate key** (or delete and create a new one)
3. Update `.env.local` with the new key
4. Redeploy or restart the running app so `/api/translate` begins using the replacement key

---

## 11. Bengali font

Bengali text requires a dedicated font. The app loads **Hind Siliguri** from Google Fonts.

**Configuration in `src/app/layout.js`:**
```js
import { Hind_Siliguri } from 'next/font/google';

const hindSiliguri = Hind_Siliguri({
  subsets: ['bengali'],
  weight: ['300', '400', '500', '600', '700'],
  variable: '--font-hind-siliguri',
  display: 'swap',
});
```

**Activation in `src/sass/common/_general.scss`:**
```scss
html[lang="bn"] {
  font-family: var(--font-hind-siliguri), sans-serif;

  h1, h2, h3, h4, h5, h6, p, span, li, a, button,
  input, select, textarea, label {
    font-family: var(--font-hind-siliguri), sans-serif;
  }
}
```

The font applies automatically when the user switches to Bengali — no extra work needed.  
When adding new element types that need Bengali text, add them to the selector list above.

---

## 12. Language toggle UI

The public/user EN / বাং toggle is rendered in `src/Components/Header/Header.jsx`. The admin header in `AdminClientLayout.jsx` renders its own toggle inside the nested admin provider.

```jsx
const { lang, setLang } = useLanguage();

<div className="cs_lang_toggle" role="group" aria-label="Language switcher">
  <button className={lang === 'en' ? 'active' : ''} onClick={() => setLang('en')}>
    EN
  </button>
  <button className={lang === 'bn' ? 'active' : ''} onClick={() => setLang('bn')}>
    বাং
  </button>
</div>
```

Styles are in `src/sass/common/_general.scss` under `.cs_lang_toggle`.  
Active state is highlighted with `--primary-color` background (dark blue `#002261`).

Persistence and scope:

| Surface | Storage key | Translation scope |
|---|---|---|
| Public site and `/user/**` | `cb_lang` | Root/public document, excluding nested language scopes |
| `/admin/**` | `cb_admin_lang` | `#cb-admin-language-scope` |

The admin provider sets `syncDocumentLang={false}` so its choice does not overwrite the document language owned by the public/root provider. This separation is deliberate even when both surfaces are open in the same browser.

---

## 13. Troubleshooting

### Text is not translating on a page

- Check that the page file has `'use client'` as its **first line** (before any imports)
- Check that the component imports `useLanguage` from the correct relative path
- Check that the key you're reading actually exists in both `en.js` and `bn.js`

### Bengali font is not rendering (showing squares or wrong glyphs)

- Confirm the `hindSiliguri` variable is in the `html` className in `layout.js`
- Open DevTools → Elements and check `<html lang="bn">` — if it reads `"en"` after switching, the `LanguageContext` update isn't reaching the DOM
- Check that `LanguageProvider` wraps `{children}` in `layout.js`

### Script error: `GOOGLE_TRANSLATE_API_KEY is not set`

- Ensure `.env.local` exists in the project root (not inside `src/`)
- Confirm the key name is exactly `GOOGLE_TRANSLATE_API_KEY` (no `NEXT_PUBLIC_` prefix)
- Use `node --env-file=.env.local` (Node 18+); if on an older Node version, install `dotenv` and update the script

### Script error: `403 API key not valid`

- The key may be restricted to specific APIs. Check Google Cloud Console → Credentials → your key → API restrictions — make sure **Cloud Translation API** is in the allowed list
- The key may be restricted to specific referrer domains. For a Node.js script (not a browser), remove any HTTP referrer restrictions

### Language resets to English on every visit

- Check that `localStorage` is available in the user's browser (private mode may block it)
- Inspect the correct key: `cb_lang` for public/user pages, `cb_admin_lang` for admin pages
- The `try/catch` in `LanguageContext.jsx` silently ignores `localStorage` errors — this is intentional to avoid crashes, but means the preference won't persist if storage is blocked

### Changing admin language also changes the public site

- Confirm `/admin/layout.js` wraps the admin shell with `storageKey="cb_admin_lang"`
- Confirm the admin wrapper has `id="cb-admin-language-scope"` and `data-language-scope`
- Confirm `GoogleCloudTranslationFallback` still ignores nodes belonging to another `[data-language-scope]`

### New namespace key is `undefined` at runtime

- You added the key to `en.js` but forgot to add the matching key to `bn.js`
- When `bn.js` is missing a key and `lang === 'bn'`, the lookup returns `undefined` which renders as blank
- Always add keys to **both** files before using them in a component
