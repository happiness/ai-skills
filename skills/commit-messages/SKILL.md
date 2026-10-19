---
name: commit-messages
description: Writes git commit messages in the company style - an English summary sentence ending with a period, a Tracker ticket reference such as [KIP#882], and an optional explanatory body. Use when creating a commit, amending or rewording a commit message, squashing commits, or reviewing commit messages, and whenever the user says "commit", "commit message" or "commit this".
---

# Commit messages

Written in English.

## Format

```
<Present-tense summary sentence ending with a period.> [<PREFIX>#<NUMBER>]

<Body: what changed and why, wrapped at ~72 characters.>
```

### Subject

- One capitalised sentence starting with a verb (`Adds`, `Fixes`, `Updates`,
  `Removes`, `Upgrades`); match `git log -10 --format=%s`.
- Name the thing changed, not the activity: `Adds membership certificate
  endpoint to gbf_merkurius.`, not `Worked on API stuff.`
- Max ~72 characters before the ticket reference; put detail in the body.

### Ticket reference

- `[GBF#882]`: the Tracker project's **prefix**, `#`, the ticket number, in square
  brackets, one space before the bracket. Tracker reads `GBF#882` anywhere in the
  message and shows the commit in the ticket's Code box (when the repository is
  linked to the project); the brackets only make it easy to spot.
- The number comes from the branch name (`882-merkurius-api-integration` -> `882`).
  The prefix is the project's in Tracker: look at earlier commits
  (`git log --grep='#' -5 --format=%s`), or `list_projects` (see `tracker-tickets`).
  If either is unknown, ask; never guess.
- Older commits use Codebase's `[touch:882]`. Don't write that any more: Tracker
  does not read it.
- Housekeeping commits without a ticket may omit the reference.

### Body

Only when the diff doesn't explain itself.

- Explain **why** and what the diff can't show: constraints, rejected
  alternatives, intentionally absent behaviour.
- Prose; lists only for enumerations (e.g. `drupal/core 11.4.5 => 11.4.7`).
- Mention operational consequences: new Composer dependencies, config to
  import, untracked files an environment needs, new commands.
- Don't restate the diff.
- Another ticket the commit touches can be named in the body as `GBF#871`;
  Tracker links the commit to it too.

## Workflow

1. `git status` and `git diff --staged`. If nothing is staged, ask what to
   include; don't stage everything.
2. Get the ticket number from `git branch --show-current` and the prefix as above.
3. One logical change per commit; if the diff mixes unrelated changes, suggest
   splitting.
4. Use a heredoc or several `-m` flags for multi-line messages.

## Do not

- Use Conventional Commits prefixes, emoji, `[touch:...]`, a bare `#882` or a
  Jira-style `GBF-882`.
- Use `--no-verify`, or amend/force-push pushed commits unless asked.
- Add attribution trailers yourself; the harness does when configured.

## Examples

```
Updates the MkDocs site name to "Glasbranschföreningen". [GBF#882]
```

```
Fixes the type of the membership organization number. [GBF#882]

The API returns membershipOrganizationNumber as an integer, as stated in
the specification, but it was mapped as a string. This made
`dr merkurius:memberships:organizations` fail with a TypeError against
the test API. The test data now uses the real type.
```

```
Security update drupal/paragraphs 1.20.0 => 1.23.0. [GBF#877]
```
