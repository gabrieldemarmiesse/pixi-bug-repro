# Pixi Bug: Absolute Paths in pixi.lock

This repository demonstrates a bug where running `pixi run echo hello` causes the pixi.lock file to be updated with absolute paths for local dependencies, breaking reproducibility.

## Bug Description

When using `tool.uv.sources` in `pyproject.toml` to specify a local path dependency, pixi converts the relative path to an absolute path in the lock file.

## Reproduction Steps

1. Clone this repository
2. Run `pixi run hello` (or any pixi command)
3. Check the `pixi.lock` file - it will contain an absolute path like:
   ```
   - my-subdir @ file:///tmp/repro/my_subdir
   ```

## Expected Behavior

The lock file should preserve the relative path to ensure reproducibility across different systems:
```
- my-subdir @ file://./my_subdir
```

## Current Behavior

The relative path `"my_subdir"` specified in `pyproject.toml`:
```toml
[tool.uv.sources]
my-subdir = { path = "my_subdir" }
```

Gets converted to an absolute path in `pixi.lock`:
```
- my-subdir @ file:///tmp/repro/my_subdir
```

This makes the lock file non-portable and breaks reproducibility when the repository is cloned to a different location.