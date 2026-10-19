---
name: git-branch-naming
description: Names git branches in the company style - the Tracker ticket number followed by a short kebab-case description, such as 882-merkurius-api-integration. Use when creating, renaming or suggesting a git branch, starting work on a ticket, or when the user asks what a branch should be called.
---

# Git branch naming

Format: `<NUMBER>-<short-description>`, e.g. `882-merkurius-api-integration`.
The number comes first because Tracker reads it: every commit pushed to the
branch, and a merge request from it, is linked to that ticket in the project the
repository belongs to (a branch name cannot name another project). The commit
reference `[GBF#882]` is derived from it too (see `commit-messages`).

## Rules

- `NUMBER`: the Tracker ticket number, digits only, without the project prefix
  (`882-…`, not `GBF-882-…` or `GBF#882-…`, which Tracker does not read).
- Description: two to five lowercase English words describing the goal, not the
  implementation (`885-drupal-core-update`, not `885-run-composer-update`).
- Only `a-z`, `0-9`, `-`. Transliterate `å`/`ä` -> `a`, `ö` -> `o`; drop
  punctuation.
- No `ticket-` prefix and no developer name (older `ticket-NNN` branches exist;
  don't copy them). Tracker also accepts a type before the number
  (`fix/885-…`), but don't add one unless the project uses them.
- Aim for 40 characters or fewer.
- One branch per ticket; follow-up work stays on it.
- Branch from `master`/`main` unless told otherwise.

## Finding the ticket

1. Use the number the user gave (`GBF#882` or `882`).
2. If they gave a title, find it with the Tracker tools (`search_tickets`, see
   `tracker-tickets`) in the project this repository belongs to; take the
   project from `list_projects` or earlier commit messages, don't guess.
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
| GBF#885 "Upgrade Drupal core" | `885-drupal-core-update` |
| GBF#881 "Add Webbinarium to event type" | `881-webbinarium-event-type` |
| GBF#883 "Ändra ingress" | `883-update-lead-text` |
