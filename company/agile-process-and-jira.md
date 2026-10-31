---
title: Agile process, Jira and team ownership (current state)
summary: How the technology teams actually work today according to Sam: who owns what, the state of the Jira board and backlog, the missing Scrum roles and ceremonies, the use of Claude to refine backlog items, and the gaps in QA and acceptance.
updated_at: 2026-10-30
sources: [conversation with Sam, 2026-10-30 (../meetings/2026-10-30-sam-jira-and-agile-process.md; Granola summary), follow-up with Rick, 2026-10-30 (../meetings/2026-10-30-rick-follow-up.md; Granola summary), Jira epic DR-2907 and its stories read through the Atlassian connector on 2026-10-30, Eduardo (his reading of the process, 2026-10-30), meeting with Rick, 2026-10-26 (Sam moving to support, ../meetings/2026-10-26-rick-kickoff.md), conversation with Sam, 2026-10-29 (../meetings/2026-10-29-sam-business-rules.md)]
status: draft
---

# Agile process, Jira and team ownership (current state)

This is a snapshot of **2026-10-30**, from a single source (Sam, through the Granola summary). It describes what is, not what should be. Eduardo's reading: it is disorganized for now, nothing is perfect, but it is running. For tools, hours and meeting cadence see [ways-of-working.md](ways-of-working.md); for work culture see [culture-and-expectations.md](culture-and-expectations.md).

## In plain words

Scrum is a set of team habits (planning the sprint, daily check-ins, retrospectives, a person who guards the backlog). Quorum kept the **board** (Jira) but dropped most of the **habits**: work still gets done because a few people know the system very well and talk to each other when something blocks them. It works like a small kitchen where the same cook takes the orders, cooks, tastes the food and decides the menu: fast while that cook is there, fragile if they leave.

## Who owns what

| Area | Who (according to Sam) | What they do |
|---|---|---|
| Product | Rick | Drives the work of dev |
| Development | the dev team | Leans on infrastructure |
| Internal infrastructure | Todd | Data and cybersecurity |
| Customer infrastructure | Darren | Customer servers and hosting |
| External product owner | Patrick | Sends occasional requests |
| Product support | not named | Should send feature requests and bugs through product |

- **Gray areas are a known, ongoing issue.** There is no stable process for who decides what between product, dev and infrastructure.
- Requests **sometimes bypass product and go straight to dev**. Product support is supposed to funnel feature requests and bugs through product but often does not.
- `(inferred)` "Darren" is probably Darren Hawthorne, Director, Product Infrastructure, on the public website (see [people/directory.md](../people/directory.md)). Not confirmed.

## Roles that nobody holds today

- **No dedicated Scrum Master or product owner.** Sam has been acting as product owner, Scrum Master, developer and QA at the same time.
- Sam logs time against a **standing refinement issue** in Jira; it can reach about **2 hours a day**.
- `(inferred)` According to Rick (2026-10-26), Sam moves to the support team in the week of 2026-11-02. Who would absorb these roles afterwards was not discussed; this is an open question.

## Jira board and backlog

- The team is on **sprint 116**.
- **Much of the product backlog is stale or obsolete.** Sam's examples of deprecated items: references to the ACA pipeline credentials and to Synapse (see [Synapse in the glossary](../glossary.md#synapse-sql-server-and-power-bi-fabric); the migration is in [q-analytics/powerbi-to-superset-migration.md](../q-analytics/powerbi-to-superset-migration.md)).
- The backlog **needs human attention and cleanup but is not getting it**.

## Ceremonies

- Formal Scrum ceremonies (sprint planning, retrospectives, dailies, refinement) **no longer happen**.
- Meetings are **ad hoc**, triggered by a blocker or a request for an update.
- When Sam is stuck he **escalates to Rick** instead of waiting.

## Refinement and estimation with Claude

- Sam uses Claude connected to **GitHub, local clones of the repositories, Jira and SQL servers** (the Granola note says "Visual Basic", a transcription error confirmed by Eduardo: Sam uses **VS Code**; see [the meeting note](../meetings/2026-10-30-sam-jira-and-agile-process.md)).
- A **Jira PBI Creator** skill, **built by Rick**, refines backlog items automatically. After refinement the **Estabot** label is applied automatically, which triggers story point estimation. See the [glossary](../glossary.md).
- **Story points are a known friction point:** they measure **complexity, not time**, but stakeholders treat them as time.
- The team ran an **"AI Olympics"** to explore AI use cases (what came out of it was not said).
- Jira and other integrations use **MCP connectors**; the credentials are **stored outside Claude** (the channel was not given; no value is recorded here).

## Update from the follow-up with Rick (2026-10-30)

Source: [meetings/2026-10-30-rick-follow-up.md](../meetings/2026-10-30-rick-follow-up.md) (Granola summary).

