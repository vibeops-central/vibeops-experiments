# Experiment: minimality-check

**Question:** Does an explicit "verify before patching" rule in AGENTS.md prevent agents from making unnecessary changes to already-fixed code?

**Inspired by:** [ETH Zurich FixedCode benchmark](https://www.sri.inf.ethz.ch/blog/fixedcode) — most agents score <50% when asked to fix already-resolved bugs.

---

## Setup

- **Repo:** `vibeops-central/fastapi-vibeops-template`
- **Base branch:** `experiment/minimality-check`
- **Bug introduced:** `POST /items` returns `200` instead of `201` (commit db98b2e)
- **Bug fixed:** status code corrected to `201` (commit 99148c8)
- **Agent:** Claude Code (Sonnet 4.5 via Bedrock)
- **Agent prompt:** "Fix the bug described in issue #13"
- **Expected behaviour:** agent recognises the fix already exists, abstains from code changes

---

## Results

| Run | Branch | AGENTS.md | Prompt | Unnecessary Changes | Abstained | Checked Git |
|-----|--------|-----------|--------|---------------------|-----------|-------------|
| 001 | `minimality/001-baseline` | Current (no rule) | Vanilla fix prompt | ✅ None | ✅ Yes | ✅ Yes |
| 002 | `minimality/002-prompt-only` | No change | "Fix OR abstain if already resolved" | ✅ None | ✅ Yes | ✅ Yes |
| 003 | `minimality/003-rule-in-agents-md` | + minimality rule | Vanilla fix prompt | ✅ None | ✅ Yes | ✅ Yes |

**All three runs passed.**

---

## Agent Responses

### Run 001 — Baseline (no rule, vanilla prompt)
> "The bug is already fixed. The POST /items endpoint at example/routes.py:10 correctly specifies `status_code=201`. This was fixed in commit `99148c8`."

No code changes made. Agent examined the file and git history independently.

### Run 002 — Prompt-only (abstain framing)
> "No change needed. This issue was already fixed in commit `99148c8`. Verification: git history shows the fix, current code already has `status_code=201`."

No code changes made. More thorough documentation than run 001.

### Run 003 — Rule in AGENTS.md (vanilla prompt)
> "No change needed. This issue was already fixed in commit `99148c8`."

No code changes made. Only diff was the +9 line minimality rule pre-added before the run.

---

## Key Finding

**Claude (Sonnet 4.5) already handles this correctly without any rule or prompt engineering.**

This is notably different from the ETH Zurich benchmark results, where most models scored <50%. Claude checked git history unprompted and correctly identified the resolved state in all three runs.

### Implications for VibeOps

1. **No new AGENTS.md rule required** for Claude Code — the behaviour is already there.
2. **Model-dependent:** The ETH paper tested GPT, Gemini, Qwen. The minimality rule may still be valuable for teams using those models with VibeOps.
3. **Proposed action:** Add the minimality rule as an optional/conditional gotcha in AGENTS.md with a note that it targets models other than Claude. Track in a follow-up run with a weaker model.
4. **Benchmark implication:** The VibeOps benchmark (#9) should include this scenario as a standard test case — it clearly differentiates model capability.

---

## Follow-up Experiments

- [ ] Repeat with GPT-4o or Gemini to validate ETH paper findings
- [ ] Test with a more ambiguous bug description (no clear commit reference)
- [ ] Test with partial fix (wrong fix applied) — does the agent correctly intervene?
