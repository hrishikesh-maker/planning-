---
name: setup
description: Ten-minute setup of time-planning for a new user. Makes their private copy of the repository if needed, works out their settings from Slack, Atlassian and the calendar, asks them to confirm everything in one message, saves context/me.md, creates the morning brief and week planner routines, and starts the first brief. Use when someone says "set me up", "set up time planning", "fill in me.md" or "help me start".
---

# Setup

Goal: the person answers one message, and ten minutes later their first morning brief is on its way. Look things up instead of asking. Ask once, at the end of step 2, with everything filled in.

This runs interactively, with the person watching. Never send a Slack message, create a page or change anything outside their copy of this repository, except the two routines in step 4 and the first runs in step 5.

## 0. Check the basics (no questions yet)

1. **Their own copy.** Run `git remote get-url origin`.
   - It points to their own copy (not `saveliy-product/time-planning`): carry on.
   - Teammates are added to the template as collaborators, which gives them write access. Never commit or push to `saveliy-product/time-planning` during setup.
   - It points to the template, or there is no checkout: make their copy with `gh repo create <their GitHub user>/time-planning --private --template saveliy-product/time-planning --clone` (`gh api user --jq .login` gives the user), then work in that folder. No `gh`: ask them to press "Use this template" on https://github.com/saveliy-product/time-planning, create a **private** repository under their own account, and paste its URL; clone it.
2. **Connectors.** The five claude.ai connectors must be connected: Slack, Atlassian Rovo, Gmail, Google Drive, Google Calendar. In this session each one shows up as tools named `mcp__<connector uuid>__<tool>`. List the ones that are missing and send the person to https://claude.ai/customize/connectors to connect them; continue with the rest meanwhile.

## 1. Look up (read-only)

Make these calls in parallel where you can.

- **Who they are.** The Slack connector's `slack_search_users` tool description names the logged-in user's ID. `slack_read_user_profile` on it gives name, title, email and time zone.
- **Atlassian.** `atlassianUserInfo` gives the account ID. Personal space key = `~` + the account ID without `:` and `-`. `getConfluenceSpaces` with `keys: ["<key>"]` gives the space id and homepage id. If they have no personal space, say so and continue: the week planner needs it, the morning brief does not.
- **Their week.** `list_events` for the next 7 days. From it take: a recurring meeting with "planning" in the title that they attend (their team planning meeting) and its attendees; their private recurring blocks (for example "Lunch") to skip.
- **Their channels.** `slack_search_public_and_private` with `filters: "from:<@their ID> after:<30 days ago>"`, `sort: "timestamp"`, `limit: 20`: the channels they post in most. Match them against the channel table in `context/sources.md`; take up to five.
- **Their Jira role.** `searchJiraIssuesUsingJql` with `project = FD AND assignee = currentUser() AND statusCategory != Done`, `fields: ["status"]`: the statuses their tickets sit in. Defaults by role: product "PM review"; engineer "Code Review"; QA "Ready for testing", "In Testing"; anyone else "none". Use the title from Slack to choose.
- **Routine settings.** Load the `schedule` skill (Skill tool) only to read what it lists: the person's cloud environments (`environment_id`) and claude.ai connectors (`connector_uuid`, `name`, `url`). Do not follow its own create workflow. If it cannot list connectors, take the UUIDs from this session's tool names and the URLs from `routines/daily-brief.routine.md`. Prefer an environment named after FuelPass, else "Default".

## 2. Ask once

One message with every value filled in, and one question: "Correct? Reply 'ok' or change any line."

```
Name, role: <from Slack>
Time zone: <from Slack>
Morning brief: work days 07:30 · Week planner: Monday 06:30   (local time)
Slack channels: <up to five>
Jira statuses that wait on you: <list>
Team planning meeting: <title, or none> · People: <names, or none>
Calendar blocks to skip: <list, or none>
Personal Confluence space: <name> (<key>)
Routines: model Opus 5.5, environment <name>, connectors Slack, Atlassian, Gmail, Drive, Calendar
After saving: run the first morning brief now (and the week planner now, which publishes this week's page)
```

Change only what they correct.

## 3. Save

Fill every value in `context/me.md` (keep "Weekly plans" as "create on first run"). Then `git add context/me.md && git commit -m "setup: me.md" && git push origin main`. The routines read the repository from GitHub, so the push is part of setup.

## 4. Create the two routines

For each of `routines/daily-brief.routine.md` and `routines/weekly-plan.routine.md`: build the create body from the file (name, cron, model, tools, prompt with their repository URL), with their `environment_id`, their repository as the only source and the five connectors in `mcp_connections`. Convert the local times to UTC for the cron (07:30 in UTC+4 is `30 3 * * 1-5`; 06:30 on Monday is `30 2 * * 1`). Create it with `RemoteTrigger` (`action: "create"`). Write each routine ID into the "Routine ID" line of its file, then commit and push.

If their time zone has daylight saving, tell them the routine runs one hour earlier or later after each clock change; they fix it by asking Claude to update the cron.

## 5. Start the first runs

1. `RemoteTrigger` `run` on the morning brief. If they said yes in step 2, also on the week planner.
2. After about a minute, `list_runs` on each. A run that started is fine: the brief arrives in Slack in about 15 minutes.
3. No run, or a run that failed at the start: `get` the routine and `get_run_log` on the run. The common cause is repository access: the routine cannot clone a private repository until the person gives the Claude GitHub app access to it. Tell them exactly that, with the routine link `https://claude.ai/code/routines/<id>`, where claude.ai shows the button.

## 6. Finish

Three lines: what was set up (with both routine links), when the first brief arrives, and how to steer it (`feedback:`, `done:`, `skip:`, `term:` in a Slack message to themselves). Mention test mode only if something looked wrong: "run the morning brief in test mode" shows a brief without sending it.
