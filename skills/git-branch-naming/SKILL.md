---
name: git-branch-naming
description: Names git branches in the company style - the Codebase ticket id followed by a short kebab-case description, such as 882-merkurius-api-integration. Use when creating, renaming or suggesting a git branch, starting work on a ticket, or when the user asks what a branch should be called.
---

# Git branch naming

Format: `<TICKET_ID>-<short-description>`, e.g. `882-merkurius-api-integration`.
The id comes first so branches sort by ticket and the commit reference
`[touch:<TICKET_ID>]` can be derived from it (see `commit-messages`).

## Rules

- `TICKET_ID`: Codebase ticket number, digits only.
- Description: two to five lowercase English words describing the goal, not the
  implementation (`885-drupal-core-update`, not `885-run-composer-update`).
- Only `a-z`, `0-9`, `-`. Transliterate `å`/`ä` -> `a`, `ö` -> `o`; drop
  punctuation.
- No `ticket-`, `feature/` or `fix/` prefix, no developer name (older
  `ticket-NNN` branches exist; don't copy them).
- Aim for 40 characters or fewer.
- One branch per ticket; follow-up work stays on it.
- Branch from `master`/`main` unless told otherwise.

## Finding the ticket id

1. Use the number the user gave.
2. If they gave a title, look it up with the Codebase tools (see
   `codebasehq-tickets`); the project permalink is `gbf`.
3. If there's no ticket, ask; never invent a number. Only if the user confirms
   there is none, use a plain description like `document-search`.

## Creating and renaming

```bash
git switch -c 882-merkurius-api-integration master
```

Run `git status` first; if there are uncommitted changes, tell the user rather
than silently carrying them over. Don't push or set upstream unless asked.

Rename with `git branch -m <new-name>`. If the old name was pushed, say so and
leave deleting the remote branch to the user.

## Examples

| Ticket | Branch |
|--------|--------|
| 885 "Upgrade Drupal core" | `885-drupal-core-update` |
| 881 "Add Webbinarium to event type" | `881-webbinarium-event-type` |
| 883 "Ändra ingress" | `883-update-lead-text` |
