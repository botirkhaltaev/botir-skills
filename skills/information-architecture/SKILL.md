---
name: information-architecture
description: >
  Structure content and navigation so users find what they need without thinking.
  Covers card sorting, tree testing, navigation hierarchies, content modeling,
  labeling systems, and the core IA heuristics. Use when organizing complex
  products, designing navigation, restructuring menus, or building sitemaps.
  Triggers on: "information architecture", "card sorting", "navigation design",
  "site structure", "content hierarchy", "menu organization", "tree testing",
  "findability".
---

# Information Architecture

Structure content so users find what they need without thinking. IA is the skeleton of every digital product.

> "The art and science of organizing and labeling websites, intranets, online communities, and software to support usability and findability." — Peter Morville & Louis Rosenfeld

## The 4 Core Systems

| System | Purpose | Example |
|--------|---------|---------|
| **Organization** | How content is grouped and categorized | Topics, tasks, audiences, metaphors |
| **Labeling** | How categories and links are represented | "Settings" vs "Preferences" vs "Account" |
| **Navigation** | How users move through the structure | Global nav, local nav, breadcrumbs, contextual links |
| **Search** | How users find content directly | Search box, filters, facets, autocomplete |

## Organization Schemes

### Exact Schemes (unambiguous, no interpretation needed)
- **Alphabetical** — glossaries, directories, indexes
- **Chronological** — blogs, news, timelines, activity feeds
- **Geographical** — store locators, travel sites, weather

### Ambiguous Schemes (require judgment, but more flexible)
- **Topic** — the most common scheme. Group by subject matter.
- **Task** — group by what users want to accomplish. Best for tools and applications.
- **Audience** — separate paths for different user types. Risky: users may not self-identify correctly.
- **Metaphor** — organize like a physical thing (desktop, file cabinet, store). Use sparingly; metaphors break down.

### Hybrid Schemes
Most products use a combination. Example: An e-commerce site uses topic (departments) + task ("Shop by Room") + audience ("For Professionals").

## Card Sorting

A method for discovering how users naturally group content.

### Types
| Type | Best For | How |
|------|----------|-----|
| **Open** | Discovering categories | Users create their own group names |
| **Closed** | Validating a structure | Users sort into predefined categories |
| **Hybrid** | Refining known structures | Users sort into preset groups, can add new ones |

### Best Practices
- **15-30 cards** per session (more = fatigue)
- **Randomize card order** for each participant
- **Use think-aloud protocol** in moderated sessions
- **5-15 participants** for reliable patterns
- **Keep sessions under 45 minutes**

### Analyzing Results
- **Agreement matrices** — how often participants grouped items together
- **Dendrograms** — visual hierarchy of relationships
- **Similarity matrices** — which items are most closely related
- **Look for strong trends**, not perfect consensus

## Tree Testing

Validate a navigation structure without visual design. Users are given a task and navigate a text-only hierarchy.

### Metrics
| Metric | What It Tells You | Target |
|--------|-------------------|--------|
| **Success rate** | % who found the correct destination | >80% |
| **Directness** | % who went straight there without backtracking | >70% |
| **Time** | Average time to complete | Context-dependent |

### When to Use
- After card sorting, before visual design
- When redesigning existing navigation
- When adding new sections to an existing structure

## Navigation Patterns

| Pattern | Best For | Avoid When |
|---------|----------|------------|
| **Global nav** | Top-level sections visible everywhere | >7 items |
| **Local nav** | Sub-sections within a category | Flat sites with no hierarchy |
| **Breadcrumbs** | Deep hierarchies (3+ levels) | Shallow sites |
| **Mega menus** | Many sub-categories under one section | <10 sub-items |
| **Faceted nav** | E-commerce, filtering by attributes | Content-heavy non-filterable sites |
| **Contextual links** | Related content within body text | Primary navigation |

### Navigation Heuristics
- **3-click rule** — any content within 3 clicks from home (guideline, not law)
- **Current state visible** — users must always know where they are
- **Consistent placement** — nav doesn't move between screens
- **Progressive disclosure** — show top levels first, reveal depth on demand

## Content Modeling

Define the structure of content types before designing pages.

### Process
1. **Inventory** — list all existing content
2. **Audit** — evaluate quality, relevance, ownership
3. **Define types** — what kinds of content exist? (article, product, event, profile)
4. **Define attributes** — what fields does each type have?
5. **Map relationships** — how do types connect?

### Example: Article Content Type
```
Article
├── Title (text, required)
├── Author (reference → Person)
├── Publish date (date, required)
├── Category (reference → Category)
├── Tags (list of text)
├── Featured image (media)
├── Summary (text, 160 chars max)
├── Body (rich text)
└── Related articles (list of references → Article)
```

## Labeling Systems

### Rules for Good Labels
- **User language, not internal jargon** — test with actual users
- **Consistent grammar** — all verbs or all nouns, not mixed
- **Specific, not abstract** — "Save" not "Submit", "Buy" not "Transact"
- **Scannable** — front-load important words
- **Mutually exclusive** — categories shouldn't overlap in meaning

### Synonym Ring
Map terms users might use to your canonical labels:
- "Settings" = "Preferences" = "Options" = "Account" → canonical: "Settings"

## Related Skills

- For interaction patterns → use `interaction-design-patterns`
- For cross-platform navigation differences → use `cross-platform-design`
- For mobile-specific navigation → use `mobile-touch-design`
