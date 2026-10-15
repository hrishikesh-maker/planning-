# Sources, IDs and queries

FuelPass-wide facts the routines need on every run. Last checked 14 Oct 2026. Personal values (your Slack ID, time zone, channels, personal space) are in `context/me.md`, not here. Update this file when something changes; the routines never guess an ID.

## The weekly rhythm

Times are Asia/Dubai. The calendar is the source of truth for dates and times; this table explains what each meeting is for.

| Day | Time | Meeting | What it means for the routines |
| --- | --- | --- | --- |
| Every weekday | 09:16 to 09:31 | Fuelpass Daily (stand-up) | Gemini notes arrive about 09:35, so the morning brief sees yesterday's stand-up, not today's. |
| Monday | 10:00 to 11:00 | Fuelpass: Product planning | The product team decides its week here. The team routine publishes the result at about 11:50 (see Confluence below). |
| Monday, sprint end | 11:30 to 12:30 | Sprint review | On staging. Finished work is shown, spill-over is agreed. Also the go-live sign-off. |
| Monday, sprint end | after the review | Production release | Every two weeks. A rotating trio (one engineer, one product person, QA) decides the feature flags. |
| Monday, sprint end | 15:00 to 16:00 | Sprint retrospective | Workshop format. |
| Tuesday, sprint start | 10:00 to 11:00 | Sprint planning | Every two weeks. |
| Tuesday | 15:30 to 17:00 | Stories review (pre-refinement) | Stories are walked through before Thursday's refinement. |
| Thursday | 10:30 to 12:00 | Refinement meeting | Stories are explained and estimated. |
| Thursday | 15:00 to 16:00 | Weekly RICE prioritization | Priorities and estimates of discovery ideas only. |
| Friday, sprint end | morning | Staging cut-off and smoke tests | Not a meeting. Sprint code is on staging by Friday morning. |
| Friday | 17:00 to 17:30 | FuelPass - Weekly D.Brief | Whole-company business brief. |

Sprint calendar: Sprint 1 = Tue 22 Sep to Mon 5 Oct 2026; Sprint 2 = Tue 6 Oct to Mon 19 Oct; then every two weeks (Sprint 3 starts Tue 20 Oct). "Sprint week 1" starts with planning on Tuesday. "Sprint week 2" ends with the Friday cut-off and the Monday review, release and retrospective.

## Slack (connector "Slack")

| Channel | ID | What it is |
| --- | --- | --- |
| #product-core (private) | `C0C1MD4U3EW` | Product team room. "My week" posts and the team's weekly planning summary go here. |
| #product-pilot (private) | `C0BJC2XQUNA` | Pilot customers. |
| #product-design-cvs (private) | `C0BH6FGDYT0` | Design work. |
| #product-release-notes | `C0BRTRSRW9X` | Automated release notes. |
| #product-partnerships | `C0C0EU7U5BR` | Partnerships. |
| #all-fuelpass-product | `C098W46PDD5` | Whole-company product channel. |
| #development-ui-questions | `C09QT0WUPN0` | Engineers' questions about the interface. |
| #rivalradar-competition | `C0BF64YE8UB` | Competitor watch. |

Other channels: find the ID with `slack_search_channels` once and write it into `context/me.md`.

Queries that work (`<me>` is the Slack user ID from `context/me.md`):

- Mentions of you: `slack_search_public_and_private` with `keywords: ["<@<me>>"]`, `filters: "after:YYYY-MM-DD"`, `sort: "timestamp"`. Returns direct messages too. **`after:` excludes the named day itself**: to see Friday 9 Oct, write `after:2026-10-08`. Every result carries a permalink; use it as the item's link.
- Your own posts: `filters: "from:<@<me>> after:YYYY-MM-DD"`.
- Channel history: `slack_read_channel` with the channel ID, newest first. `oldest` (Unix seconds) cuts at the window start.
- Thread replies: `slack_read_thread` with the channel ID and the parent `ts`. A direct-message thread needs the DM channel ID, which the search result shows.
- Your direct messages with yourself: `slack_read_channel` with `channel_id: "<me>"`.
- Send a message to yourself: `slack_send_message` with `channel_id: "<me>"`. The connector takes markdown: `**text**` is bold, a single `*text*` is italic, `[text](url)` is a link. 5000 characters at most. Never use `slack_send_message_draft` in a routine.

## Gmail (connector "Gmail")

- Meeting notes arrive by email from `gemini-notes@google.com`, subject `Notes: "<meeting name>" <Mon D, YYYY>`, usually within 30 minutes after the meeting. The plain-text body has Quick Notes and "Suggested next steps" as `[Owner] Title: description`.
- Query: `search_threads` with `query: "from:gemini-notes@google.com after:YYYY/MM/DD"`, then `get_thread` with `messageFormat: "PLAIN_TEXT"`.
- Calendar invitations are a fallback only when the Calendar connector fails: `search_threads` with `query: "subject:(invitation OR \"updated invitation\") newer_than:14d"`. The newest "Updated invitation" for an event wins. Say in the output when this fallback was used.

## Owner tags in meeting notes are unreliable

- The office meeting room joins as one account. Gemini labels everything said from it with the room's name: "UCIG DMCC", "Conference" or "Co" (calendar invites list the room as `conference@ucigae.com`). Several people speak from that room, so the label is never one person.
- `[The group]` means nobody.
- Gemini mishears names. Check a name against `context/people.md` before making it an open loop.
- Resolve the real owner from the transcript before giving a commitment to a person (accuracy rules in the morning brief skill, step 3F).

## Google Calendar (connector "Google Calendar")