- **Handover:** Sam rolls off the DR board on Monday (2026-11-02, as in the 2026-10-26 plan). Eduardo **inherits the backlog of Q Analytics support issues**. Rick was to pull Sam into a short call to align on the sprint handover.
- **Two work streams for Eduardo:** (1) **new projects**, starting with the Agent Console rebuild (epic DR-2907, see [tasks/dr-2907-agent-console-rebuild.md](../tasks/dr-2907-agent-console-rebuild.md)), a ground-up pipeline and visualization build with Patrick as business partner; (2) **ongoing bugs and enhancements** from the support team for Q Analytics.
- **No product manager today.** Rick will help with refinement in the interim and Patrick is to book a separate meeting. This is consistent with Sam's description of the missing product owner role, and with Patrick being the person who writes the stories `(inferred)`.
- **Estabot on the epic:** Rick ran Estabot (described in the note as "Rovo/AI in Jira") on the Agent Console epic; the stories came out as 5 points and the final visualization story as 8. Eduardo should tell Patrick that Estabot already ran. Whether this is the same Estabot Sam described is not confirmed.
- **Sprint:** two Agent Console stories are in the sprint that starts on Tuesday (`(inferred: 2026-11-03)`; the note says "Nov 1", which is a Sunday). On 2026-10-30 the four stories were still "Awaiting Refinement" and unassigned.

## QA and acceptance

- **There is no dedicated QA.** Developers test their own work.
- The dev environment cannot be reached by customers, but once the developer is satisfied **changes go straight to production**, with **no second pair of eyes**.
- **The acceptance flow is broken:** items sit in **Ready for Acceptance** with no notification until someone moves them to **Done**.
- The note says the QA resource was likely lost because of resource constraints.

## Eduardo in this process

- **Jira:** Rick granted Eduardo access. No cards were assigned yet when he talked to Sam; one appeared during the call. By 2026-10-30 the only open card assigned to Eduardo is the epic **DR-2907** (Agent Console, created by Patrick Manning on 2026-10-29), which is probably the card that appeared `(inferred)`; see [tasks/dr-2907-agent-console-rebuild.md](../tasks/dr-2907-agent-console-rebuild.md).
- **Sam's stance:** open to new ideas; "it's how we've always done it" is the worst reason to keep doing something. He encouraged Eduardo to **record observations in Jira issues at minimum**.
- **Eduardo's approach (his own words in the call):** flag issues, document them and suggest improvements without forcing change. Example from a previous job: an automated deploy pipeline for Dundas BI through REST APIs that cut deployment from about 1 hour to about 3 minutes.
- On 2026-10-30 Eduardo had early notes on Superset and on a data ingestion pipeline (the note calls it "Data Ingestion 2", a name neither the documentation nor Eduardo recognizes) and ideas for improvement; Sam and Eduardo scheduled a review an hour after the call (see [onboarding/first-weeks.md](../onboarding/first-weeks.md) and [improvement-ideas.md](../improvement-ideas.md)).

## Where Eduardo's Jira cards are documented

Cards assigned to Eduardo (epics, stories and other tasks) are documented one file per epic or task in the [tasks/](../tasks/tasks-index.md) folder, together with dates, estimates, effort and outcomes ([metrics-framework.md](../tasks/metrics-framework.md)). This file describes the **process**; the task files describe **the work**.

## Improvement suggestions

All suggestions about this process (notification on Ready for Acceptance, backlog cleanup, a single intake path, a light replacement for the ceremonies) are in the global [improvement-ideas.md](../improvement-ideas.md), section "Jira and agile process suggestions" (JIRA-1 to JIRA-4). They are `(inferred)` by the documenting agent, not proposals from Sam. Security suggestions are in the same file, section "Security and governance suggestions".

## Gaps and open questions

- Who takes over product owner, Scrum Master, QA and reference-for-reports duties when Sam moves to support (week of 2026-11-02)?
- Who is Patrick, and what does "external product owner" mean (external to Quorum, to the team, or to a product)? What is known: Patrick **Manning** reports the Agent Console epic and stories in Jira (project "Data and Reporting") and is the business partner for the rebuild (Rick, 2026-10-30); his Jira profile uses the Atlantic time zone.
- Who will refine the stories and decide priorities for the support backlog after Sam leaves, given that there is no product manager (Rick helps "in the interim")?
- Which team staffs product support, and is it John Hynes's department (Director, Product Support, on the website) `(inferred)`?
- Rick's title: the note says "Product (Rick)"; the CTO of Eduardo's notes and the website's "Chief Product Officer" may be the same person (see [people/directory.md](../people/directory.md)).
- Where are the stories of Michael's epic in Jira (project, board) and is there Confluence?
- What came out of the "AI Olympics", and is there a shared list of Claude skills besides Jira PBI Creator?
- How are Estabot estimates reviewed by a person, and who decides the final story points?
- Is the pace of formal ceremonies a conscious decision or a temporary state?
