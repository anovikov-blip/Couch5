# CLAUDE.md

## Capturing tasks and reminders

Whenever I mention something I need to do — a task, a reminder, a to-do, a
commitment, a deadline, or a follow-up — add it to `context-directory/reminders.md`.

This applies even when I say it in passing. I do not have to ask for it to be
written down; mentioning it is the request.

### How to keep that file

- **Organize by category.** Categories come from what is actually on the list, not
  from a fixed template. Re-cut them when the shape of the list changes.
- **Within each category, order by urgency** — most urgent first. Use explicit dates
  when I give them.
- **Mark urgency** with one of: `[!]` urgent, `[~]` soon, `[ ]` no deadline set.
- **Don't invent details.** If the scope, deadline, or owner is unclear, record the
  item and note what is unknown under an `*Open:*` line rather than guessing.
- **Don't drop anything.** When reorganizing, every existing item survives the rewrite.
- Update the `Last updated` date at the top on every change.

### Committing

Commit and push changes to `main` so the file is visible in the GitHub mobile app
without switching branches.

## Context

- I am based in **Hong Kong (Asia/Hong_Kong, UTC+8)**. My Google Calendar's own
  timezone is set to `America/Mexico_City`, which is *not* where I am. Always resolve
  "today" and "tomorrow" against Hong Kong time, and query the calendar with explicit
  `Asia/Hong_Kong` day boundaries — a Mexico City day covers the wrong hours.
- My meetings span Hong Kong, Europe, Brazil and Mexico. When proposing a time block,
  say what the local time is on the other end if the work involves calling someone.

## "Plan my day"

When I say **"plan my day"**, do this in order:

1. Check my calendar for that day (Hong Kong day boundaries).
2. Read `context-directory/reminders.md`.
3. Show me both: what's on the calendar, and what's on the reminders list.
4. Ask me what my priorities are for the day.
5. Suggest a schedule that fits those priorities around the existing meetings.
6. Once I confirm, add the time blocks to my calendar.
7. Create a daily note with the finalized schedule.

### Daily notes

Daily notes live in `daily-notes/`, organized by year and month:

```
daily-notes/YYYY/MM-Month/YYYY-MM-DD.md
```

For example: `daily-notes/2026/04-April/2026-04-27.md`

Create the year and month folders if they don't exist yet.

## Running log

Throughout every conversation, keep a running log in today's daily note. Add an entry
at meaningful moments — starting a task, finishing something, hitting a blocker, making
a decision. One line per entry, with a timestamp.

Do this quietly. Don't mention the logging, don't report it, don't ask about it.
Create today's daily note if it doesn't exist yet.

## Calendar events you create

Any time you add an event to my calendar, prefix the event title with 🤖 so I can see
at a glance which events you created.

Example: `🤖 Prompt Pay — Thailand`

This applies to every event you create, including time blocks from "plan my day".
Don't add the prefix to events created by anyone else.
