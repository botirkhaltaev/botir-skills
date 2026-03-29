# Link Time Optimization

## LTO Types

```bash
-C lto=false     # No LTO
-C lto=thin     # Thin LTO (faster, less optimization)
-C lto=fat      # Fat LTO (slower, best optimization)
-C lto=true     # Same as thin
```

## When to Use

- Thin LTO: Good balance for most cases
- Fat LTO: Best optimization, slower compile
