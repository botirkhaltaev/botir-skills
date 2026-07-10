# Animation Patterns for AI-Native UI

## Principles

- Motion should have **purpose** — never decorative
- Duration: **200-400ms** for micro-interactions, **400-800ms** for layout changes
- Always respect `prefers-reduced-motion`
- Animate **whole blocks**, not per-token (prevents motion sickness)

## Pattern: Skeleton to Content Morph

```tsx
// streamUI pattern: yield loading, return content
async function* generateCreative() {
  yield <SkeletonCreative />;  // skeleton state
  const data = await createCreative();
  return <CreativeDisplay data={data} />;  // final content
}
```

```tsx
// Skeleton component
const SkeletonCreative = () => (
  <div className="space-y-3 animate-pulse">
    <div className="h-4 bg-gray-200 rounded w-3/4" />
    <div className="h-32 bg-gray-200 rounded" />
    <div className="h-4 bg-gray-200 rounded w-1/2" />
  </div>
);
```

## Pattern: Ambient Glow (Agent Active)

```tsx
// Framer Motion: pulsing glow on element being worked on
<motion.div
  animate={{
    boxShadow: isAgentActive
      ? ["0 0 0 rgba(59,130,246,0)", "0 0 20px rgba(59,130,246,0.3)", "0 0 0 rgba(59,130,246,0)"]
      : "0 0 0 rgba(59,130,246,0)"
  }}
  transition={{ duration: 1.8, repeat: Infinity }}
>
  <Content />
</motion.div>
```

## Pattern: Version Morph (Swipe Between)

```tsx
<AnimatePresence mode="wait" custom={direction}>
  <motion.div
    key={version.id}
    custom={direction}
    initial={(d) => ({ opacity: 0, x: d * 50 })}
    animate={{ opacity: 1, x: 0 }}
    exit={(d) => ({ opacity: 0, x: d * -50 })}
    transition={{ type: "spring", stiffness: 300, damping: 30 }}
  >
    <CreativeOutput data={version} />
  </motion.div>
</AnimatePresence>
```

## Pattern: Score Counter Animation

```tsx
// Number counts up/down when version changes
<motion.span
  key={currentScore}
  initial={{ opacity: 0, y: 10, filter: "blur(4px)" }}
  animate={{ opacity: 1, y: 0, filter: "blur(0px)" }}
  transition={{ duration: 0.3 }}
>
  {currentScore}%
</motion.span>
```

## Pattern: Content Stream (Typing Effect)

```tsx
// Batch tokens, animate whole blocks
const [displayed, setDisplayed] = useState("");

useEffect(() => {
  // Batch tokens into chunks of ~10
  const chunks = tokens.reduce((acc, _, i) => {
    if (i % 10 === 0) acc.push(tokens.slice(i, i + 10).join(""));
    return acc;
  }, [] as string[]);
  
  let i = 0;
  const interval = setInterval(() => {
    if (i >= chunks.length) { clearInterval(interval); return; }
    setDisplayed(prev => prev + chunks[i]);
    i++;
  }, 50);
  
  return () => clearInterval(interval);
}, [tokens]);
```

## Pattern: Trust Overlay Fade

```tsx
// Tap and hold to reveal trust info
<motion.div
  initial={{ opacity: 0, y: 20 }}
  animate={{ 
    opacity: isHolding ? 1 : 0,
    y: isHolding ? 0 : 20,
    pointerEvents: isHolding ? "auto" : "none"
  }}
  transition={{ duration: 0.2 }}
  className="absolute bottom-0 left-0 right-0 bg-black/90 text-white p-4 rounded-t-lg"
>
  <TrustSummary />
</motion.div>
```

## CSS-Only Patterns

```css
/* Shimmer loading effect */
@keyframes shimmer {
  0% { background-position: -200% 0; }
  100% { background-position: 200% 0; }
}
.shimmer {
  background: linear-gradient(90deg, #f0f0f0 25%, #e0e0e0 50%, #f0f0f0 75%);
  background-size: 200% 100%;
  animation: shimmer 1.5s infinite;
}

/* Breathing glow for agent activity */
@keyframes breathe {
  0%, 100% { opacity: 0.4; transform: scale(1); }
  50% { opacity: 1; transform: scale(1.05); }
}
.agent-dot { animation: breathe 2s ease-in-out infinite; }

/* Fade-in for content appearance */
@keyframes fade-in-up {
  from { opacity: 0; transform: translateY(8px); }
  to { opacity: 1; transform: translateY(0); }
}
.content-appear { animation: fade-in-up 0.3s ease-out; }
```
