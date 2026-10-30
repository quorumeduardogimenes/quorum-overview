---
title: Questions for Todd
summary: Official list of the still-open questions Eduardo will ask Todd, in English, plus the examples he wants to see in practice. Answered questions are removed.
updated_at: 2026-10-29
sources: [Eduardo's Claude Doc "Questions for Todd and Q Analytics study guide" (revision 16), deleted on 2026-10-27 at Eduardo's request after its content was copied to this file; Round 3 added on 2026-10-27 at Eduardo's request, from Todd's call and message of the same day; pruning of answered questions on 2026-10-28 at Eduardo's request, based on the exploration of dev and the ingestion tool's README; message to Todd and question answered by the job form, 2026-10-29 (Eduardo)]
status: draft
---

# Questions for Todd

This file is the **source** of the list: the original living document (a Claude Doc) was deleted at Eduardo's request on 2026-10-27. The questions stay in English because they will be sent to Todd (Microsoft Teams or meeting). To change them, follow section 7 of the [guide](../00-documentation-guide.md).

**List rule:** an answered question leaves this list. The **original round numbering was kept**, so there are numbers missing on purpose. What was removed and why is in [first-weeks.md](first-weeks.md), section "Follow-up questions for Todd". Technical context in [../q-analytics/ingestion-agent.md](../q-analytics/ingestion-agent.md), [../q-analytics/ingestion-pipeline.md](../q-analytics/ingestion-pipeline.md) and [../q-analytics/architecture.md](../q-analytics/architecture.md).

## Round 1

2. How is the Postgres warehouse organized? Which schemas hold the detail tables, the analytical models and the summary tables? Is it a star schema?
3. Which dashboards read summary tables and which read transaction-level data? Is there a report catalog or a list of datasets?
4. Are the exported Superset datasets and dashboards stored in Git, and who reviews them?
5. How is row-level security applied in practice? Which field identifies the dealership, and is the filter only in the Superset security manager, or also in Postgres?
6. Are the dashboards templated by product, with predefined dashboards for each product, or can customers have their own customized reports?

## Round 2

1. SQL Server as a destination database: what is the use case, which products need it, and when? (I can already see the SQL Server destination servers in Dev.)
2. Job and pipeline configuration is covered by the snapshots and GitHub. How are the Postgres data and the Superset configuration backed up? What is the disaster recovery plan?
3. Besides SQL Server change tracking, do the other sources (APIs, Postgres, MySQL) rely only on the last run variable of the job to pick up changes?
4. When a scheduled job fails, is there any alert, or do we have to check the Runtime Logs? How do we rerun it for a time window?
5. If a pg_cron refresh fails, how do we rerun it and how do we get alerted?
6. How are roles and permissions defined: in Descope, in Superset or in the dealership security table? Who maintains that table, and how is a new user added?

## Round 3 (arose after the 2026-10-27 call)

1. Is the configuration snapshot with GitHub export/apply the official way to promote a pipeline or job from Dev to Production, given that each client is registered with only one server? Who reviews it?
2. The Dev version is behind the firewall. How should I access it from Brazil? Is the data migration environment (the "DM Colo" client) the same as the Dev version?
3. In the client deployment process, step 1 says the client provides a username and password for their environment. Is that a database account or access to the dealership server?

## Suggested examples

It would help me a lot to see these in practice. Could you show them live, or record a short video?

- Could you show me one client (dealership) with its pipelines and jobs? Then, if a second dealership uses the same source system, is it enough to associate it with the existing job?
- Could you walk me through one simple dashboard going from Dev to Production: export, import and validation?
- Could you open the same dashboard with users from two different dealerships, so I can see how row-level security works?
- Could you pick one KPI and trace it back to its source: Superset dataset, Postgres table, pg_cron summary and source system?

## Already sent to Todd, unanswered (outside the rounds)

- **2026-10-29, message in English on Teams:** is there documentation of the DealerMine tables? Eduardo said he is building his own pipeline and jobs with the DM Colo client, listed tables he found interesting (`DTContactHistory`, `DTContactList`, `DTSalesProduct`, `DMVehicleDetail`, `ServiceHistMaster`) and explained that many of the 1790 tables are empty in the test environment. When Todd replies, update this section and [../q-analytics/dev-ingestion-practice.md](../q-analytics/dev-ingestion-practice.md).

## Gaps and open questions

- The idea of undoing job deletion via the X did not make it into the list (it is an improvement idea, see [../q-analytics/improvement-ideas.md](../q-analytics/improvement-ideas.md)). Include it if Eduardo asks.
- New questions from the practice that have not become questions yet: assignment of the pipeline to the client in the client panel (the first job will show it), "Unassigned" and the difference between 1776 and 108 jobs. New candidate: what the contact type `Turned Off` means, which accounts for about 75% of `DTContactHistory` in the test database. Eduardo decides whether they become a Round 4.
- The question "does the destination table have to exist before the job?" was **answered on 2026-10-29** by the job form itself ("Target tables must already exist") and left the list.
