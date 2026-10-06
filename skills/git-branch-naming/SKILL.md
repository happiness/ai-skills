---
name: git-branch-naming
description: Names git branches in the company style - the Codebase ticket id followed by a short kebab-case description, such as 882-merkurius-api-integration. Use when creating, renaming or suggesting a git branch, starting work on a ticket, or when the user asks what a branch should be called.
---

# Git branch naming

## Format

```
<TICKET_ID>-<short-description>
```

- `TICKET_ID` is the Codebase ticket number, digits only.
- `short-description` is lowercase English, words separated by single hyphens.
- Example: `882-merkurius-api-integration`.

The ticket id comes first so branches sort by ticket and so the commit
reference `[touch:<TICKET_ID>]` can be derived from the branch name (see the
`commit-messages` skill).

## Rules

- Use two to five words that describe the ticket's goal, not the
  implementation: `885-drupal-core-update`, not `885-run-composer-update`.
- Only `a-z`, `0-9` and `-`. Transliterate Swedish letters (`å`, `ä` -> `a`,
  `ö` -> `o`) and drop punctuation. No spaces, underscores, slashes or
  uppercase.
- No `ticket-` prefix, no `feature/` or `fix/` prefix, and no developer name.
  Older `ticket-NNN` branches exist in the history; do not copy that style.
- Aim for 40 characters or fewer in total.
- One branch per ticket. Related follow-up work on the same ticket stays on the
  same branch.
- Branch from `master`/`main` unless the user names another base.

## Finding the ticket id

1. Use the number if the user gave one.
2. Otherwise, if the user describes a ticket by title, look it up with the
   Codebase tools (see the `codebasehq-tickets` skill). The project permalink
   is `gbf`.
3. If there is no ticket, ask. Do not invent a number. Only if the user
   confirms the work has no ticket, use a plain description such as
   `document-search`, as `master`, `dev` and `redesign` already do.

## Creating the branch

```bash
git switch -c 882-merkurius-api-integration master
```

Run `git status` first. If there are uncommitted changes, tell the user
instead of carrying them onto the new branch silently. Do not push or set
upstream unless asked.

## Renaming

Rename a local branch with `git branch -m <new-name>`. If the old name was
already pushed, say so, and leave deleting the remote branch to the user.

## Examples

| Ticket | Branch |
|--------|--------|
| 885 "Upgrade Drupal core" | `885-drupal-core-update` |
| 881 "Add Webbinarium to event type" | `881-webbinarium-event-type` |
| 883 "Ändra ingress" | `883-update-lead-text` |
