# Care Bangla — Localization Engineering Guide

> Developer/content implementation guide for the private system. [Return to the technical overview](README.md).

## 1. Use the correct localization layer

| Content class | Preferred implementation | Do not use |
|---|---|---|
| Repeated UI labels, buttons, validation text | Translation dictionary namespace + `useLanguage().t()` | Duplicated component literals. |
| CMS/public editorial content | Explicit language fields or structured localized values in the relevant model | Runtime DOM substitution. |
| Product specifications/model numbers | Translate explanatory labels; preserve factual identifiers intentionally | Blind transliteration/translation of codes. |
| Medical/legal/payment copy | Human-reviewed language version | Unreviewed automatic output. |
| Dates, tables, date pickers | Library/formatter locale configuration | Plain text dictionary keys alone. |

## 2. Dictionary and namespace conventions

The private system uses nested English/Bengali dictionaries and a language hook. Keep paths semantic and stable; use the same structural key path in both language dictionaries.

```ts
// Example shape, not copied from source.
const en = {
  booking: {
    review: { title: 'Review your request', submit: 'Submit request' },
  },
};

const bn = {
  booking: {
    review: { title: 'আপনার অনুরোধ পর্যালোচনা করুন', submit: 'অনুরোধ জমা দিন' },
  },
};
```

```tsx
const { t } = useLanguage();
return <button type="submit">{t('booking.review.submit')}</button>;
```

Rules:

- Namespace by product area (`common`, `navigation`, `booking`, `shop`, `account`, `admin`) rather than component file name.
- Add the English and Bengali key in the same change; avoid a silent source-language fallback for critical actions.
- Keep interpolation identifiers stable and document the data shape a translation expects.
- Avoid placing dynamic database entities inside dictionary files.

## 3. Localized database content

For durable content, use explicit field-level localization rather than attempting a deep merge between CMS records and static UI dictionaries.

```ts
// Conceptual field-per-language convention.
type LocalizedService = {
  title_en: string;
  title_bn: string;
  description_en: string;
  description_bn: string;
};

const pick = (record, lang, field) =>
  record[`${field}_${lang}`] ?? record[`${field}_en`] ?? record[field];
```

This pattern keeps MongoDB records queryable, makes editorial status visible, avoids context-size bloat, and provides a predictable rendering fallback. Structured `InlineText`/`ImageValue` content needs the same normalization discipline in each language variant.

## 4. Review and test checklist

| Check | Developer test |
|---|---|
| Key parity | Every required English key has Bengali equivalent and vice versa. |
| Fallback | Missing optional localized data renders intentional fallback, not `undefined`. |
| Persistence | Public/customer selection survives navigation/reload; staff selection remains independent. |
| Typography | Hind Siliguri loads; Bengali wraps correctly on small screens. |
| Interaction | Form errors, button labels, cart modes, statuses, and date controls are understandable in both languages. |
| Metadata | Localized title/description/social preview follows route/content policy. |
| Accessibility | `lang` attributes, readable labels, focus order, and no layout shift from language changes. |

## 5. Automation boundary

The private application includes translation support tooling and can call a server-side translation provider for uncatalogued interface text. Treat generated output as a proposed draft. Never expose client-side credentials; validate server input/output, cache only with a clear invalidation/retention plan, and do not route personal or sensitive health information through an unapproved translator.

## 6. Troubleshooting map

| Symptom | Likely source | First diagnostic |
|---|---|---|
| Raw key visible | Missing namespace/key or provider not mounted | Inspect dictionary parity and provider tree. |
| Admin language affects public site | Incorrect shared storage/context | Confirm separate admin/public keys and scope. |
| Bengali glyphs/squares | Font not loaded or CSS overwritten | Inspect font network/style and fallback stack. |
| Flash of English | Asynchronous runtime fallback | Move high-frequency UI strings into local dictionary/cache. |
| Search preview wrong language | Metadata route uses default copy | Review server metadata and canonical/locale policy. |

## Public boundary

This guide intentionally excludes translation-provider credentials, commands, source file contents, and private configuration. It documents the conventions a developer should preserve in the private codebase.
