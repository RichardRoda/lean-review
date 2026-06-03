---
name: lean-review
description: Use after superpowers:brainstorming writes a spec or superpowers:writing-plans writes a plan. Runs Devil's Advocate first as a design gate, then dispatches 9 specialist agents in parallel to review for over-engineering, security gaps, SOLID violations, maintainability issues, missed data-driven design opportunities, auditability gaps, and observability gaps, then works through findings iteratively with the user. A final consistency pass checks the edited document for internal contradictions.
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
- `auditability-advocate.md`
- `observability-advocate.md`
- `consistency-reviewer.md`
- `synthesizer.md`

## Step 3: Phase 1 — Devil's Advocate Gate

The Devil's Advocate is **expensive and design-level** — run it alone first so a fundamental design objection is surfaced before the other specialists spend tokens reviewing a design that may change.

Check whether `document_path` contains this exact line:

> **Devil's Advocate:** Ran — no document changes resulted.

**If that line is present**: skip Phase 1 and go directly to Step 4.

**If that line is absent**: dispatch the Devil's Advocate alone:
- `subagent_type`: `"general-purpose"`
- `model`: `"opus"`
- `prompt`: the `devils-advocate.md` content, followed by `\n\n## Document to Review\n\n` and the full document text

Parse the DA's JSON response `verdict` field:

- **`"Recommend reconsidering design"`**: Surface the `alternative_approach` to the user. Ask whether they want to explore the alternative or continue with the original design.
  - *User chooses to explore the alternative*: the user will revise the document. After revisions, restart from Step 1.
  - *User continues with original design*: write the Devil's Advocate marker (see below) and proceed to Step 4.
- **`"Existing design holds"`**: write the Devil's Advocate marker and proceed to Step 4.

### Writing the Devil's Advocate Marker

Write the following line to `document_path` using the Edit tool:

`**Devil's Advocate:** Ran — no document changes resulted.`

If the document already contains a `## Review Decisions` section, append this line to that section. Otherwise append a new section at the end of the document:

```markdown
## Review Decisions

**Devil's Advocate:** Ran — no document changes resulted.
```

This marker is what Step 3 checks on every run — including future sessions — to decide whether to skip the DA gate.

## Step 4: Phase 2 — Dispatch Remaining Specialists in Parallel

Using the Agent tool, dispatch the 9 non-DA specialists in a **single message** (parallel). For each agent:
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
| 6 | Security Expert | `security-expert.md` | `opus` |
| 7 | SOLID Reviewer | `solid-reviewer.md` | `sonnet` |
| 8 | Auditability Advocate | `auditability-advocate.md` | `sonnet` |
| 9 | Observability Advocate | `observability-advocate.md` | `sonnet` |

## Step 5: Dispatch the Synthesizer

Once all 9 specialists have returned results, dispatch a single Opus synthesizer agent:
- `subagent_type`: `"general-purpose"`
- `model`: `"opus"`
- `prompt`: the `synthesizer.md` content, followed by all 9 specialist reports concatenated under `## Specialist Reports`

## Step 6: Drive the Iterative Review Loop

The synthesizer returns a JSON object. Work through it with the user one item at a time. **Always render fields as natural prose — never show raw JSON to the user.**

1. Present the first unresolved item from the `issues` array (lowest `id` not yet accepted or rejected).
2. Format each item for the user as: the `summary` prose, then "Source: `source`", then "Why this matters: `priority_rationale`". Use plain markdown — no JSON syntax.
3. Discuss with the user — they choose: **accept**, **reject**, or **modify**.
4. **Accept**: Apply the change to `document_path` using the Edit tool, then restart from Step 1.
5. **Reject**: Record the item's `id` and the user's reason. Continue to the next unresolved `issues` item from the *existing* synthesizer output — no re-run needed since the document is unchanged.

The loop ends when `issues` is empty or all items are resolved.

## Step 7: Consistency Pass

After the iterative loop ends (all synthesizer items resolved), re-read `document_path` to get the current state of the document, then dispatch a single consistency reviewer agent:
- `subagent_type`: `"general-purpose"`
- `model`: `"sonnet"`
- `prompt`: the `consistency-reviewer.md` content, followed by `\n\n## Document to Review\n\n` and the full current document text

If the consistency reviewer's JSON has `"status": "Issues Found"`, present each item from its `issues` array to the user one at a time (accept / reject / modify), applying accepted fixes with the Edit tool before continuing to the next item. Render each item as prose: `conflict` followed by "Suggested resolution: `resolution`" — no raw JSON. If `"status": "Approved"`, skip directly to Step 8.

The consistency pass does **not** trigger a full panel re-run — it is a targeted final check only.

## Step 8: Finalize the Document

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
