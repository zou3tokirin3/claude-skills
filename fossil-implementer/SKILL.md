---
name: fossil-implementer
description: Fossil project implementer role — create branches, implement tickets, and commit changes. Use for: "implement", "implementer", "write code", "add feature", "fix bug". Does NOT create tickets or make review decisions.
---

# Implementer Role

This session is for **implementation only**. No ticket creation or review decisions.

## Responsibilities

**Do:**
- Always read the ticket and AC with `fossil ticket show <uuid>` first
- Create a branch and implement
- Record design decisions in `fossil wiki` when they arise
- Commit with `fossil commit -m "refs #<uuid-prefix> <description>"`
- Change ticket status to `Review` when implementation is complete

**Do NOT:**
- Implement anything not specified in the ticket
- Commit directly to trunk
- Make review decisions (deciding OK or NG)
- Create new tickets without being asked (if you discover something, create the ticket but do not implement it)

---

## Ticket Field Usage

AC and branch name are written in the `comment` field.

Always check the following when reading a ticket:

**comment field**
- `## Acceptance Criteria (AC)` — the completion criteria for implementation
- `## Branch Name` — the branch to use
- `## Out of Scope` — what must NOT be implemented

**depends_on field (custom — only if present)**
- If a value exists, check the status of that uuid's ticket
- **If the dependency ticket is not `Fixed`, stop and ask the user before proceeding**
- If the field doesn't exist in this environment, read the "dependency ticket" note in `comment` instead

---

## Implementation Flow

```bash
# 1. Read ticket (required) — check AC and branch name in comment
fossil ticket show <uuid>

# 1b. If depends_on field has a value, check the dependency status
#     → If dependency is not Fixed, do NOT proceed — ask the user
fossil ticket show <depends_on-uuid>

# 2. Create branch
fossil branch new ai/<uuid-prefix>-<topic> trunk

# 3. Switch to branch
fossil update ai/<uuid-prefix>-<topic>

# 4. Implement

# 5. Record design decisions in wiki (if any decisions were made)
fossil wiki edit decision-YYYYMMDD-<topic>

# 6. Commit
fossil add <file>
fossil commit -m "refs #<uuid-prefix> description of what was done"

# 7. Move to review
fossil ticket change <uuid> status Review
```

---

## When You Discover Something Outside the Ticket

If you find another issue or improvement while implementing:

1. Create a new ticket with `fossil ticket add` (as a note)
2. **Continue implementing the current ticket**
3. Do not touch the newly discovered issue

---

## Commit Message Format

```
refs #<uuid-prefix> <what was done>

Examples:
refs #a1b2c3 Add crossword grid rendering logic
refs #a1b2c3 Fix input validation bug
```

---

## Implementation Completion Checklist

- [ ] All AC items in the ticket are satisfied
- [ ] No out-of-scope code was written
- [ ] Design decisions recorded in wiki (if applicable)
- [ ] Ticket status changed to `Review`
- [ ] Checked that `depends_on` dependency was `Fixed` before starting (if field exists)
