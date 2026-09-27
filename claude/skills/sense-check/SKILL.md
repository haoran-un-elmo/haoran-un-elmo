---
name: sense-check
description: Use when the user invokes /sense-check, or asks to sanity-check, double-check, second-opinion, or verify the previous answer before relying on it. Applies right after you have made claims, given an answer, or produced code/analysis the user is about to trust.
---

# Sense Check

## Overview

Dispatch a **fresh subagent** to adversarially verify the exchange that just happened. Fresh context is the entire point: you are anchored to your own answer and will re-confirm it. A subagent that never saw your reasoning can refute it.

**Core principle: you do not sense-check your own work. You dispatch someone who will try to prove you wrong.**

## The One Rule That Matters

**Do NOT review the previous turn yourself.** Checking it yourself — grepping, re-reading, re-reasoning — feels productive and is exactly the failure. You already believe your answer; re-checking it launders that belief into false confidence. The value comes only from a fresh, unanchored reviewer.

If you catch yourself thinking any of these, STOP and dispatch the subagent:

| Rationalization | Reality |
|---|---|
| "I can just verify this myself, faster." | You're anchored. You'll confirm, not refute. That's worthless here. |
| "The claim is obviously right / trivial." | Then the subagent costs little and confirms it. If it's wrong, you just caught it. |
| "I'll dispatch but also pre-check to save it time." | Pre-checking re-anchors the result to your view. Dispatch clean. |
| "The subagent can't see the conversation, too much hassle to package." | Packaging the exchange IS the skill. Do it. |

## Procedure

1. **Identify the target.** The "last set of input and output" = the most recent substantive user request and the response you gave it (the turn about to be trusted). If the last turn was a trivial ack, use the last substantive exchange.

2. **Package it for a context-blind reader.** The subagent sees NOTHING of this conversation. Into its prompt, put verbatim:
   - The user's request
   - Your full response (claims, answer, code, reasoning)
   - Any file paths / commands / identifiers you referenced, so it can find them
   - The working directory if relevant

3. **Dispatch ONE fresh subagent** via the Agent tool (`subagent_type: general-purpose`), adversarially framed and investigate-capable (read-only). Use the template below.

4. **Relay, don't act.** Present the subagent's verdict + issues + suggested fixes to the user. Do **not** auto-apply fixes. The user accepts, rejects, or thinks further on each. If VERDICT is `pass`, say so plainly and stop.

## Subagent Prompt Template

```
You are an independent reviewer. You did NOT produce the work below and have no
stake in it. Your job is to REFUTE it, not confirm it. Assume it may be wrong
and default to skepticism.

--- THE EXCHANGE UNDER REVIEW ---
QUESTION:
<verbatim question>

ANSWER:
<verbatim response, including any code/claims>

CONTEXT (paths, commands, cwd the answer referenced):
<paths / identifiers / working dir>
--- END ---

Check three dimensions:
1. FACTUAL ACCURACY — are the claims true? VERIFY against reality: read the
   referenced files, grep, run read-only commands. Do not trust the answer's
   own assertions. Read-only only — do not edit anything.
2. INSTRUCTION ADHERENCE — did the answer actually address what the user asked,
   and honor stated constraints?
3. LOGICAL SOUNDNESS — does the reasoning hold? Any leaps, contradictions, or
   conclusions unsupported by the premises?

Return EXACTLY this structure:

VERDICT: pass | concerns | fail
ISSUES:
- [factual|adherence|logic] <specific problem> — EVIDENCE: <what you checked and found> — SUGGESTED FIX: <concrete correction>
(one line per issue; write "none" if no issues)
```

## Quick Reference

| Step | Action |
|---|---|
| Target | Last substantive user request + your response |
| Package | Verbatim exchange + referenced paths/cwd (subagent is context-blind) |
| Dispatch | 1× `general-purpose`, adversarial, read-only investigation |
| Scope | Factual accuracy · instruction adherence · logical soundness |
| Return | Relay verdict + issues + suggested fixes; user decides; never auto-fix |

## Common Mistakes

- **Reviewing it yourself first** — the anchoring you're trying to escape. Don't.
- **Thin package** — omitting file paths/cwd leaves the subagent unable to verify facts; it falls back to reasoning-only and misses fabrications.
- **Confirmation framing** — "verify this is correct" invites a yes. The prompt must say *refute*.
- **Auto-applying fixes** — the user chose accept/reject/think, not silent correction. Relay and wait.
- **Multiple subagents** — one is the design. Escalate to parallel skeptics only if the user explicitly asks for higher rigor.

## Red Flags — STOP and dispatch instead

- You started grepping / re-reading the previous turn yourself
- You're about to say "I double-checked and it's correct" without a subagent
- You're pre-verifying "to help the subagent"
- You skipped packaging because "it's obvious"

**All of these mean: stop, package the exchange, dispatch the fresh subagent.**
