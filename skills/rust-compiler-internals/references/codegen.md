# Codegen Options

## Optimization Levels

```bash
-C opt-level=0  # No optimization
-C opt-level=1  # Some optimization
-C opt-level=2  # Standard optimization
-C opt-level=3  # Maximum optimization
```

## Codegen Units

```bash
# Lower = more optimization, slower compile
-C codegen-units=1
-C codegen-units=16
```

## Debuginfo

```bash
-g                    # Full debug info
-C debuginfo=0        # No debug info
-C debuginfo=2        # Full debug info
```
