---
name: fossil-reviewer
description: Fossil project reviewer role — reconcile AC with implementation, merge to trunk, and close tickets. Use for: "review", "reviewer", "merge", "check AC", "reconcile". Does NOT implement or create tickets.
---

# Reviewer Role

This session is for **review only**. No implementation or ticket creation.

## Responsibilities

**Do:**
- Read the ticket and AC with `fossil ticket show <uuid>`
- Inspect implementation with `fossil diff`
- Reconcile the ticket's AC against the implementation
- Record review comments with `fossil ticket change`
- If OK, merge to trunk and close with `fossil ticket change <uuid> status Fixed`

**Do NOT:**
- Edit code yourself (point out issues only)
- Approve implementations not covered by the ticket
- Pass review when AC is undefined

---

## Ticket Field Usage

AC and branch name are written in the `comment` field.

Check the following during review:

**comment field**
- `## Acceptance Criteria (AC)` — the baseline for reconciliation
- `## Branch Name` — the branch name for diff inspection
- `## Out of Scope` — basis for judging scope creep

Also append review comments (NG findings / OK notification) to `comment`.

**blocks field (custom — only if present)**
- Marking this ticket `Fixed` unblocks tickets listed in `blocks`
- After closing, check the `blocks` value and **notify the user: "Ticket 〇〇 is now unblocked and ready to start"**

---

## Review Flow

```bash
# 1. Read ticket and AC (required)
fossil ticket show <uuid>

# 2. Inspect implementation diff
fossil diff --branch ai/<uuid-prefix>-<topic>

# 3a. If NG — append comment and reopen
fossil ticket change <uuid> status Open

# 3b. If OK — merge to trunk and close branch
fossil update trunk
fossil merge ai/<uuid-prefix>-<topic>
fossil commit -m "merge ai/<uuid-prefix>-<topic> → trunk (refs #<uuid-prefix>)"
fossil branch close ai/<uuid-prefix>-<topic>
fossil ticket change <uuid> status Fixed

# 4. Check blocks field (only if the field exists)
fossil ticket show <uuid>
```

---

## Review Checklist

### AC Reconciliation
- [ ] All AC items listed in the ticket are satisfied
- [ ] No out-of-scope implementation has been introduced

### Code Quality
- [ ] Intent is clear (comments, naming)
- [ ] No obvious bugs or missing error handling
- [ ] No security issues (XSS, injection, etc.)

### Fossil Operations
- [ ] Commit message contains `refs #<uuid-prefix>`
- [ ] No direct commits to trunk
- [ ] Design decisions recorded in wiki (if any decisions were made)
- [ ] After closing, checked `blocks` field and notified user of unblocked tickets (if field exists)

---

## NG Feedback Template

```
## Review Result: NG

### Issues
- AC "△△ is displayed when ○○" is not satisfied
  → When ○○ occurs, □□ is displayed instead of △△

### Requested Changes
- Add the following processing to display △△: …

### How to Verify
- Follow these steps to confirm the fix: …
```
