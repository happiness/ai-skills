---
name: tracker-planning
description: Plans work in Tracker through its MCP tools - milestones (get_milestones, create_milestone, update_milestone), the backlog and its priority, completing a milestone and drafting its release notes, and leaving projects one no longer works in (find_inactive_projects, unassign_from_projects). Use when asked about milestones, releases, sprints, deadlines, the backlog, planning tickets into a delivery, release notes, or cleaning up project memberships.
---

# Planning in Tracker

Milestones and the backlog of Tracker projects, through its MCP server (see `tracker-tickets` for connecting it,
finding projects and writing ticket text). Creating and changing milestones and planning tickets are **staff only**;
clients can read milestones and the backlog of the projects they belong to.

## Concepts

- A **milestone** is a delivery with a start date and a deadline (and optionally a description, a responsible staff
  member and a parent milestone). Statuses: active, completed, archived. Tickets are only planned into active
  milestones that have both dates.
- The **backlog** is every open ticket without a milestone. Its own priority (`high`, `normal`, `low`; none counts as
  normal) orders the backlog only, and is cleared when the ticket gets a milestone.
- A ticket's **work package** (billing and time) is separate from its milestone (delivery in time).

## Tools

- `get_milestones` (`status`: active, completed or all): dates, status and ticket counts (staff also estimates).
- `create_milestone` (`name`, `description`, `start`, `deadline`, `responsible`, `parent`).
- `update_milestone` by its current name: the same fields, `name` to rename, `status`. Completing (`completed`)
  leaves open tickets in it unless `move_open_to` names another milestone or `"none"` (back to the backlog).
- Planning a ticket: `update_ticket` with `milestone` (a name, or `""` for the backlog) or `backlog_priority`.
- Finding: `search_tickets` with `milestone:"Rel 2"`, `milestone:none`, `backlog:yes`, `backlog:high`,
  `sort:backlog`, `sort:deadline`.
- `find_inactive_projects` (`days`, default 180) and `unassign_from_projects` (`projects`; without `confirm: true`
  only a preview).

Show what will change and get a go-ahead before creating or changing milestones, moving tickets or leaving projects.

## Workflows

**Plan a milestone.** `get_milestones` and the backlog (`search_tickets backlog:yes sort:backlog`). Propose which
tickets go in, with their estimates (staff) against the time until the deadline, and what stays in the backlog. After
approval, `create_milestone` if needed and one `update_ticket` per ticket.

**Where a milestone stands.** Its tickets by status (`milestone:"Rel 2"`), what is still open or blocked
(`blocked:yes`), what waits for the client (`status:waiting`), and the days left. A short summary with references.

**Complete a milestone with release notes.**

1. Closed tickets in it: `search_tickets` with `milestone:"Rel 2" status:closed`; read each that is unclear.
2. Draft release notes grouped as new, changed and fixed, one line per ticket in plain words with its reference.
   **Only from what a client may read**: never internal tickets or notes, estimates or time, unless the user says the
   notes are for staff only.
3. Ask where the open tickets go (another milestone, the backlog, or stay).
4. After approval: `update_milestone` with `status: completed` and `move_open_to`, and the notes in `description` if
   the user wants them on the milestone (Tracker's own "Draft release notes" button on the milestone page writes a
   draft too).

**Leave old projects.** `find_inactive_projects`, show the list, `unassign_from_projects` without `confirm` as a
preview, and with `confirm: true` only for the projects the user picks. Staff still see every project afterwards;
only membership and watching stop.
