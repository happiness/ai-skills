---
name: tracker-tickets
description: Finds, creates and updates tickets in Tracker, Happiness's ticket system, through its MCP tools (search_tickets, get_ticket, get_ticket_notes, create_ticket, update_ticket, add_note, add_attachment, get_attachment). Use when asked to open, file, look up, triage, update, comment on, close or reassign a ticket, attach or read a file on one, summarise where a project's tickets stand, or write ticket text with references such as KIP#123.
---

# Tracker tickets

Tickets live in Tracker (Happiness's replacement for CodebaseHQ), reached through its MCP server, usually connected as
`tracker` (tools `mcp__tracker__*`). If the tools are missing, the user connects them with a personal token from
Tracker's Settings › AI access:
`claude mcp add --transport http tracker https://<tracker host>/mcp --header "Authorization: Bearer trk_…"`.
Load deferred tool schemas with ToolSearch before calling.

The tools act as the token's owner with exactly their permissions. **Clients** see no internal tickets or notes, and
may comment and change status and assignee where their membership allows writing. **Staff** change every field. A
read-only token cannot write. Every change is in the ticket's history, marked "via MCP". Milestones and the backlog are
in `tracker-planning`; time in `tracker-time`.

## Finding things

- `list_projects`: every project's **prefix** (2 to 4 characters, `KIP`) and key. Pass either as `project`; never
  guess. A ticket is `project` + `number`, written `KIP#123`.
- `search_tickets`: the search-box language. Words search subjects and comments; filters: `status:open`
  (`closed`, `waiting` = waiting for the client, or a name), `assignee:me|none|<name>`, `reporter:`, `watcher:me`,
  `type:`, `priority:high,critical` (comma = any of), `category:`, `milestone:"Rel 2"|none`, `tag:`,
  `workpackage:`, `project:KIP`, `blocked:yes`, `backlog:yes`, `private:yes` (staff), `created:<30d`,
  `updated:>2026-09-01`, `not-status:closed` (or `-status:closed`), `sort:updated_at order:desc`. Up to 50 per page.
- `get_ticket`: fields, description, relationships and sub-tasks. `get_ticket_notes`: comments and changes, oldest
  first (paged), with attachment ids and commits that mention the ticket.
- `get_lookups`: the project's statuses (and which close a ticket), types, priorities, categories, tags, active
  milestones and work packages. `get_project_users`: who can be mentioned or assigned.

## Writing

1. **Look first.** Before an update, read the ticket (and its notes if history matters). Before creating, search for
   a duplicate.
2. **Create:** `create_ticket` with `summary` (one line) and `description`. Everyone may set type, priority,
   category, milestone, tags, start and deadline; staff also status, assignee, estimate, `internal` and `subtask_of`
   (a sub-task's parent number; one level only). With more than one work package, `work_package` is required.
3. **Update:** `update_ticket` with only the fields that change, and `comment` for a comment in the same call. An
   empty string clears a field (never status). Status changes follow the project's workflow; assignees are Happiness
   staff (email, full name or `me`). Names are matched without regard to case.
4. **Comment:** `add_note` with `body`. **Files:** `add_attachment` (base64, up to 50 MB, as a new comment);
   `get_attachment` reads one by its id.
5. **Report back** with the reference and what changed.

A **closed** ticket takes no comments or files; changing its status to an open one re-opens it. Relationships
(blocks, duplicates, relates to …) cannot be set through MCP: say which ones to add in the web page.

Tickets and comments are read by the team and often the client and cannot be cleanly withdrawn. Show the text and get
a go-ahead before any write, unless the user asked for exactly that text.

## Internal or public

Clients who are members read every public ticket and comment. Other clients, prices, estimates, staff matters and
anything the client should not see go in an **internal** comment (`internal: true` in `add_note` and
`add_attachment`, `internal_comment: true` in `update_ticket`) or an internal ticket (`internal: true`); only staff can
set it. When unsure, ask. Write in the language the ticket is already written in.

## Tracker's Markdown

GitHub-flavoured: headings, lists, tables, task lists (`- [ ]`), fenced code with highlighting, strikethrough; one
line break stays a line break; raw HTML shows as text.

| Purpose | Write |
| --- | --- |
| Another ticket | `KIP#123`, with the prefix, also in the same project; never a bare `#123`, `KIP-123` or a URL |
| A person | `@handle` (the part before the @ of their address); they are notified and start following |
| Branch, commit, merge request | Backticks (`` `882-new-export` ``, `` `3e459c1` ``) or a link; commits that mention a ticket appear on it by themselves (staff only) |
| An image | Attach it; images linked from other servers are not shown |
| A callout | `> **Note:** …` |

Codebase markup (`{branch:…}`, `{commit:…}`, `{tag:…}`, `Note:` blocks) does not exist in Tracker.

## Workflows

**Triage new tickets** (staff). `search_tickets` with `status:open assignee:none sort:created_at order:asc` (add
`project:`). For each: read it, check for a duplicate, and propose type, priority, category, assignee and milestone
or backlog, plus a question to the reporter if something is missing. Present all proposals as one table; apply only
what the user approves, one `update_ticket` per ticket.

**Where a project stands.** Open tickets by status and assignee (`project:KIP status:open`), those waiting for the
client (`status:waiting`), blocked ones (`blocked:yes`), the oldest unanswered, and changes in the last week
(`updated:<7d`). A short summary with references; nothing is written.

**Tell the ticket what was done.** After work on a branch (`882-…`): read the ticket, summarise the change from
`git log` and the diff in plain words for the people on the ticket (what changed for them, how to test, anything left),
and propose a comment, and a status change if the workflow has one for it (such as Testing). Commits already show on
the ticket for staff; the comment is for everyone else. Internal details go in an internal comment.

## Related

- Milestones and the backlog: `tracker-planning`; time: `tracker-time`; branch names: `git-branch-naming`; commit messages: `commit-messages`.
