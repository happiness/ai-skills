# Happiness AI Skills

Drupal module that bundles Happiness-specific AI skills so they can be reused
across Drupal projects. Each skill lives in its own directory under `skills/`
with a `SKILL.md`.

## Skills

| Skill | Description |
| --- | --- |
| `codebasehq-tickets` | Create and update CodebaseHQ tickets through the Codebase MCP tools. |
| `commit-messages` | Write git commit messages in the company style: an English summary sentence, a Codebase ticket reference such as `[touch:882]`, and an optional body. |
| `git-branch-naming` | Name git branches after the Codebase ticket id plus a short kebab-case description, such as `882-merkurius-api-integration`. |

## Installation

Install with Composer. Do not clone the repository into `web/modules/custom`.

1. Add the repository to the Drupal project:

   ```bash
   composer config repositories.happiness-ai-skills vcs git@github.com:happiness/ai-skills.git
   ```

2. Require the module:

   ```bash
   composer require happiness/ai-skills:^1.0
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
git tag -a 1.1.0 -m "1.1.0"
git push origin 1.1.0
```
