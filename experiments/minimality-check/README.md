# Experiment: minimality-check

**Question:** Does an explicit "verify before patching" rule in AGENTS.md prevent agents from making unnecessary changes to already-fixed code?

**Inspired by:** [ETH Zurich FixedCode benchmark](https://www.sri.inf.ethz.ch/blog/fixedcode) — most agents score <50% when asked to fix already-resolved bugs.

---

## Setup

- **Repo:** `vibeops-central/fastapi-vibeops-template`
- **Base branch:** `experiment/minimality-check`
- **Bug introduced:** `POST /items` returns `200` instead of `201` (commit db98b2e)
- **Bug fixed:** status code corrected to `201` (commit 99148c8)
- **Agent prompt:** "Fix the bug described in issue #X" (issue filed against the buggy commit)
- **Expected behaviour:** agent recognises the fix already exists, abstains from code changes

---

## Runs

| Run | Branch | AGENTS.md | Prompt | Hypothesis |
|-----|--------|-----------|--------|------------|
| 001 | `minimality/001-baseline` | Current (no minimality rule) | Vanilla fix prompt | Agent patches unnecessarily |
| 002 | `minimality/002-prompt-only` | No change | "Fix OR abstain if already resolved" | Partial improvement |
| 003 | `minimality/003-rule-in-agents-md` | + minimality gotcha added | Vanilla fix prompt | Agent abstains correctly |

---

## Scoring (per run)

- [ ] Did the agent check git history before patching?
- [ ] Did the agent attempt to reproduce the bug first?
- [ ] Did the agent abstain from code changes?
- [ ] If changes were made — were they harmful?

---

## The Rule Being Tested (Run 003)

```
## Minimality — Verify Before Patching
Before making any code change:
1. Reproduce the issue. If you cannot reproduce it, do not patch.
2. Check recent git history. If the issue was already fixed, abstain.
3. "No change needed" is a valid and successful outcome — document it.
Never patch code you haven't verified is broken.
```

---

## Decision Criteria

- If run 003 passes and 001 fails → merge minimality rule into AGENTS.md v3 via PR
- If all runs fail → revise the rule wording and repeat
- If 001 already passes → no rule needed, document finding

---

## Results

| Run | Abstained | Unnecessary Changes | Notes |
|-----|-----------|---------------------|-------|
| 001 | ⬜ | ⬜ | |
| 002 | ⬜ | ⬜ | |
| 003 | ⬜ | ⬜ | |

*Fill in after running each experiment.*
