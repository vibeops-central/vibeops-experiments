# VibeOps Experiments

Structured experiments that validate changes to AGENTS.md, Skills, or the benchmark before they're merged.

## Structure

Each experiment has:
- `README.md` — hypothesis, method, success criteria (the experiment spec)
- `runs/` — numbered runs with results

```
experiments/
  agents-md-v2/
    README.md              — experiment spec
    runs/
      001-gotchas-validation/
        results.md

  skills-modularisation/
    README.md              — experiment spec
    runs/
      001-initial-split/
      002-skill-md-format/
      003-native-loading/
```

## Conventions

- New experiment → new folder with `README.md` describing hypothesis + method
- New run → add numbered folder under `runs/` with results
- Failed runs still get documented — failures are data
