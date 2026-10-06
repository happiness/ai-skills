---
name: commit-messages
description: Writes git commit messages in the company style - an English summary sentence ending with a period, a Codebase ticket reference such as [touch:882], and an optional explanatory body. Use when creating a commit, amending or rewording a commit message, squashing commits, or reviewing commit messages, and whenever the user says "commit", "commit message" or "commit this".
---

# Commit messages

Written in English.

## Format

```
<Present-tense summary sentence ending with a period.> [touch:<TICKET_ID>]

<Body: what changed and why, wrapped at ~72 characters.>
```

### Subject

- One capitalised sentence starting with a verb (`Adds`, `Fixes`, `Updates`,
  `Removes`, `Upgrades`); match `git log -10 --format=%s`.
- Name the thing changed, not the activity: `Adds membership certificate
  endpoint to gbf_merkurius.`, not `Worked on API stuff.`
- Max ~72 characters before the ticket reference; put detail in the body.

### Ticket reference

- Exactly `[touch:882]`: one space before the bracket, none after the colon.
  It links the commit to the Codebase ticket (see `codebasehq-tickets`).
- Take the id from the branch name (`882-merkurius-api-integration` -> `882`).
  If there's no number and the user gave none, ask. Housekeeping commits
  without a ticket may omit it.

### Body

Only when the diff doesn't explain itself.

- Explain **why** and what the diff can't show: constraints, rejected
  alternatives, intentionally absent behaviour.
- Prose; lists only for enumerations (e.g. `drupal/core 11.4.5 => 11.4.7`).
- Mention operational consequences: new Composer dependencies, config to
  import, untracked files an environment needs, new commands.
- Don't restate the diff.

## Workflow

1. `git status` and `git diff --staged`. If nothing is staged, ask what to
   include; don't stage everything.
2. Get the ticket id from `git branch --show-current`.
3. One logical change per commit; if the diff mixes unrelated changes, suggest
   splitting.
4. Use a heredoc or several `-m` flags for multi-line messages.

## Do not

- Use Conventional Commits prefixes, emoji, or a ticket id without
  `[touch:...]`.
- Use `--no-verify`, or amend/force-push pushed commits unless asked.
- Add attribution trailers yourself; the harness does when configured.

## Examples

```
Update MkDocs site name to "Glasbranschföreningen" in configuration. [touch:882]
```

```
Fixes the type of the membership organization number. [touch:882]

The API returns membershipOrganizationNumber as an integer, as stated in
the specification, but it was mapped as a string. This made
`dr merkurius:memberships:organizations` fail with a TypeError against
the test API. The test data now uses the real type.
```

```
Security update drupal/paragraphs 1.20.0 => 1.23.0. [touch:877]
```
