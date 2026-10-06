---
name: commit-messages
description: Writes git commit messages in the company style - an English summary sentence ending with a period, a Codebase ticket reference such as [touch:882], and an optional explanatory body. Use when creating a commit, amending or rewording a commit message, squashing commits, or reviewing commit messages, and whenever the user says "commit", "commit message" or "commit this".
---

# Commit messages

Commit messages are written in English, like all documentation in this repo.

## Format

```
<Summary sentence in the present tense, ending with a period.> [touch:<TICKET_ID>]
<blank line>
<Body: what changed and why, wrapped at about 72 characters.>
```

### Subject line

- One sentence, capitalised, ending with a period, then a space and the ticket
  reference.
- Present tense, starting with a verb: `Adds`, `Fixes`, `Updates`, `Removes`,
  `Upgrades`. Match the recent history of the repository; `git log -10
  --format=%s` shows it.
- Name the thing that changed, not the activity: `Adds membership certificate
  endpoint to gbf_merkurius.`, not `Worked on API stuff.`
- Aim for 72 characters or fewer before the ticket reference. Move detail to
  the body instead of stretching the subject.

### Ticket reference

- Append `[touch:<TICKET_ID>]` to the subject. It links the commit to the
  ticket in Codebase (see the `codebasehq-tickets` skill).
- Write it exactly as `[touch:882]`: no space after the colon, one space before
  the bracket.
- Find the ticket id in the branch name (`882-merkurius-api-integration` means
  `882`). If the branch has no number and the user has not given one, ask
  instead of guessing. Housekeeping commits with no ticket may omit the
  reference.

### Body

Add a body when the diff does not explain itself. Skip it for trivial changes.

- Explain **why**, and the decisions a reader of the diff cannot see:
  constraints, rejected alternatives, behaviour that is intentionally absent.
- Write prose paragraphs. Use a list only for enumerations, such as the
  package versions of a dependency update (`drupal/core 11.4.5 => 11.4.7`).
- Mention operational consequences: new Composer dependencies, config that
  must be imported, files outside version control that an environment needs,
  commands added.
- Do not repeat what `git diff` shows line by line.

## Workflow

1. Run `git status` and `git diff --staged`. If nothing is staged, ask what to
   include rather than staging everything.
2. Take the ticket id from `git branch --show-current`.
3. Write the message. One logical change per commit; if the staged diff mixes
   unrelated changes, say so and suggest splitting it.
4. Pass multi-line messages with a heredoc or several `-m` flags so newlines
   survive.

## Do not

- Do not use Conventional Commits prefixes (`feat:`, `fix:`), emoji, or a
  trailing ticket id without the `[touch:...]` brackets.
- Do not use `--no-verify`, and do not amend or force-push commits that are
  already pushed unless the user asks.
- Do not add attribution trailers yourself. The harness adds them when it is
  configured to.

## Examples

Small change, no body needed:

```
Update MkDocs site name to "Glasbranschföreningen" in configuration. [touch:882]
```

Change that needs the reason explained:

```
Fixes the type of the membership organization number. [touch:882]

The API returns membershipOrganizationNumber as an integer, as stated in
the specification, but it was mapped as a string. This made
`dr merkurius:memberships:organizations` fail with a TypeError against
the test API. The test data now uses the real type.
```

Dependency update with a list:

```
Security update drupal/paragraphs 1.20.0 => 1.23.0. [touch:877]
```
