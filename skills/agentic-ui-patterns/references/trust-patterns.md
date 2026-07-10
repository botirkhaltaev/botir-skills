# Trust & Safety UI Patterns for Agentic Apps

## The Trust Equation (Simplified)

```
Trust = Transparency × Control × Reliability
```

- **Transparency** — User can see what agents are doing and why
- **Control** — User can stop, redirect, or override agents
- **Reliability** — Agents behave predictably and fail gracefully

If any factor is zero, trust is zero.

## Kill Switch Design

### Requirements
- **Visible** — Not buried in menus. Always within 2 taps.
- **Scoped** — Stop individual agents, not just "everything"
- **Immediate** — No confirmation dialog for emergency stop
- **Recoverable** — Easy to resume after stopping

### UI Pattern

```
Default state:    Small red dot in corner, pulsing when agents active

Halt state:       ┌──────────────────────────┐
                  │  🛑 AGENTS HALTED        │
                  │  4 agents stopped        │
                  │                          │
                  │ [Resume] [Review]        │
                  └──────────────────────────┘
```

### Implementation
```tsx
// Kill switch component
function KillSwitch({ agents, onHalt, onResume }) {
  const [halted, setHalted] = useState(false);
  
  if (halted) {
    return (
      <div className="bg-red-50 border border-red-200 rounded-lg p-4">
        <div className="flex items-center gap-2 text-red-700">
          <AlertOctagon size={16} />
          <span className="font-medium">AGENTS HALTED</span>
        </div>
        <p className="text-sm text-red-600 mt-1">
          {agents.length} agents stopped
        </p>
        <div className="flex gap-2 mt-3">
          <Button onClick={onResume} variant="outline">Resume</Button>
          <Button onClick={onReview}>Review Changes</Button>
        </div>
      </div>
    );
  }
  
  return (
    <button
      onClick={() => { onHalt(); setHalted(true); }}
      className="flex items-center gap-1.5 text-red-500 hover:text-red-700 
                 hover:bg-red-50 px-3 py-1.5 rounded-md transition-colors"
    >
      <StopCircle size={14} />
      <span className="text-sm">Halt Agents</span>
    </button>
  );
}
```

## Confidence Scoring UI

### Display Patterns

**Inline badge (subtle):**
```
"Your morning coffee just got greener" [94%]
                                        ↑ hover: "3 iterations, strong test results"
```

**Score bar (more prominent):**
```
Confidence: ████████████████████░░  94%
            High — trust this output
```

**Color-coded dot (minimal):**
```
● High confidence    (green)
● Medium confidence  (yellow)  
● Review recommended (orange)
● Human required     (red)
```

### Score Calculation Factors

| Factor | Weight | How to Measure |
|--------|--------|----------------|
| Iteration count | 20% | More iterations = more refined |
| Test pass rate | 30% | Higher pass rate = more reliable |
| Human override rate | 15% | Fewer overrides = more trusted |
| Historical accuracy | 25% | Past predictions that came true |
| Model confidence | 10% | The LLM's own confidence score |

## Audit Trail Design

### Minimal Audit Entry
```
14:32:23  Copywriter  Generated headline v3
          "Your coffee got 10x greener" (94%)
14:32:45  Designer    Created visual concept
          Lifestyle photo with hiking model
14:33:01  Tester      A/B test: 4.1% CTR (PASS)
```

### Audit Access Pattern
- Default: Hidden
- Access: Swipe up or click "View Audit" in trust overlay
- Format: Chronological log, newest first
- Filter: By agent, by action type, by time range

## Approval Gate Patterns

### Risk-Based Friction

| Risk | Example | UI Pattern |
|------|---------|------------|
| **None** | Internal draft | Auto-approve, show "Done" badge |
| **Low** | Social media post | One-tap "Approve" button |
| **Medium** | Customer email | Approve + brief confirmation |
| **High** | Production deploy | Approve + Face ID + typed confirmation |

### Approval UI Examples

**Low risk:**
```
[ ✓ Approved automatically ]
```

**Medium risk:**
```
┌──────────────────────────────┐
│  Approve this campaign?      │
│  Will reach 50,000 users.    │
│                              │
│  [ Cancel ]  [ Approve ]     │
└──────────────────────────────┘
```

**High risk:**
```
┌──────────────────────────────────────┐
│  ⚠️ High-Risk Action                │
│  Deploy to production (50K users)?  │
│                                      │
│  Type "DEPLOY" to confirm: [____]  │
│                                      │
│  [ Cancel ]        [ Deploy ]       │
└──────────────────────────────────────┘
```
