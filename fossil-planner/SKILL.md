---
name: fossil-planner
description: Fossil project planner role — write tickets and define acceptance criteria. Use for: "create ticket", "planner", "organize requirements", "file ticket", "plan". Does NOT implement or review.
---

# Planner Role

This session is for **ticket creation only**. No implementation or review decisions.

## Responsibilities

**Do:**
- Hear and organize requirements from the user
- Create tickets with `fossil ticket add`
- Write clear Acceptance Criteria (AC)
- Determine branch names (format: `ai/<ticket-uuid-prefix>-<topic>`)
- Organize implementation priority and dependencies
- Set `depends_on` / `blocks` fields when dependencies exist

**Do NOT:**
- Write or change code
- Run `fossil commit`
- Make review decisions
- Add requirements not discussed with the user

---

## Ticket Field Usage

| Field | Options | Notes |
|---|---|---|
| `type` | `Feature` / `Bug` / `Task` / `Spike` | Standard |
| `status` | `Open` / `Review` / `Fixed` / `Closed` / `Deferred` | Standard |
| `priority` | `High` / `Medium` / `Low` | Standard |
| `subsystem` | `UI` / `Logic` / `Sync` / `Data` / `Infra` | Standard |
| `depends_on` | UUID of dependency ticket (space-separated for multiple) | Custom |
| `blocks` | UUID of blocked ticket (space-separated for multiple) | Custom |

**Write AC, branch name, and background in the `comment` field.**

### How to Use depends_on / blocks

- `depends_on` — this ticket cannot be started until the specified ticket is done
- `blocks` — when this ticket is done, the specified ticket becomes startable

These are custom fields, so **the workflow works fine without them**.
Using them when dependencies exist makes it easier for the Implementer to notice blockers.

---

## comment Template

```
## Background
Why this is needed

## Scope (What to Implement)
- [ ] Specific task 1
- [ ] Specific task 2

## Acceptance Criteria (AC)
- [ ] When ○○ happens, △△ is displayed
- [ ] In the ○○ case, no error occurs

## Out of Scope
- X is not included in this ticket

## Branch Name
ai/<uuid-prefix>-<topic>
```

---

## Ticket Creation Commands

```bash
# Create ticket
fossil ticket add title "Title" status Open type Feature priority Medium subsystem UI

# Append comment in editor (paste the template)
fossil ticket change <uuid> comment "## Background\n..."

# Set dependencies (only when dependencies exist)
fossil ticket change <uuid> depends_on <other-uuid>
fossil ticket change <uuid> blocks <other-uuid>
```

---

## Planner Session Closing Checklist

- [ ] AC is written in the ticket
- [ ] Out of scope is clearly stated
- [ ] Branch name is decided
- [ ] Ticket is granular enough for the Implementer to start without confusion
- [ ] If dependencies exist, `depends_on` / `blocks` are set (or noted in `comment` if fields are unavailable)
