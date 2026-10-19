---
name: tracker-tickets
description: Creates, finds and updates tickets in Tracker, Happiness's ticket system, through its MCP tools (search_tickets, get_ticket, create_ticket, update_ticket, add_note). Use when asked to open, file, look up, update, comment on, close or reassign a ticket, or to write ticket descriptions and comments, including references such as KIP#123.
---

# Tracker tickets

Tickets live in Tracker (Happiness's replacement for CodebaseHQ) and are managed through its MCP server, usually
connected as `tracker` (tools `mcp__tracker__*`). If the tools are missing, the user connects them with a personal
token from Tracker's Settings › AI access:
`claude mcp add --transport http tracker https://<tracker host>/mcp --header "Authorization: Bearer trk_…"`.
Load deferred tool schemas with ToolSearch before calling.

The tools act as the person whose token it is, with exactly their permissions: clients see no internal tickets or
notes and can only comment and change status and assignee; staff can change every field. A read-only token cannot
write at all. Every change is recorded in the ticket's history, marked "via MCP".

## Workflow

1. **Find the project.** `list_projects` gives each project's **prefix** (2 to 4 characters, such as `KIP`) and key.
   Pass either as `project`; never guess it. A ticket is then `project` + `number`.
2. **Look before writing.** For an update, `get_ticket` (and `get_ticket_notes` if history matters). For a new
   ticket, look for duplicates with `search_tickets` (the search-box language: words plus `status:open`,
   `assignee:me`, `type:bug`, `milestone:"Rel 2"`, `not-status:closed`, `sort:updated_at`).
3. **Use valid names.** Status, type, priority, category, milestone, tags and work package take *names*, matched
   without regard to case; `get_lookups` lists what the project allows. A status change must follow the project's
   workflow. `assignee` is an email address, a full name or `me` (`get_project_users`); only Happiness staff can be
   assigned. With more than one work package, `create_ticket` needs `work_package`.
4. **Create:** `create_ticket` with `summary` (one line) and `description` (Markdown, see below). Staff may also set
   status, assignee, estimate, `subtask_of` and `internal`.
5. **Update:** `update_ticket` with `project`, `number` and only the fields that change; `comment` adds a comment in
   the same call. An empty string clears a field (not status). A comment alone: `add_note` with `body`.
6. **Report back** with the reference (`KIP#123`) and what changed.

Tickets and comments are seen by the team and often by the client, and cannot be cleanly taken back. Show the user the
text and get a go-ahead before `create_ticket`, `update_ticket` or `add_note`, unless they asked for exactly that text.

## Internal or public

Clients who are members of the project read every public comment and ticket. Anything about other clients, prices,
estimates, staff matters or work the client should not see goes in an **internal** comment (`internal: true` in
`add_note`, `internal_comment: true` in `update_ticket`) or an internal ticket (`internal: true`), which only staff can
set. When unsure, ask. Write in the language the ticket is already written in.

## Writing in Tracker

GitHub-flavoured Markdown: headings, lists, tables, task lists (`- [ ]`), fenced code with highlighting,
strikethrough; a single line break stays a line break, and raw HTML is shown as text.

| Purpose | Write |
| --- | --- |
| Another ticket | `KIP#123`, with the project's prefix, also in the same project; never `#123` alone, `kip-123` or a URL |
| A person | `@handle` (the name before the @ of their address); they are notified and start following the ticket |
| A branch, commit or merge request | Plain text in backticks (`` `882-new-export` ``, `` `3e459c1` ``) or a link; Tracker shows commits and merge requests that mention a ticket in the ticket's Code box (staff only) |
| An image | Attach it (`add_attachment`); images linked from other servers are not shown |

Codebase markup such as `{branch:repo/name}`, `{commit:repo/sha}`, `{tag:…}` and `Note:` blocks does not exist in
Tracker; use the above, or a blockquote (`> **Note:** …`) for a callout.

Example description:

```markdown
## Problem

Old /student/ aliases still resolve after the migration.

> **Note:** Blocked until the client confirms the pathauto patterns.

## Work

Done on the branch `3901-purge-old-aliases`. Related to KIP#3864.
```

## Related conventions

- Branches: `<number>-<short-description>`, e.g. `3901-purge-old-aliases` (see `git-branch-naming`).
- Commit subjects end with `[KIP#3901]` (see `commit-messages`).
