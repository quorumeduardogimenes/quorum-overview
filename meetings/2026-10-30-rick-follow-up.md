---
title: Follow-up with Rick, 2026-10-30 (first-week review, Agent Console, sprint, Mori rebranding)
summary: Note (Granola) from Eduardo's follow-up with Rick at the end of his first week: what Eduardo showed from the sandbox, how the Agent Console epic enters the sprint, Sam's handover, and the rebranding of Q Analytics as "Mori Metrics".
updated_at: 2026-10-30
sources: [Granola summary of the meeting "Eduardo | Rick Connect" with Rick Johnston, 2026-10-30 at 13:30 BRT, read through the Granola connector on 2026-10-30 (https://notes.granola.ai/d/6806268d-7a2f-4906-ad1f-d2a56f7849f4; Granola summary, not the full transcript)]
status: confirmed
---

# Follow-up with Rick, 2026-10-30

- **Date:** 2026-10-30 (Friday), 13:30 BRT. The Granola note was last updated at 14:19 BRT.
- **Participants:** Eduardo and Rick (Rick Johnston in Granola).
- **Title in Granola:** "Eduardo | Rick Connect".
- **Context:** end of Eduardo's first week; follow-up on the first activity (the Agent Console report, epic DR-2907 in Jira).
- **This note is a primary source:** the raw text below (Granola, in English) is not rewritten. Corrections go in "Later corrections".

## How to read these notes

- The Granola text is an **AI summary**, not a transcript. Statements are what Rick and Eduardo said as of 2026-10-30, not official Quorum policy.
- Names and terms from automatic transcription may be wrong; see "Later corrections".
- The meeting had no personal or off-topic section; nothing was left out of this note.

## Where each fact was documented

| Topic | File |
|---|---|
| The Agent Console epic and stories, who is the business partner, estimates, sprint | [tasks/dr-2907-agent-console-rebuild.md](../tasks/dr-2907-agent-console-rebuild.md); the report and its data: [q-analytics/agent-console-report.md](../q-analytics/agent-console-report.md) |
| Sam leaving the board, Eduardo inheriting the Q Analytics support backlog, no product manager, Rick helping with refinement, Estabot run on the epic | [company/agile-process-and-jira.md](../company/agile-process-and-jira.md) |
| Rebranding of Q Analytics as "Mori Metrics", Mori as the AI brand, MCP/AI harness vision | [q-analytics/overview.md](../q-analytics/overview.md), [products/brands.md](../products/brands.md) |
| What Eduardo showed (sandbox pipeline, SQL job, test schema, Superset dashboard, Handlebars plan) | [q-analytics/dev-ingestion-practice.md](../q-analytics/dev-ingestion-practice.md), [q-analytics/superset-and-security.md](../q-analytics/superset-and-security.md) (already documented there) |
| Patrick and Rick in their roles | [people/directory.md](../people/directory.md) |
| Meeting row and next steps | [onboarding/first-weeks.md](../onboarding/first-weeks.md) |
| New terms (Agent Console, Mori, Mori Metrics, Epic, Crystal Reports; updates to Estabot, MCP, Q Analytics, BDC) | [glossary.md](../glossary.md) |

## Original notes (in English, unedited)

# First Week Onboarding (Days 1–5)

- Main contact so far: Todd
  - Introduced Eduardo to the data ingestion tool (built in Go, VS Code)
  - Covered Key Analytics, Apache Superset, and the full data lifecycle and BI development process
  - Explained the agent-based architecture: an agent runs on the client server, and the ingestion tool connects to it
  - Granted access to dev/test instances: data ingestion tool, Postgres warehouse, and Superset sandbox

# Sandbox Work Demonstrated

- Created a new pipeline ("Other Mine Test Edu") to keep work separate from Todd's migration tests
- Wrote a SQL job querying a DealerMind context/transactions table (leads, emails, calls)
- Ingested data into a new test schema and table on the Postgres dev instance
- Built a Superset dashboard on top of that data
  - Plans to explore Handlebars (HTML/CSS) to customize visuals: colors, font sizes, etc.
- Started exploring the Agent Console report using Claude Code, prompting in Portuguese for speed

# Sprint Planning and Jira Workflow

- Sam rolls off the DR board Monday; Eduardo inherits the backlog of Q Analytics support issues
- Work will be a mix of two streams:
  - New projects: Agent Console Reboot in Q Analytics (epic DR-2907), ground-up pipeline and visualization build with Patrick as business partner
  - Ongoing bugs and enhancements from the support team for Q Analytics (Power BI)
- Ran Estabot (Rovo/AI in Jira) to auto-estimate story points on the Agent Console epic
  - Stories came out as 5-pointers; final visualization story estimated at 8 points
- Two Agent Console stories added to the sprint starting Tuesday (Nov 1)
- No product manager currently; Rick will assist with refinement in the interim, Patrick to book a separate meeting

# Rebranding and AI Direction

- Q Analytics to be rebranded as "Mori Metrics" next year
  - "Mori" is the DealerMind AI brand; everything AI at Quoruminfotech falls under Mori
- Longer-term vision: MCP server connecting to dashboards so users can query data via AI
  - Rick's view: the AI harness is the visualization tool of the future

# Next Steps

- **Join Sam's 3 PM call with Rick** (Eduardo)

  Rick will ping Sam to be pulled in for 5 minutes to align on sprint handover.
- **Connect with Patrick to discuss Agent Console epic** (Eduardo)

  Patrick is booking a meeting today; let him know Estabot has already been run on DR-2907.

## Later corrections

- **2026-10-30, to be confirmed by Eduardo:** "Other Mine Test Edu" is probably a transcription error for **DealerMine Test Edu**, the pipeline documented in [q-analytics/dev-ingestion-practice.md](../q-analytics/dev-ingestion-practice.md) `(inferred)`.
- **2026-10-30, guide rule:** "DealerMind" is a known transcription error for **DealerMine** (see the guide, section 7).
- **2026-10-30, to be confirmed:** "Key Analytics" is probably **Q Analytics** `(inferred)`.
- **2026-10-30, date check:** the note says the sprint starts "Tuesday (Nov 1)", but 2026-11-01 is a Sunday; the Tuesday of that week is **2026-11-03**. Which one is meant is not known `(inferred: 2026-11-03)`. "Monday" for Sam leaving the board matches 2026-11-02, as in [meetings/2026-10-26-rick-kickoff.md](2026-10-26-rick-kickoff.md).
- **2026-10-30, to be confirmed:** "Sam's 3 PM call": the time zone is not stated.
- **2026-10-30, to be confirmed:** the note calls Estabot "Rovo/AI in Jira"; Sam described it as a label applied after the Jira PBI Creator skill refines an item (see [company/agile-process-and-jira.md](../company/agile-process-and-jira.md)). Whether it is the same thing, and what "Rovo" means here, is not known.
- **2026-10-30, to be confirmed:** the note says "Eduardo inherits the backlog of Q Analytics support issues" and calls the support work "(Power BI)"; Q Analytics now runs on Superset (see [q-analytics/powerbi-to-superset-migration.md](../q-analytics/powerbi-to-superset-migration.md)). Whether some support items still concern Power BI reports is not known.
