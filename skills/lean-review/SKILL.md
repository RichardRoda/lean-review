---
name: lean-review
description: Use after superpowers:brainstorming writes a spec or superpowers:writing-plans writes a plan. Runs a panel of 8 specialist agents in parallel to review for over-engineering, security gaps, SOLID violations, maintainability issues, and missed data-driven design opportunities, then works through findings iteratively with the user.
---

# Lean Review Panel

**Announce at start:** "Running lean-review panel on `[document_path]`..."

You will be given two pieces of context when invoked:
- **document_path** — absolute path to the document to review
- **document_type** — `spec` or `plan`

## Step 1: Read the Document

Use the Read tool to read the full content of `document_path`. You will inline this text into each specialist agent's prompt — agents do not read files themselves.

## Step 2: Read All Specialist Prompt Files

The base directory for this skill is provided at the top of this skill's loaded content as "Base directory for this skill: <path>". Use the Read tool to read each of these files from the `prompts/` subdirectory of that base directory:
- `scope-minimizer.md`
- `complexity-challenger.md`
- `feasibility-auditor.md`
- `data-driven-advocate.md`
- `maintainability-reviewer.md`
- `devils-advocate.md`
- `security-expert.md`
- `solid-reviewer.md`
- `synthesizer.md`

## Step 3: Dispatch Specialists in Parallel

The Devil's Advocate is **expensive and design-level** — run it selectively:

- **First run** (no prior panel iteration this session): dispatch all 8 specialists including the Devil's Advocate.
- **Re-run after a non-Devil's-Advocate finding was accepted**: dispatch the 7 specialists **excluding** the Devil's Advocate (agents 1–5, 7–8 from the table below).
- **Re-run after a Devil's Advocate finding was accepted**: dispatch all 8 specialists including the Devil's Advocate.

Using the Agent tool, dispatch the applicable specialists in a **single message** (parallel). For each agent:
- `subagent_type`: `"general-purpose"`
- `model`: see table below
- `prompt`: the specialist prompt file content, followed by `\n\n## Document to Review\n\n` and the full document text

| # | Agent | Prompt File | Model |
|---|-------|-------------|-------|
| 1 | Scope Minimizer | `scope-minimizer.md` | `sonnet` |
| 2 | Complexity Challenger | `complexity-challenger.md` | `sonnet` |
| 3 | Feasibility Auditor | `feasibility-auditor.md` | `sonnet` |
| 4 | Data-Driven Advocate | `data-driven-advocate.md` | `sonnet` |
| 5 | Maintainability Reviewer | `maintainability-reviewer.md` | `sonnet` |
| 6 | Devil's Advocate *(conditional — see above)* | `devils-advocate.md` | `opus` |
| 7 | Security Expert | `security-expert.md` | `opus` |
| 8 | SOLID Reviewer | `solid-reviewer.md` | `sonnet` |

## Step 4: Dispatch the Synthesizer

Once all dispatched specialist agents have returned results, dispatch a single Opus synthesizer agent:
- `subagent_type`: `"general-purpose"`
- `model`: `"opus"`
- `prompt`: the `synthesizer.md` content, followed by all specialist reports concatenated under `## Specialist Reports`

## Step 5: Drive the Iterative Review Loop

Work through the synthesizer's action list with the user one item at a time:

1. Present the highest-priority item from the synthesizer output
2. Discuss with the user — they choose: **accept**, **reject**, or **modify**
3. **Accept**: Apply the change to `document_path` using the Edit tool, note whether the accepted item originated from the **Devil's Advocate**, then restart from Step 1. Pass that flag into Step 3 to control whether the Devil's Advocate is re-dispatched on the next run.
4. **Reject**: Record the item and the user's reason for rejection. Continue to the next item from the *existing* synthesizer output — no re-run needed since the document is unchanged
5. **Devil's Advocate "Recommend reconsidering design"**: Surface this verdict as the first item before all others. If the user decides to proceed with the original design, record as rejected and continue processing remaining items

The loop ends when the synthesizer produces no new items (all items resolved or panel returns clean).

## Step 6: Finalize the Document

After the loop completes, append the following section to `document_path` using the Edit tool:

If items were rejected:
```markdown
## Review Decisions — Rejected

- **[Item summary]**: [User's reason for rejection]
- **[Item summary]**: [User's reason for rejection]
```

If nothing was rejected:
```markdown
## Review Decisions — Rejected

*(No items rejected — all panel findings accepted or no issues found.)*
```
