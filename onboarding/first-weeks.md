---
title: Eduardo's first-weeks plan
summary: Schedule, action checklist and status of the onboarding follow-up questions, agreed with Rick, Todd and Sam.
updated_at: 2026-10-29
sources: [meeting with Rick, 2026-10-26 (../meetings/2026-10-26-rick-kickoff.md), meeting with Todd, 2026-10-26 (../meetings/2026-10-26-todd-technical-tour.md), call with Todd, 2026-10-27, and his Teams message of the same day (../meetings/2026-10-27-todd-ingestion-call.md), Todd's deck (sent on 2026-10-27), Todd's Word document about the agent (2026-10-27), conversation with Sam, 2026-10-29 (../meetings/2026-10-29-sam-business-rules.md), Eduardo (diagram received, Todd's deck and Word document shared, accesses received, clarifications, 2026-10-27; connection to the test Postgres, Todd's guidance, exploration and object creation in dev, README and screenshots of the ingestion tool, 2026-10-28; exploration of the source database, production jobs, message to Todd, first job completed and test dashboard in Superset Dev, 2026-10-29)]
status: draft
---

# Eduardo's first-weeks plan

## Schedule

| When | What | With whom | Duration | Status |
|---|---|---|---|---|
| 2026-10-26 | 1-on-1 to walk through the architecture | Todd | n/s | done (see [meetings/2026-10-26-todd-technical-tour.md](../meetings/2026-10-26-todd-technical-tour.md)) |
| 2026-10-27 | Message and call about the test environment and creating ingestion flows | Todd | n/s | done (see [meetings/2026-10-27-todd-ingestion-call.md](../meetings/2026-10-27-todd-ingestion-call.md)) |
| 2026-10-29 | Conversation about the dealership business, DMS, history of the reports and culture; DMS demonstration | Sam | about 2 h | done (see [meetings/2026-10-29-sam-business-rules.md](../meetings/2026-10-29-sam-business-rules.md)); screens and access will be documented with screenshots |
| Week of 2026-10-26 | Shadowing on the existing reports | Sam | 3 to 4 hours | in progress: the 2026-10-29 conversation may have been part of it; Sam said Eduardo can follow him while he works (to be arranged later the same day) |
| 2026-10-28 (Wednesday, to be confirmed) | Check-in | Rick | 30 min | Rick is to schedule |
| Week of 2026-10-26 | Receive diagram and meeting notes | Rick | n/a | diagram received on 2026-10-27 (see [products/brands.md](../products/brands.md)); written notes: not confirmed |
| Week of 2026-11-02 | Start the epic of new report stories | Michael (epic owner) | n/a | pending |

Context: Sam moves to the support team in the week of 2026-11-02 (Rick), so the shadowing needs to happen this week.

## Actions from the meeting with Todd (2026-10-26)

| Action | Who | Status |
|---|---|---|
| Create a new GitHub account with the quoruminfotech.com email and send the ID to Todd on Teams | Eduardo | pending (to be confirmed) |
| Add Eduardo to Quorum's GitHub organization | Todd | depends on the action above |
| Granola invitation and meeting transcript | Todd | already shared, according to Eduardo |
| High-level architecture PowerPoint | Todd | received and transcribed in [q-analytics/architecture.md](../q-analytics/architecture.md) (2026-10-27) |
| Point to the QAnalytics repositories and READMEs of the ingestion tool | Todd | Eduardo has already seen the tool's README (2026-10-28); summary in [q-analytics/ingestion-pipeline.md](../q-analytics/ingestion-pipeline.md) |
| Generate a Superset README if the documentation is scarce | Todd | to be confirmed |

## Actions from the call with Todd (2026-10-27)

