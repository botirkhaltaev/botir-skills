# Animation Patterns for AI-Native UI

## Principles

- Motion has **purpose** — never decorative
- Duration: **200-400ms** micro, **400-800ms** layout changes
- Always respect `prefers-reduced-motion`
- Animate **whole blocks**, not per-token

## Skeleton → Content Morph

```tsx
// streamUI: yield loading, return content
async function* generate() {
  yield <Skeleton />;
  const data = await create();
  return <Content data={data} />;
}

const Skeleton = () => (
  <div className="space-y-3 animate-pulse">
    <div className="h-4 bg-gray-200 rounded w-3/4" />
    <div className="h-32 bg-gray-200 rounded" />
    <div className="h-4 bg-gray-200 rounded w-1/2" />
  </div>
);
```

## Ambient Glow

```tsx
<motion.div
  animate={{
    boxShadow: isActive
      ? ["0 0 0 rgba(0,0,0,0)", "0 0 20px rgba(59,130,246,0.3)", "0 0 0 rgba(0,0,0,0)"]
      : "0 0 0 rgba(0,0,0,0)"
  }}
  transition={{ duration: 1.8, repeat: Infinity }}
>
  <Content />
</motion.div>
```

## Version Morph (Swipe)

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
    <Output data={version} />
  </motion.div>
</AnimatePresence>
```

## Score Counter

```tsx
<motion.span
  key={score}
  initial={{ opacity: 0, y: 10, filter: "blur(4px)" }}
  animate={{ opacity: 1, y: 0, filter: "blur(0px)" }}
  transition={{ duration: 0.3 }}
>
  {score}%
</motion.span>
```

## Trust Overlay Fade

```tsx
<motion.div
  initial={{ opacity: 0, y: 20 }}
  animate={{ 
    opacity: isHolding ? 1 : 0,
    y: isHolding ? 0 : 20,
    pointerEvents: isHolding ? "auto" : "none"
  }}
  transition={{ duration: 0.2 }}
>
  <TrustSummary />
</motion.div>
```

## CSS Patterns

```css
/* Shimmer loading */
@keyframes shimmer {
  0% { background-position: -200% 0; }
  100% { background-position: 200% 0; }
}
.shimmer {
  background: linear-gradient(90deg, #f0f0f0 25%, #e0e0e0 50%, #f0f0f0 75%);
  background-size: 200% 100%;
  animation: shimmer 1.5s infinite;
}

/* Breathing glow */
@keyframes breathe {
  0%, 100% { opacity: 0.4; transform: scale(1); }
  50% { opacity: 1; transform: scale(1.05); }
}
.agent-dot { animation: breathe 2s ease-in-out infinite; }

/* Fade-in-up */
@keyframes fade-in-up {
  from { opacity: 0; transform: translateY(8px); }
  to { opacity: 1; transform: translateY(0); }
}
.content-appear { animation: fade-in-up 0.3s ease-out; }
```
