---
name: tracker-time
description: Reports and checks Happiness staff's time in Kimai through Tracker's MCP tools (log_time, get_my_time, get_ticket_time, remove_time, get_time_activities, touch_ticket, get_work_package_time). Use when asked to log, report, check, correct or remove time on a ticket, to fill in today's or a past day's time, to see what is already reported, or to put a ticket on "My day".
---

# Time in Tracker and Kimai

Kimai is where time is kept; Tracker reports to it and reads from it. These tools are **for Happiness staff only**
and act as the token's owner (see `tracker-tickets` for connecting Tracker). Time is always on a ticket and goes to
the Kimai project of the ticket's work package, with the description `KIP#123 comment`; the person cannot choose
another Kimai project. Clients never see time.

## Tools

- `get_my_time` (`from`, `to`, at most 31 days): what Kimai already has for you, whoever logged it (Tracker, Kimai
  itself, other tools), with totals per day. Admins can pass `person`.
- `get_ticket_time`: all time in Kimai that names a ticket, with each entry's `entry_id`, and what is still waiting
  to reach Kimai.
- `get_time_activities`: the activities to log with. `log_time` defaults to the one you used last.
- `log_time` (`project`, `number`, `duration` like `1.5`, `1,5` or `1:30`, optional `date`, `activity`, `comment`):
  one timesheet. It is **not deduplicated**: every call adds time. Saved in Tracker first and sent to Kimai, later if
  Kimai is down. Not for a future day; Kimai's locked periods still apply.
- `remove_time` (`entry_id` from `get_ticket_time`): only time logged through Tracker, by its author or an admin;
  time entered in Kimai itself is changed in Kimai. Correct a wrong entry by removing it and logging again.
- `touch_ticket`: puts a ticket on your **My day** page for a day (work that left no trace in Tracker), without a
  note on the ticket. Reporting is then done here or on that page.
- `get_work_package_time`: per work package, its time in total and this week, and its open tickets.

## Rules

- **Never invent durations.** Hours come from the user. Propose, show, and log only what they confirm.
- **Check before logging:** `get_my_time` for the day, so nothing is reported twice.
- One `log_time` per ticket and piece of work; the comment says briefly what was done (it becomes the timesheet
  description after the reference).
- Report the result: per ticket the duration logged, and the day's new total.

## Workflows

**Report a day's time.**

1. `get_my_time` for the day: what is already in Kimai.
2. Collect what was worked on: the user's own list, and from git, for each repository they name,
   `git log --all --since=<day> --until=<next day> --author=<their email> --format='%h %ad %s' --date=format:%H:%M`.
   Tickets come from branch names (`882-…`) and references in subjects (`[GBF#882]`); map each to its project with
   `list_projects` when the prefix is not in the text.
3. Propose a table: ticket, what was done (from the commits), already logged, and a duration **left empty or marked
   as a suggestion from the commit times**. Ask the user to fill in or correct the hours.
4. Log the confirmed rows with `log_time` (with `date` for a past day) and show the new total from `get_my_time`.

**Check a ticket's time.** `get_ticket_time`: the total, who logged what, entries shared with other tickets (their
time counts on each), and anything not yet in Kimai.

**Remove or correct an entry.** `get_ticket_time` for the `entry_id`, confirm with the user which entry, then
`remove_time` (and `log_time` again for a correction).
