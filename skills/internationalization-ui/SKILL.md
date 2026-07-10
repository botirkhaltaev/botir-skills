---
name: internationalization-ui
description: >
  Design interfaces that work across languages, scripts, and cultures. Covers
  RTL layouts, text expansion, date/number/currency formatting, iconography,
  color culture, and the i18n implementation checklist. Use when building for
  multiple languages or regions. Triggers on: "internationalization", "i18n",
  "RTL", "right-to-left", "localization", "multi-language", "translation",
  "global design".
---

# Internationalization (i18n) UI

Design so your interface works in every language — including ones you can't read. i18n is not translation; it's designing for variability.

## Core Principle: Design for the Worst Case

Design for the language that expands most, scripts that flow opposite, and text that might double in length.

## Text Expansion

English is compact. Most languages are longer.

| Target Language | Expansion from English |
|-----------------|----------------------|
| **German** | +20-35% |
| **French** | +15-20% |
| **Spanish** | +15-25% |
| **Portuguese** | +20-30% |
| **Japanese** | -30-50% (shorter!) |
| **Chinese** | -30-50% (shorter!) |
| **Arabic** | +5-15% (but RTL) |
| **Hindi** | +20-50% (complex scripts) |

### Design Rules
- **Buttons:** Allow wrapping or truncation with ellipsis. Fixed-width buttons will break.
- **Headlines:** Test with 150% of English length
- **Navigation:** Horizontal nav breaks first. Design for wrapping or truncation.
- **Forms:** Labels above inputs handle expansion better than left-aligned labels
- **Tables:** Allow column resizing or horizontal scroll

## RTL (Right-to-Left) Languages

Arabic, Hebrew, Persian, Urdu, and others read right-to-left.

### What Flips
- **Text alignment** — right-aligned
- **Layout direction** — flex/grid `direction: rtl`
- **Navigation** — logo right, actions left
- **Icons with directional meaning** — arrows, back buttons, progress bars
- **Form inputs** — text entry starts from right
- **Sliders** — minimum on right, maximum on left

### What Does NOT Flip
- **Media playback controls** — play still points right
- **Circular progress** — still clockwise
- **Charts** — still left-to-right (time flows left-to-right universally)
- **Brand logos** — never flip
- **Numbers** — still LTR within RTL text

### Implementation
```css
/* Logical properties handle this automatically */
.margin-inline-start { /* replaces margin-left */ }
.margin-inline-end { /* replaces margin-right */ }
.text-align-start { /* replaces text-align: left */ }
.border-inline-start { /* replaces border-left */ }
```

Use `dir="rtl"` on the `<html>` element and CSS logical properties everywhere.

## Date, Time, Number, and Currency

Never hardcode formats. Use locale-aware formatting:

| Element | USA | Germany | Japan |
|---------|-----|---------|-------|
| **Date** | 12/31/2024 | 31.12.2024 | 2024/12/31 |
| **Time** | 2:30 PM | 14:30 | 14:30 |
| **Number** | 1,234.56 | 1.234,56 | 1,234.56 |
| **Currency** | $1,234.56 | 1.234,56 € | ¥1,235 |

**Always use `Intl` API or libraries like `date-fns`, `moment` (deprecated), or framework-specific i18n libraries.**

## Iconography & Imagery

| Element | Guideline |
|---------|-----------|
| **Icons** | Use universally understood icons. A thumbs-up is not positive in all cultures. |
| **Hand gestures** | Avoid — meanings vary wildly across cultures |
| **Animals** | Research symbolism (e.g., owl = wisdom in West, bad luck in some East) |
| **Colors** | Don't rely on color meaning alone. Red = danger (West), luck (China), mourning (South Africa) |
| **People in photos** | Represent diversity. Avoid stock photos that are obviously from one region |
| **Flags** | Don't use flags for language selection (a country may have multiple languages) |

## Content Considerations

- **Avoid idioms** — "Piece of cake" doesn't translate
- **Avoid text in images** — it can't be translated
- **Provide context for translators** — "Run" (a command) vs "Run" (a process)
- **Pluralization rules** — Some languages have multiple plural forms (Polish, Russian, Arabic)
- **Gendered language** — Some languages gender everything. Use gender-neutral where possible.

## Implementation Checklist

- [ ] All UI text externalized (no hardcoded strings)
- [ ] Layout uses logical properties (margin-inline, padding-inline, inset)
- [ ] Flexbox/grid handles direction change
- [ ] Buttons allow text wrapping or have max-width + ellipsis
- [ ] Navigation handles longer labels
- [ ] Date/time/number formatted with locale API
- [ ] Currency formatting uses locale
- [ ] RTL tested with actual content (not just `dir="rtl"`)
- [ ] Icons reviewed for cultural meaning
- [ ] Images don't contain untranslatable text
- [ ] Pluralization handled for all target languages
- [ ] Font supports all target scripts (not all fonts support Arabic, Devanagari, CJK)

## Related Skills

- For design tokens supporting multi-locale → use `design-systems`
- For typography considerations → use `ui-visual-design`
- For accessibility across languages → use `a11y-accessibility-design`
