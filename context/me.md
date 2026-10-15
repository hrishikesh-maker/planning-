# About me

The only file you must fill in before the first run. Replace every value in `<angle brackets>`. Both routines read it on every run. If a value they need is still a placeholder, they stop and report it instead of guessing.

The `setup` skill can fill most of it for you: open Claude Code in your copy of this repository and say "set me up".

## Who I am

- Name: <your full name>
- Role: <your role, for example "QA engineer" or "Product designer">
- Email: <you@fuelpass.com>
- Slack user ID: <U0XXXXXXXXX>. In Slack: your profile, the three-dot menu, "Copy member ID". A direct message to this ID is a message to yourself; both routines send their output there.
- Time zone: <Asia/Dubai> (UTC offset <+04:00>). Work days: <Monday to Friday>.
- Private calendar blocks to skip: <for example "Lunch", "Focus time", or "none">

## Schedule

- Morning brief: <07:30> local time on work days.
- Week planner: <06:30> local time on Mondays, before the morning brief.

## Where my work shows up

- Slack channels to read every morning (name and ID; `context/sources.md` lists the common ones): <#product-core C0C1MD4U3EW, ...>
- Weekly "My week" post: <channel where you post it, or "none">
- Jira statuses where a ticket waits on me (counted only when the ticket is assigned to me): <for example "PM review" for a product manager, "Code Review" for an engineer, "Ready for testing" for QA>
- Jira projects I work in: <FD, FUEL>
- Team planning meeting: <the calendar title, for example "Fuelpass: Product planning", or "none">
- People I plan with: <names of the people in that meeting, or "none">. The week planner suggests a goal for each of them.

## Personal Confluence space

The week planner publishes one page per week here.

- Space key: <~...>
- Space id: <...>
- Homepage id: <...>
- Parent page "Weekly plans": <page id, or "create on first run">. The week planner creates the page on its first run and writes its id here.

How to find the three values: your personal space key is `~` followed by your Atlassian account ID without the `:` and `-` characters. The `setup` skill looks them up with the Atlassian connector.
