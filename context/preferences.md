# Preferences

How you want the outputs. Both routines read this before writing anything. Change any rule here; the routines follow this file over the defaults in the skills. The week planner appends what it learned (dated) at the bottom; the morning brief applies your `feedback:` messages there.

## Language

- Plain words, short sentences. No jargon and no abbreviations unless they are in the glossary. Explain a company name the first time it appears in a message ("Northwind Air, a pilot customer").
- Observe, do not command. "Sam is waiting for an answer on X" rather than "you need to reply to Sam".
- No praise, no apologies, no filler.
- Never invent. If a source was not read, say so in one line.

## Morning brief

- Speak to the reader as "you" ("15 tickets are waiting for your review", "You committed to", "Others committed to you").
- Slack formatting through the connector is markdown: `**text**` is bold, a single `*text*` is italic. Header and section titles in `**double asterisks**`.
- Every item ends with a markdown link: Jira `[FD-1234](https://fuelpass-team.atlassian.net/browse/FD-1234)`, Slack the permalink from the search result, meeting notes the Google Doc link, Confluence the page URL. No link available: name the source in words.
- One Slack message. Under 5000 characters. Readable on a phone in two minutes.
- "Needs you today" comes first, ranked, at most 7 items. Each item: bold title, one sentence with the ask and why today, a link.
- Then "Your to-do this week" (planned on Monday, then what came up during the week), then meetings today with one preparation line each, then what was captured yesterday, then the delivery pulse in numbers, then resolved items, then at most 3 unknown terms.
- Quote people verbatim when the wording matters; otherwise paraphrase.
- Empty sections are dropped. An empty brief says "Nothing needs you this morning" and stops.

## Week planner

- The Slack draft uses the "My week" format. If you already post a weekly message in another format, paste one example here and the planner copies it.

```
*My week — 12–16 Oct*

*PRIORITIES*
1. Topic — what "done" looks like this week. One line of context if needed.
2. ...
(3 to 5 items, ranked)

ALSO
- smaller items, one line each

NEED FROM YOU
- @Person: the question, in one line
```

- Strikethrough (`~text~`) marks an item done in a posted "My week"; read the live post, not a search snippet.
- The Confluence page is the long version with evidence. Bullets, not paragraphs. Numbers in tables.
- Propose, do not decide. Rank by: commitments to customers and leadership first, then sprint health, then discovery.
- Keep the proposal to 3 to 5 priorities and leave room: a team planning meeting usually adds items.
- Phrase each priority as its next concrete step ("design shared and scored at RICE"), not as a document ("spec written").
- Phrase each item as a single action you control. Put steps that depend on someone else under NEED FROM YOU.
- For a story-writing priority, "done" means the stories are in Ready for estimation and on the refinement list, not just created.
- When a priority depends on one meeting, name the slot and a fallback slot.
- Take every date from the sprint calendar in `context/sources.md` and from the calendar, never from a note's wording.
- An unposted draft is not a commitment. A missing "My week" post is not missing work.

## Learned (appended by the routines)

Nothing learned yet. Each line will start with its date, for example `- 2026-10-19: ...`.
