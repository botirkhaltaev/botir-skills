# Trust & Safety UI Patterns

## The Trust Equation

```
Trust = Transparency × Control × Reliability
```

If any factor is zero, trust is zero.

## Confidence Score Calculation

| Factor | Weight | How to Measure |
|--------|--------|----------------|
| Iteration count | 20% | More iterations = more refined |
| Test pass rate | 30% | Higher pass rate = more reliable |
| Human override rate | 15% | Fewer overrides = more trusted |
| Historical accuracy | 25% | Past predictions that came true |
| Model confidence | 10% | LLM's own confidence score |

## Audit Trail Format

```
14:32:23  Copywriter  Generated headline v3
          "Your coffee got 10x greener" (94%)
14:32:45  Designer    Created visual concept
14:33:01  Tester      A/B test: 4.1% CTR (PASS)
```

**Access:** Default hidden. Swipe up or click "View Audit." Chronological, newest first.

## Approval Gate Examples

**Low risk (auto-approve):**
```
[ ✓ Approved automatically ]
```

**Medium risk (one-tap):**
```
┌──────────────────────────────┐
│  Approve this campaign?      │
│  Will reach 50,000 users.    │
│  [ Cancel ]  [ Approve ]     │
└──────────────────────────────┘
```

**High risk (multi-step):**
```
┌──────────────────────────────────────┐
│  ⚠️ High-Risk Action                │
│  Deploy to production (50K users)?  │
│  Type "DEPLOY" to confirm: [____]  │
│  [ Cancel ]        [ Deploy ]       │
└──────────────────────────────────────┘
```
