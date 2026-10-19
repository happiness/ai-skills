# Happiness AI Skills

Drupal module that bundles Happiness-specific AI skills so they can be reused
across Drupal projects. Each skill lives in its own directory under `skills/`
with a `SKILL.md`.

## Skills

| Skill | Description |
| --- | --- |
| `tracker-tickets` | Find, triage, create and update tickets in Tracker through its MCP tools: search, comments, files, internal notes where clients must not read along, references such as `KIP#123`, and telling a ticket what was done. |
| `tracker-time` | Report and check staff time in Kimai through Tracker: log a day's time from what was worked on (git and Tracker), check and correct entries, My day. |
| `tracker-planning` | Milestones and the backlog in Tracker: plan a delivery, see where it stands, complete it with release notes, and leave projects one no longer works in. |
| `commit-messages` | Write git commit messages in the company style: an English summary sentence, a Tracker ticket reference such as `[GBF#882]`, and an optional body. |
| `git-branch-naming` | Name git branches after the Tracker ticket number plus a short kebab-case description, such as `882-merkurius-api-integration`. |

Version 2 is for Tracker, Happiness's replacement for CodebaseHQ: the Tracker skills use its MCP server and commits
reference tickets as `[GBF#882]`, which Tracker links to the ticket. Version 1 (`^1.0`, branch `main`) is the CodebaseHQ
version with `[touch:882]`, for projects that still use Codebase.

The Tracker skills need Tracker's MCP server connected in the AI client, with a personal token from Tracker's Settings ›
AI access (described in Tracker's documentation, Guide › AI assistants).

## Installation

Install with Composer. Do not clone the repository into `web/modules/custom`.

1. Add the repository to the Drupal project:

   ```bash
   composer config repositories.happiness-ai-skills vcs git@github.com:happiness/ai-skills.git
   ```

2. Require the module:

   ```bash
   composer require happiness/ai-skills:^2.0
   ```

   The package type is `drupal-module`, so Composer installs it to
   `web/modules/contrib/ai_skills` (via `composer/installers`, included in the
   Drupal recommended project template).


## Updating

```bash
composer update happiness/ai-skills
```

## Releasing

Versions come from git tags (semantic versioning). Do not set `version` in
`composer.json`.

```bash
git tag -a 2.0.0 -m "2.0.0"
git push origin 2.0.0
```

Version 2 is developed on the branch `2.x`, version 1 on `main`; tag each from its own branch.
