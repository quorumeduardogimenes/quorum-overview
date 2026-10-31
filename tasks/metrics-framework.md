---
title: Metrics framework for Eduardo's work
summary: What to record for every task and project so that results can be measured and demonstrated in the future: field definitions, indicators and how they are calculated, and how the data is captured.
updated_at: 2026-10-30
sources: [Eduardo's idea on 2026-10-30 (document everything about the work he does, to have metrics and results to show in the future; no format or tool decided), Jira data model as read through the Atlassian connector on 2026-10-30, meeting with Rick, 2026-10-30 (../meetings/2026-10-30-rick-follow-up.md; sprint, Estabot points), general practice in agile delivery metrics (not from a Quorum source)]
status: draft
---

# Metrics framework for Eduardo's work

## In plain words

Think of a gym log: if you only write down "went to the gym", you cannot show progress; if you write date, exercise, weight and repetitions every time, a chart appears by itself months later. This file says **what to write down every time Eduardo works on something**, so that months from now there is real data to show how much was delivered, how fast, how well and with what business effect, instead of reconstructing it from memory.

## Principles

1. **Capture at the time.** Start dates, status changes, effort and validation results are lost if not recorded when they happen.
2. **Fact, estimate and target are different things.** Mark each number: measured, estimated (by whom) or target. Unknown is `n/k`, never a guess.
3. **Every number has a source:** a Jira key, a worklog, a dashboard, a pull request, a validation file. No source, no number.
4. **Outcomes, not only activity.** Leadership cares about what changed (a legacy report retired, data fresher, a team saved time), not only how many cards closed.
5. **No customer data in the repository.** Use counts, ranges and "a named dealer"; no names, credentials or addresses.
6. **Do not judge people with these numbers.** They show the health of the delivery and the value of the work; the team context (no QA, no product manager, ceremonies missing, see [../company/agile-process-and-jira.md](../company/agile-process-and-jira.md)) affects them.

## What is recorded per task

Fields live in the header of each task file and as a row in the [ledger](tasks-index.md). The body of each task has a "Status log" and a "Metrics" table.

| Group | Field | How to get it |
|---|---|---|
| Identity | key, type, stream, title, requester or business partner, parent epic | Jira |
| Dates | created, assigned, started (first real work), ready for acceptance, done (accepted) | Jira status history; status log |
| Size | story points (and who estimated: Estabot, refinement, Eduardo), re-estimates | Jira; refinement |
| Effort | hours actually spent | Jira worklogs per card (proposed) or Eduardo's own log |
| Quality | acceptance criteria total and met on the first attempt; validation checks run and result (for example "matches the baseline"); returns from Ready for Acceptance; defects after release | task file; Jira |
| Output | tables or sources ingested, jobs created, datasets, charts, dashboards delivered, dealers or stores covered | task file; Superset |
| Data outcome | data freshness (maximum age), refresh success rate, load time of the page | monitoring; Superset |
| Business outcome | audience and number of users, legacy report retired, time saved, cost avoided (only with a source) | stakeholder; usage logs |
| AI-assisted work | whether Claude was used and for what; time saved **as an estimate** | Eduardo |
| Links | Jira, repository, dashboard, documentation | all |

## Indicators for the roll-up

Calculated from the ledger; definitions are fixed so that numbers stay comparable over time.

| Indicator | Definition | Why leadership cares |
|---|---|---|
| Throughput | Stories done per sprint (and per month) | How much is delivered |
| Points delivered | Sum of story points of stories done per sprint | Delivery size, comparable with the team's velocity |
| Lead time | Created to done | How long a request waits and gets done |
| Cycle time | Started to done | How long work takes once started |
| Estimate accuracy | Actual hours / points, compared across stories | Whether plans can be trusted |
| First-pass acceptance rate | Stories accepted without returning from Ready for Acceptance, divided by stories accepted | Quality of delivery |
| Post-release defects | Bugs raised against a delivered task within 30 days (period `(inferred)`, to agree) | Quality in production |
| Validation match | Share of validation checks that matched the legacy baseline | Trust in rebuilt reports (central to DR-2907) |
| Data freshness compliance | Share of time that sources stay within their target age (for example 15 minutes) | Reliability of data products |
| Products moved toward coverage | Products moved from yellow or red to purple in Rick's diagram (see [../products/brands.md](../products/brands.md)) | Progress on the goal of covering all products |
| Legacy retirements | Old reports or processes retired because of a delivery (for example the BLUE version of the Agent Console) | Cost and maintenance removed |
| Support load | Support items closed per month, and time to resolve | Value of the support stream |
| Adoption | Distinct users or views of delivered reports, if usage data is available | Whether people use what is delivered |

Terms in [../glossary.md](../glossary.md): cycle time, lead time and throughput.

## How the data is captured

1. **While working:** keep the task file's header, "Status log" and "Metrics" table current (dates, effort, validation results). Log time in Jira against the card.
2. **At each documentation session:** read Jira through the Atlassian connector, update the task file and the [ledger](tasks-index.md), and cite the read date.
3. **At each release:** write the outcome (what changed, for whom) and the validation results, even if small.
4. **Monthly (proposed):** recompute the roll-up table in the ledger and keep a dated snapshot of it in the change log of [../index.md](../index.md).

## Purpose and scope

The only goal is to **document everything about the tasks Eduardo works on**, at the time they happen, so that metrics and results exist to be shown in the future. How or where they will be presented is **not decided** and is not part of this framework. Whether leadership wants such a view, and which numbers can be shared, is to be confirmed with Rick when the time comes.

## Gaps and open questions

- Does Quorum already track delivery metrics (velocity, sprint reports) for the data and reporting team? Where?
- **Pending (Eduardo will find out and document it):** will Eduardo be able to log time in Jira per card, and is time logging expected? Until then effort is `n/k`.
- Which period defines "post-release defect", and who confirms that a defect belongs to a delivered task?
- Is there usage data for delivered reports (who opened what) that could feed an adoption indicator?
- Where and how will the results be presented in the future (not decided)?
- Do the DR board's statuses allow a clean split of lead time and cycle time?
