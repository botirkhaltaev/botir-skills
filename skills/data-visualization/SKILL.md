---
name: data-visualization
description: >
  Present data clearly and honestly. Covers chart selection (which chart for
  which question), dashboard design, data-to-ink ratio, preattentive
  processing, color for data, and common visualization mistakes. Use when
  building dashboards, reports, analytics features, or any data-heavy
  interface. Triggers on: "chart selection", "dashboard design", "data viz",
  "visualization", "graph design", "analytics UI", "data presentation".
---

# Data Visualization

Present data so users reach correct conclusions faster. Bad visualization misleads; good visualization reveals.

## Chart Selection Matrix

Match the chart to the question, not the other way around.

| Question | Best Chart | Avoid |
|----------|-----------|-------|
| How does this change over time? | Line chart | Pie chart, bar for continuous time |
| Which categories are highest/lowest? | Sorted bar chart | Pie chart (hard to compare slices) |
| Do two variables move together? | Scatterplot | Dual-axis line chart (implies false causation) |
| What's the composition of a total? | Stacked bar, treemap | Pie chart with >5 slices |
| What's the exact value? | Table with conditional formatting | Charts requiring visual estimation |
| Where are exceptions? | KPI card with conditional color | Dense dashboards with no hierarchy |
| How is data distributed? | Histogram, box plot | Bar chart for distributions |
| What's the relationship across 2 dimensions? | Heatmap | Bubble charts (hard to compare area) |

### Chart Selection Rules
- **Line charts** — continuous data over time. Max 3-4 lines. Use small multiples if more.
- **Bar charts** — comparing magnitudes across categories. Always sort bars (unless time-based).
- **Pie charts** — avoid. Humans can't compare angles. Use stacked bars instead.
- **Scatterplots** — correlation and distribution. Add trendline when showing correlation.
- **Tables** — precise lookup, not pattern detection. Add conditional formatting for scanning.

## Dashboard Design

### Layout Principles
- **Headline KPIs first** — top-left corner gets the most attention
- **6-8 charts max per view** — more = cognitive overload
- **Group by topic** — proximity signals relationship
- **Progressive disclosure** — summary first, detail on demand
- **5-second rule** — viewer should understand status in 5 seconds

### Dashboard Types

| Type | Purpose | Density | Audience |
|------|---------|---------|----------|
| **Executive** | Status at a glance | Low | Leadership |
| **Operational** | Monitor and respond | High | Operations teams |
| **Analytical** | Explore and investigate | Medium | Analysts |

### Information Density by Audience
- **Executive** — headline numbers, trends, exceptions only
- **Operational** — real-time metrics, thresholds, alerts
- **Analytical** — filters, drill-down, comparisons, raw data access

## Preattentive Processing

The visual properties the brain processes before conscious attention (~150ms):

| Property | Use For |
|----------|---------|
| **Color hue** | Categorization (this vs. that) |
| **Color intensity** | Quantity (more vs. less) |
| **Position** | Rank, sequence |
| **Length** | Magnitude |
| **Size** | Importance, quantity |
| **Shape** | Category differentiation |

### Rules
- Use **one preattentive attribute per message**
- Red + green is problematic for colorblind users (8% males) — use blue/orange instead
- Motion draws attention immediately — use only for alerts

## Color for Data

### Sequential (ordered data, low to high)
Single hue, varying lightness: light = low, dark = high

### Diverging (ordered data with meaningful midpoint)
Two hues with neutral midpoint: blue = low, white/grey = middle, red = high

### Categorical (unordered groups)
Distinct hues with equal lightness. Max 6-8 categories.

### Color Rules
- **Never use rainbow scales** — perceptually uneven, misleading
- **Color is not the only signal** — add pattern, label, or position
- **Test with colorblind simulators** — Deuteranopia, Protanopia
- **Consistent meaning** — red always = bad/warning across the product

## Data-to-Ink Ratio

Minimize non-data ink. Every pixel should convey information.

| Remove | Keep |
|--------|------|
| 3D effects | Clean 2D shapes |
| Gridlines (heavy) | Light gridlines or none |
| Background patterns | White/clean background |
| Decorative borders | Minimal axis lines |
| Legend (if direct-label possible) | Direct data labels |
| Chart junk (ornaments) | Clear data marks |

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Truncated Y-axis | Always start at zero for bar charts |
| Dual-axis charts | Use two separate charts or a scatterplot |
| Inconsistent color meaning | Document color semantics |
| Missing labels/context | Every chart needs a title, axis labels, units |
| Overplotting | Sample, bin, or use transparency |
| Cherry-picking time ranges | Show representative windows |

## Related Skills

- For visual hierarchy → use `ui-visual-design`
- For color theory → use `ui-visual-design`
- For accessibility in data viz → use `a11y-accessibility-design`