- Load the tools with ToolSearch `+calendar events list`. `list_events` with `startTime` and `endTime` in RFC 3339 with your offset (for example `2026-10-14T00:00:00+04:00` to `2026-10-15T00:00:00+04:00`), `timeZone` from `context/me.md`, `orderBy: "startTime"`, `pageSize: 50`. Ask for one day or one week at a time; two weeks is about 70 KB.
- Never call `create_event`, `update_event`, `delete_event` or `respond_to_event`.
- Keep only events where your `responseStatus` is not `declined`. Skip the private blocks named in `context/me.md`.
- A meeting recorded by Gemini has an attachment titled "Notes by Gemini" whose `fileUrl` contains the Drive document ID: use it with `read_file_content` instead of searching Drive.
- The calendar only shows meetings you are invited to. A meeting you cannot find is "not on your calendar", not "not booked".

## Google Drive (connector "Google Drive")

- Every Gemini meeting produces a Google Doc named `<meeting name> - YYYY/MM/DD HH:MM GMT+04:00 - Notes by Gemini` with Quick notes, Full notes (Summary, Decisions, Next steps, Details with timecodes) and the full Transcript.
- Search: `search_files` with `query: "title contains 'Notes by Gemini' and modifiedTime > 'YYYY-MM-DDT00:00:00Z'"`. Then `read_file_content` with the file ID. Prefer the ID from the calendar attachment.
- A file with `mimeType: application/vnd.google-apps.shortcut` belongs to another organiser; `read_file_content` may fail on it. Fall back to the notes email and say the transcript was not read.
- A full doc with transcript is 30 to 60 KB. Read the transcript only to check an owner, a decision or a quote.

## Jira (connector "Atlassian Rovo")

- Site `https://fuelpass-team.atlassian.net`, cloudId `ace89eff-23d4-4534-8f6b-b5bdce74d823`.
- Projects: `FD` delivery, `FUEL` product discovery (RICE ideas), `FT` triage.
- Fields: story points `customfield_10042` ("Story Points"); the older `customfield_10016` carries the same values and is a fallback only; sprint `customfield_10020`.
- FD statuses: Ideas, In Refinement, Requirements done, Ready for estimation, Ready for development, To-do, In Progress, Code Review, Developer test, Ready for testing, In Testing, Needs further development, PM review, Ready for release, Done, Blocked, Archived.
- Scrum board "FD Sprints", board 67: `https://fuelpass-team.atlassian.net/jira/software/c/projects/FD/boards/67`. Refinement board: board 34.
- PM review has a 2-working-day rule; automation then mentions the assignee. Every waiting status belongs to the ticket's assignee: a ticket assigned to someone else is theirs to chase.
- Label `carryover`: work that was already in QA, PM review or Ready for release when the sprint started. It sits in the sprint but stays out of the velocity reading. Compare only new work with the sprint target.
- JQL that works (`searchJiraIssuesUsingJql`, `maxResults` 50; `searchResultMode: "count"` when only a number is needed; ask only for `["summary","status","assignee","updated","customfield_10042"]`, because answers overflow):
  - Waiting on you: `project = FD AND status in ("<your statuses>") AND assignee = currentUser() ORDER BY updated ASC`
  - Your open work: `project = FD AND assignee = currentUser() AND statusCategory != Done ORDER BY updated DESC`
  - Stuck (team): `project = FD AND status in (Blocked, "Needs further development") ORDER BY updated ASC`
  - Status changes in a window: `project = FD AND status CHANGED DURING ("YYYY/MM/DD","YYYY/MM/DD")`
  - Entered a status in a window: `project = FD AND status CHANGED TO "Ready for release" DURING ("YYYY/MM/DD","YYYY/MM/DD")`
  - Sprint, new work: `project = FD AND sprint in openSprints() AND status != Archived AND (labels IS EMPTY OR labels NOT IN (carryover))`. A sprint holds more than 100 items: when the answer says `hasNextPage: true`, call again with `nextPageToken` until it is false.
  - Sprint, carryover: `project = FD AND sprint in openSprints() AND status != Archived AND labels = carryover`
  - Your discovery ideas: `project = FUEL AND assignee = currentUser() AND statusCategory != Done ORDER BY updated DESC`
  - Created since a moment (to check whether a promised ticket exists): `project in (FD, FUEL) AND created >= "YYYY-MM-DD HH:mm"`; the reporter shows who created it.
- What the connector cannot see: board settings (column names, totals), the FuelPass app and Auth0 (who logged in), WhatsApp and phone calls. Say "not checked" for these.

## Confluence (same connector)

- Shared space `FuelPass` (id `5603332`). The routines never create or edit pages there.
- The product team's weekly plan: the team routine (repository `fuelpass-platform/product-planning`) publishes `Weekly planning — <D Mon> to <D Mon YYYY>` under page `207388676` every Monday at about 11:50 Dubai. Read it with `getConfluencePageDescendants` on `207388676`, then `getConfluencePage`. Its action items name owners from the meeting transcript.
- Pages changed recently: `searchConfluenceUsingCql` with `cql: "space.key = \"FuelPass\" AND type = page AND lastmodified >= now(\"-7d\") ORDER BY lastmodified DESC"`, `limit: 25`. It sometimes times out; retry once.
- A page that contains smart links refuses a markdown update (error 422). Read and update such a page in `html` (`getContentFormatGuide` with `toolName: "updateConfluencePage"` shows the format).
- Reference pages: Development Process `185532466`; Sprints at FuelPass `202571801`; Definition of Ready `87588865`; Estimations `32047143`.
