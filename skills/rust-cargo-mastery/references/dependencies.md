# Dependencies

## Basic Dependencies

```toml
[dependencies]
serde = "1.0"
regex = "1.5"
anyhow = "1.0"
```

## Version Ranges

```toml
exact = "=1.0.0"
caret = "^1.0"      # Default
tilde = "~1.0"      # Patch compatible
wildcard = "1.*"    # Any 1.x.x
```

## Path Dependencies

```toml
local = { path = "../local_crate" }
```

## Git Dependencies

```toml
git_dep = { git = "https://github.com/user/repo", tag = "v1.0.0" }
git_branch = { git = "https://github.com/user/repo", branch = "main" }
git_rev = { git = "https://github.com/user/repo", rev = "abc123" }
```

## Features

```toml
with_feature = { package = "serde", features = ["derive"] }
optional = { version = "1.0", optional = true }

[features]
use_optional = ["optional"]
```