| Action | Who | Status |
|---|---|---|
| Send the requested accesses | Todd | sent, according to Eduardo |
| Send the Word document about the ingestion agent process | Todd | received; summarized in [q-analytics/ingestion-agent.md](../q-analytics/ingestion-agent.md) |
| Send Postgres credentials and access to the test server (to connect through pgAdmin 4) | Todd | **done on 2026-10-28:** Todd sent the correct pgAdmin web URL and Eduardo created the `test db` connection (see [q-analytics/postgres-test-environment.md](../q-analytics/postgres-test-environment.md)) |
| Explore the tool and the repository first, then compile the remaining questions and send them through Microsoft Teams | Eduardo | in progress: the dev tool was explored and the README read on 2026-10-28; the question list was pruned; on 2026-10-29 Eduardo sent Todd the first message (documentation of the DealerMine tables), still unanswered |
| Create a personal pipeline and jobs in the data migration environment, using the DealerMine MSSQL test database to practice SQL and get to know the tool | Eduardo | **first job completed (2026-10-29):** pipeline "DealerMine Test Edu" on the DM Colo client in dev; source database explored read-only; table `test_edu.dt_contact_daily` created; the contacts-by-day, type and direction job ran without errors (675 rows, 12,344 contacts, checked against the source) and the upsert was confirmed. Next jobs: to be defined (see [q-analytics/dev-ingestion-practice.md](../q-analytics/dev-ingestion-practice.md)) |
| Create his own schema in the `datamigration` database and his own job pipeline, to separate Eduardo's work from Todd's (Todd's suggestion, 2026-10-28) | Eduardo | **done on 2026-10-28:** schema `test_edu`, destination server DMColoPGTestEdu and pipeline created |
| Create a test dataset, chart and dashboard in Superset Dev, from the loaded table | Eduardo | **version 1 done on 2026-10-29:** dataset on `test_edu.dt_contact_daily`, 10 charts and a dashboard with contact type and period filters, names in English (see [q-analytics/superset-and-security.md](../q-analytics/superset-and-security.md)) |
| Test dashboard v2: replace the Big Numbers with Handlebars cards with comparison against the previous month (percentage or percentage points), default period = current month, with a new modeling view in `test_edu` | Eduardo | **planned for 2026-10-30** (plan in [q-analytics/superset-and-security.md](../q-analytics/superset-and-security.md)); only new, personal objects, never change existing ones |
| Provide a destination table in SQL Server, if needed | Todd | only if Eduardo asks |

## Actions from the conversation with Sam (2026-10-29)

| Action | Who | Status |
|---|---|---|
| Help Eduardo get access to the DMS test server (identified as ZZZ509, via "QIS Connector" / colocation) | Sam | pending |
| Formally request access to the DMS test environment from Todd or the help desk | Eduardo | pending |
| Talk to Sam again later the same day; possible shadowing while he works | Eduardo and Sam | to be arranged |
| Send the DMS screenshots (screens and how access is done) to document [products/quorum-dms.md](../products/quorum-dms.md) | Eduardo | pending (second moment) |

**Caution agreed with Sam:** the DMS test server is safe to explore; **do not connect to named customer environments**.

**Cautions agreed with Todd:** clicking the X in the ingestion tool can delete jobs; do not modify existing pipelines and jobs, not even in dev and even as an admin (the incrementals and historicals have no backup); practice only in a test environment (the dev version that Todd recommends, which sits behind the firewall, or the data migration environment); in the test Postgres, work only in `datamigration`, in your own schema. Credentials, IPs, addresses, users and database codes never go into this documentation. The DealerMine test database has real customer data: no data rows are recorded.

## Follow-up questions for Todd

The list of questions **still open**, in English and in three rounds, is in [questions-for-todd.md](questions-for-todd.md), which is the current source. Answered questions are removed from there; the original numbering is kept. This table keeps the history (status as of 2026-10-29; the numbers are those of each round):

| Question (summary) | Status |
|---|---|
| Round 1, 1: is there a Dev of the ingestion tool and what is the practical process to register a new flow | **Answered and removed from the list (2026-10-28).** There are dev versions in containers on the internal network, behind the firewall (Todd). Eduardo explored dev, read the README and created schema, destination server and pipeline; on 2026-10-29 he created and ran the first job successfully. The step-by-step (fields, form, upsert, scheduling, manual run) is in [q-analytics/ingestion-pipeline.md](../q-analytics/ingestion-pipeline.md) and [q-analytics/dev-ingestion-practice.md](../q-analytics/dev-ingestion-practice.md). Whatever is left from the practice becomes a new question |
| Round 1, 2 to 6 (Postgres organization, dashboards vs summaries, Superset Git, RLS in practice, dashboards per product) | Open, no answer in the new sources |
| Round 2, 1: SQL Server as a destination | **Partial, kept:** it is available; in dev there are SQL Server destination servers and a job that writes to both destinations (probably Todd's test, inferred); the use case, products and timeline are missing |
| Round 2, 2: backup and disaster recovery | **Partial, kept and narrowed:** the configuration of jobs and pipelines has snapshots and GitHub (README); missing are the Postgres data backup, the Superset one and the recovery plan |
| Round 2, 3: change tracking and other sources | **Partial, kept and narrowed:** SQL Server uses change tracking (`VIEW CHANGE TRACKING` permission); the README only shows the last-run variable (`last_run_var`) for the rest; need to confirm whether that is all |
| Round 2, 4: job failure and reprocessing | **Partial, kept and narrowed:** the tool has Runtime Logs, Data Transfer History and the Run button to run again (README); missing is whether there are alerts and how to reprocess a time window |
| Round 2, 5: pg_cron failure | Open |
| Round 2, 6: roles and permissions | Open |
| Round 3, 1: promotion from test to production | **Partial, kept and narrowed:** the README describes configuration snapshots with export, apply and restore via GitHub, a likely path `(inferred)`; need to confirm it is the official process and who reviews. Eduardo will not test promotion |
| Round 3, 2: access to dev behind the firewall and whether it is the same as the data migration environment | **Partial, kept:** Eduardo already accesses dev (the means was not recorded) and "DM Colo" appears in it, a sign that they are the same environment (inferred). Todd still needs to confirm |
| Round 3, 3: step 1 of the deployment (client username and password) | Open |
| Eduardo's question to Todd about whether "test db" and "data migration db" are the same thing and whether DM Colo writes to it (outside the rounds) | **Partial answer from Todd:** `datamigration` is in the test environment and the other databases are his tests. In dev, DM Colo currently does not write to `datamigration` (except through Eduardo's job, which now writes there) |
| Eduardo's message to Todd on 2026-10-29 (outside the rounds): is there documentation of the DealerMine tables? | **Sent, unanswered.** See [questions-for-todd.md](questions-for-todd.md) |

**New questions, with no round number** (arose on 2026-10-28 and 2026-10-29; Eduardo will test them in practice before bringing them to Todd; only those that remain become a Round 4, if he wants):

- Did the "DealerMine Test Edu" pipeline need to be assigned to the DM Colo client in the client panel? The job ran without errors, but the step was not recorded; still open.
- What are the "Unassigned" jobs and how do they relate to "runs for all clients"?
- Why does a screenshot show 1776 jobs if Todd said 108?
- What does the contact type `Turned Off` mean, which accounts for about 75% of `DTContactHistory` in the test database (9,302 of 12,344 contacts)?
- For the dashboard: is there a visual standard for Quorum's dashboards (font, colors, size of the numbers)? And does Quorum's Superset Handlebars accept custom HTML/CSS, or does sanitization remove it? (see [q-analytics/superset-and-security.md](../q-analytics/superset-and-security.md))

**Answered and removed from this list:** which column the upsert uses as the key (README, 2026-10-28: the job's conflict column, which must match a PRIMARY KEY or UNIQUE of the destination); how to run a job manually (README, 2026-10-28: Run button, with confirmation; used in practice on 2026-10-29); and whether the destination table must exist before the job (job form, 2026-10-29: yes, "Target tables must already exist"; confirmed when creating the table before the first job).

The idea of undoing deletions made with the X and the idea of seeing, in the Destination Servers tab, the pipelines and jobs that use each server are in [q-analytics/improvement-ideas.md](../q-analytics/improvement-ideas.md) and did not make it into the question list.

## Preparation suggestions (inferred, not agreed)

- In the shadowing with Sam, prioritize what only he knows: the business rules behind the reports and the history of modeling decisions. The business foundation is already in [business/dealership-domain.md](../business/dealership-domain.md); at the next meeting, go deeper into the DMS table map and the catalog of existing reports.
- Re-read the [glossary](../glossary.md) before opening the repositories and the ingestion tool (agent, container, pgAdmin 4, MSSQL, destination server, Query Executor, WO/RO, F&I) and, for Superset, the new terms (weighted average, MTD, native filter, view).
- Write down each step taken and the screens when creating the personal pipeline, to turn it into a documented step-by-step (it started in [q-analytics/dev-ingestion-practice.md](../q-analytics/dev-ingestion-practice.md)).
- Ask Sam whether there is a visual standard for the Q Analytics dashboards before investing more in the look of the test dashboard (inferred).

## Gaps and open questions

- Accesses and environments Eduardo already has, as of 2026-10-28: production ingestion tool (no admin) and dev (with admin, according to him); pgAdmin 4 web and test Postgres; Superset Dev (admin). Still to record: repository access, confirming the read-only connection to production Postgres, and getting access to the DMS test environment (never record the access values here).
- Deadline and priorities of Michael's epic.
- Rick's expectation for the end of the first two weeks.
- Does Sam remain the reference for the reports after he moves to support? When exactly was the shadowing agreed for?
