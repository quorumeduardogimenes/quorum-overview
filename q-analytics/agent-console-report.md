---
title: Agent Console report (BDC) and where its data lives
summary: What the Agent Console report is today (Crystal Reports on top of SQL tables), how it is refreshed, how it relates to Mori AI, where its data probably lives (dealer database or common database) and the technical questions still open for the rebuild.
updated_at: 2026-10-30
sources: [Jira epic DR-2907 and stories DR-2908 to DR-2911, read through the Atlassian connector on 2026-10-30 (https://quoruminfotech.atlassian.net/browse/DR-2907), meeting with Rick, 2026-10-30 (../meetings/2026-10-30-rick-follow-up.md), meeting with Rick, 2026-10-26 (../meetings/2026-10-26-rick-kickoff.md; task description pasted by Eduardo), Eduardo (2026-10-29 and 2026-10-30)]
status: draft
---

# Agent Console report (BDC) and where its data lives

This file holds the **knowledge** about the report. The **work** of rebuilding it (Jira cards, acceptance criteria, estimates, status, metrics) is in [../tasks/dr-2907-agent-console-rebuild.md](../tasks/dr-2907-agent-console-rebuild.md).

## In plain words

The Agent Console is the dashboard a **BDC manager** (the manager of the dealership's call and appointment center) watches during the day to see how the center handles calls: how many came in, how many were answered, missed or sent to voicemail, how many became appointments, and how that compares with goals. Today it is a **Crystal Reports** file fed by tables in a SQL Server database. Quorum now has an **AI that answers some of those calls (Mori)**, so the report must show human and AI handling side by side.

## The report today

- **Name:** Agent Console, version "S1000" (what `S1000` stands for was not said).
- **Where the logic lives (DR-2908):** split across `team_model_reporting`, `team_model_historical_data` and **Crystal formulas inside the file `TeamModel_BDC_Results_s1000.rpt`**, which exist only in that file. The `.rpt` is the only place to read some of the formulas.
- **Refresh today (DR-2909):** the data is rebuilt every **10 minutes** (from a job the card calls "EraDownHost" into `team_model_historical_data`), except **EM Count** (text and email conversations), which comes from `twilio_ApptCount.textAndEmailConversation`, a table built once **nightly** from `DTContactHistory` by the procedure `Enterprise_twilio_appt_update`, so today's messages only show the next day.
- **Audience:** internal Quorum BDC staff; dealers will not have access to the rebuilt report (DR-2911).
- **BLUE version:** the card says "the BLUE version will be retired shortly after the new report goes live". What "BLUE" is was not explained.

## Mori AI in the report

- Mori AI answers inbound calls to the dealership's call center (see [../glossary.md](../glossary.md), "Mori and Mori Metrics"). The rebuilt report counts those calls next to the human ones.
- Per-call Mori data is already in Q Analytics (`dm.mori_call`, schema `dm`); the card says it comes from **Viova** `(inferred: the provider of the Mori call data)`.
- To decide whether a missed call or voicemail happened outside business hours, the report converts the call start from UTC to **Atlantic time** and compares it with the queue's hours for that weekday, and treats national and store holidays as outside hours. Mori calls must use the same rule (details in the task file).
- **Validation idea (from the cards):** take Mori out and the numbers must equal the old report.

## Where the data lives: dealer database or common?

This was Rick's original question. The Jira cards **do not say** which database each table is in. What the other documentation allows, all `(inferred)`:

- The **call and queue tables** (`twilio_*`, `net_*`, `BDC_*`) look like part of a **common/central DealerMine database**: the production DealerMine jobs read call tables (`twilio_*`) and a registry of dealer databases from one central database and then query each dealer database through linked servers (see "Patterns in the DealerMine production jobs" in [ingestion-pipeline.md](ingestion-pipeline.md)).
- `DTContactHistory` (source of EM Count) is a **per-dealer** table: it exists in the DealerMine test database Eduardo explored (see [dev-ingestion-practice.md](dev-ingestion-practice.md)). `dtcontacthistory_em` and `dtadditionalfields` are likely per-dealer too.
- `dm.mori_call` is already in the Q Analytics Postgres.

Why it matters: per-dealer tables need jobs that run for every dealer database; common tables are one source loaded once. To be confirmed with Patrick, Todd and the `dm-mono-repo`.

## How this connects to the rest of Q Analytics

- The DealerMine **ingestion practice** (aggregation at the source, upsert, incremental load) and the **test dashboard** in Superset Dev are the same kinds of work that the rebuild needs (see [dev-ingestion-practice.md](dev-ingestion-practice.md) and [superset-and-security.md](superset-and-security.md)).
- The architecture's 8 steps to integrate a product (Discover, Connect, Configure, Model, Visualize, Secure, Validate, Release; see [architecture.md](architecture.md)) map onto the stories `(inferred)`: DR-2908 is Discover, DR-2909 and DR-2910 are Connect, Configure and Model, DR-2911 is Visualize and Secure, and the baselines and Mori checks are Validate.
- The DM Colo test database does **not** contain the `twilio_*` tables (see [dev-ingestion-practice.md](dev-ingestion-practice.md)), so it may not be enough to reproduce the Agent Console.
- First page of the Q Analytics expansion to DealerMine products (see [overview.md](overview.md) and [../products/brands.md](../products/brands.md)).

## Gaps and open questions (technical)

- Which database holds each source table (dealer or common)? Is there a mapping in the `dm-mono-repo`?
- Where is the `.rpt` file and who can export its formulas? Does Eduardo have access to the `dm-mono-repo` (GitHub organization membership was pending, see [../onboarding/first-weeks.md](../onboarding/first-weeks.md))? Rules for using Claude Code on that repository still need confirming.
- What are "S1000" and the "BLUE" version?
- Who is Viova and what exactly does it provide? Time zone of `started_at`?
- Where can the rebuild be tested with `twilio_*` data (is there a test copy)?
- Which of the report's numbers come from nightly-only sources?
- What do the Crystal formulas do that the SQL does not (for example, goals, costs per call, voice drop costs)?
