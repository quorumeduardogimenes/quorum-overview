---
title: DR-2907 Agent Console rebuilt in QAnalytics, including Mori AI
summary: Task file for the Jira epic DR-2907 (and stories DR-2908 to DR-2911) assigned to Eduardo: origin, scope and acceptance criteria per story, estimates, sprint, status log, metrics captured so far and open planning questions.
updated_at: 2026-10-30
sources: [Jira epic DR-2907 and stories DR-2908 to DR-2911 (project "Data and Reporting"), read through the Atlassian connector on 2026-10-30 (https://quoruminfotech.atlassian.net/browse/DR-2907), meeting with Rick, 2026-10-30 (../meetings/2026-10-30-rick-follow-up.md), meeting with Rick, 2026-10-26 (../meetings/2026-10-26-rick-kickoff.md; task description pasted by Eduardo on 2026-10-29), Eduardo (2026-10-29 and 2026-10-30)]
status: draft
jira_key: DR-2907
jira_url: https://quoruminfotech.atlassian.net/browse/DR-2907
type: epic
stream: project
task_status: Awaiting Refinement
created: 2026-10-29
started: 2026-10-30
done: n/k
points_estimated: n/a (stories: 5, 5, 5, 8; per Rick, not checked in Jira)
effort_hours: n/k
---

# DR-2907 Agent Console rebuilt in QAnalytics, including Mori AI

How task files work: [tasks-index.md](tasks-index.md). What is measured and why: [metrics-framework.md](metrics-framework.md). What the report is and where its data lives: [../q-analytics/agent-console-report.md](../q-analytics/agent-console-report.md).

## In plain words

A BDC manager (the manager of a dealership's call and appointment center) watches one report all day to see how the team handles calls. That report, the **Agent Console**, lives in an old tool (Crystal Reports). Quorum now has an **AI that answers some of those calls (Mori)**. The epic rebuilds the report on the new stack (Postgres + Superset), adds the AI calls next to the human ones, keeps the data under 15 minutes old, and proves that the numbers still match the old report.

## Origin

- **Rick's brief (2026-10-26, relayed by Eduardo):** connect to the `dm-mono-repo`, analyze the Agent Console (how it is built, which data it uses, where that data lives: dealer database or common) and prepare to rebuild it as one of the first DealerMine pages in Q Analytics. **Patrick Manning** was to explain the report and send a story.
- **2026-10-29:** Patrick created the epic and four stories in Jira; the epic was assigned to Eduardo.
- **2026-10-30 (Rick):** "Agent Console Reboot in Q Analytics", a ground-up pipeline and visualization build with **Patrick as business partner**. Eduardo had already started exploring the report with Claude Code, prompting in Portuguese. See [meetings/2026-10-30-rick-follow-up.md](../meetings/2026-10-30-rick-follow-up.md).

## Jira structure

| Card | Type | Summary | Status (2026-10-30) | Owner | Points (Rick) |
|---|---|---|---|---|---|
| DR-2907 | Epic | Agent Console rebuilt in QAnalytics, including Mori AI | Awaiting Refinement | Eduardo | n/a |
| DR-2908 | Story | Agent Console spec and baselines | Awaiting Refinement | unassigned | 5 |
| DR-2909 | Story | Sync all Agent Console sources every 15 minutes | Awaiting Refinement | unassigned | 5 |
| DR-2910 | Story | Mori per-call data every 15 minutes | Awaiting Refinement | unassigned | 5 |
| DR-2911 | Story | Build the Agent Console report in QAnalytics, including Mori | Awaiting Refinement | unassigned | 8 `(inferred: the "final visualization story")` |

- **Estimates (Rick, 2026-10-30):** Estabot was run on the epic: stories of **5 points** and the final visualization story of **8**. Not checked in Jira. Estabot ran **before** Patrick and Eduardo discussed the epic; Eduardo is to tell Patrick.
- **Sprint (Rick):** two Agent Console stories are in the sprint that starts on Tuesday `(inferred: 2026-11-03)`; which two was not said.
- **Natural order `(inferred)`:** spec and baselines (DR-2908), then data (DR-2909, DR-2910), then the report (DR-2911), because the report is validated against the baselines and needs fresh, complete data.

## Epic acceptance criteria (DR-2907)

User story: as a BDC Manager, the main BDC report should include Mori AI and stay up to date during the day, to see human and AI call handling side by side. The BLUE version will be retired shortly after the new report goes live.

1. The Q Analytics report has **every Agent Console filter and column**, plus the new Mori columns.
2. **Columns not affected by Mori match Agent Console.** Mori-affected columns match once Mori is taken back out.
3. **Data is no more than 15 minutes old**, and the report **shows when it was last refreshed**.

## DR-2908: spec and baselines

- Document **every column**: name, tooltip and the formula exported from the `.rpt` (some logic exists only there).
- Document **every filter** with values and defaults: Summary/Detail, Date Grouping, Queue/Agent, DBName, Location, Team Leader, BDC Location, Within Shift, Suppress Groups, Low Answer Rates, Missed Objective, Include Inactive.
- List the **hardcoded values**: the **80%** answer-rate and productivity goals, **$2.25 per call**, **$0.21 voice drop**, **$0.35 Engage AI voice drop** (assumed US dollars `(inferred)`).
- Save **baseline exports** for **3 dealers x 3 date ranges** (one day, one week, one month), in **Summary and Detail**.

## DR-2909: sync every source every 15 minutes

Tables the card lists as needing to be synced "where they are missing" (Q Analytics already has `twilio_reporting`, `twilio_apptcount`, `twilio_calllog_archive`, `voicedropqueue` and `dtcontacthistory_em`):

- **Queue and team setup:** `twilio_QueueMaster` and `_Schedule`, `net_TwilioLoginUser` and `_Queue`, `twilio_sync_users`, `net_floater` and `_schedule`, `net_teamleader`, `twilio_callObjectives`, the `net_holiday_*` tables, `BDC_StoreHolidays`.
- **Activity:** `Twilio_CallLog_History`, `twilio_callLog_voiceMail`, `voiceDropHistory`, `appointment_booking_history`, `entBDCACSummary`, `twilio_serviceSold`, `twilio_QPScore`, `twilio_Reporting_Ratio_Data`, `bdc_punchInOut`, `BDC_Attendance`, `BDC_Reasons`.
- **Store eligibility:** `dm4dm_assets` (codes BDC-001, BDC-003, BDC-008) and `dtadditionalfields`.

**EM Count every 15 minutes, including the current day:** calculate it from `dtcontacthistory_em` (kept at most 15 minutes old) instead of the nightly `twilio_ApptCount`, with the **same rules** as the nightly procedure `Enterprise_twilio_appt_update`: unique customers per store, BDC user and day across text and email; reason `Text - %`, or `Email - %` with no campaign; the same excluded contact types; username `DMBDC%`. For completed days the result must match `twilio_ApptCount.textAndEmailConversation`.

**Every other source** loads at least every 15 minutes; a source that only updates nightly is documented as such. **An alert fires if any source is more than 30 minutes behind.**

## DR-2910: Mori per-call data

- `dm.mori_call` (already in Q Analytics; data from **Viova** per the card) must load at least every 15 minutes.
- **"Outside business hours" rule (same as Agent Console):** the call start is converted from UTC to **Atlantic time** (`CallStartBDCUserDateTime`) and compared with the queue's hours for that weekday (`twilio_queueMaster_schedule`, active rows only). On **national or store holidays** every call counts as outside hours. Mori calls use exactly the same rule.
- Confirm the time zone of Viova's `started_at` (it appears to be UTC).
- Flag each Mori call: **outside business hours**; **handled for more than 10 seconds**; **appointment booked** (through the store's **"Book As" user** in DealerMine).
- **Validate** per-call totals against Viova's **daily totals per store** and flag differences.
- **Mori calls are never also counted as Twilio inbound calls.**

## DR-2911: the report

- A Q Analytics **dataset and dashboard** replacing `team_model_reporting`, with Mori columns from the start, in **Summary and Detail**, with the same filters and defaults as the spec.
- **Internal Quorum BDC staff only; dealer users cannot access it** (see roles and RLS in [../q-analytics/superset-and-security.md](../q-analytics/superset-and-security.md)).
- Columns not affected by Mori match the baselines exactly, with **one intended change:** **V Drp = success + failed for every day in the range** (for a named dealer, one month, the expected value is 264).
- **Mori changes:**
  - **IN Offered** includes Mori calls handled over 10 seconds (IN Offered - Mori = Agent Console IN Offered).
  - New column **Mori** (between V Drop and Msd Calls): calls handled by Mori AI, including outside business hours.
  - New column **Mori OutSideHrs** (between V Lnk and M/VOutSideHrs): Mori calls outside business hours.
  - **IN Handled** (IN Offered - Msd Calls - V Msg) includes Mori (IN Handled - Mori = Agent Console IN Handled).
  - New column **AC Ans Rate** (right of Ans Rate 80%): [IN Handled - Mori] / [IN Offered - Mori - M/V Outside Hrs]; must equal Agent Console's current Ans Rate 80%.
  - Tooltip on **Ans Rate 80%** says it includes Mori.
  - New column **Mori %** (right of Mori): Mori / IN Offered.
  - A **"Mori AI" row** appears as if it were a DMBDC user: at most one per store/queue, only where Mori took calls; the Mori and Mori OutSideHrs totals equal the sum of these rows; **Appt Cnt** includes appointments booked through "Book As".
- **Example check:** for one named store on one day in September 2026, IN Offered goes from 27 to 35 with 8 Mori calls.

## Status log

| Date | Event | Source |
|---|---|---|
| 2026-10-26 | Rick describes the activity (analyze the Agent Console, prepare to rebuild it) | Rick kickoff |
| 2026-10-29 | Patrick creates DR-2907 and the four stories; epic assigned to Eduardo | Jira |
| 2026-10-30 | Eduardo starts exploring the Agent Console with Claude Code | Rick follow-up |
| 2026-10-30 | Rick runs Estabot on the epic (5, 5, 5, 8 points); two stories planned for the next sprint | Rick follow-up |
| 2026-10-30 | Patrick books a meeting with Eduardo (to confirm that it happened) | Rick follow-up |

Add a row at every status change, refinement, start of work, hand-off to acceptance and release. Dates are what feed [metrics-framework.md](metrics-framework.md).

## Metrics (captured so far)

| Metric | Value | Note |
|---|---|---|
| Stories | 4 (DR-2908 to DR-2911) | |
| Points estimated | 23 (5 + 5 + 5 + 8) | Estabot, per Rick; not checked in Jira |
| Created to start | 1 day (2026-10-29 to 2026-10-30) | Start = first exploration |
| Effort so far | n/k | Log time in Jira against each card |
| Acceptance criteria | epic: 3; DR-2908: 4; DR-2909: 4 (with sub-points); DR-2910: 8; DR-2911: 5 (with sub-points) | Count at acceptance how many were met on the first try |
| Baselines to produce | 3 dealers x 3 ranges x 2 views = 18 exports | DR-2908 |
| Source tables to sync | about 30 listed in DR-2909 | Count again after refinement |
| Freshness target | 15 minutes (alert at 30) | Measure the real value after release |
| Legacy report retired | not yet (BLUE version to be retired shortly after release) | Business outcome to record |

## Gaps and open questions (planning)

- Which two stories entered the sprint, and what do 5 and 8 points mean once Patrick and Eduardo refine the epic (Estabot ran before)?
- Is DR-2907 the same as "Michael's epic of new report stories" mentioned on 2026-10-26, or a separate one?
- Who refines the stories (no product manager; Rick helps in the interim) and who accepts them?
- Which dealers and date ranges are used for the 3 x 3 baselines?
- What are "S1000" and the "BLUE" version, and when will BLUE be retired?
- Who owns the 15-minute sync and the alert (more than 30 minutes behind) and how are alerts delivered?
- Does "no more than 15 minutes old" conflict with nightly-only sources that the card says to "document as such"?
- Access to the `dm-mono-repo` and the rules for using Claude Code on it (technical questions are in [../q-analytics/agent-console-report.md](../q-analytics/agent-console-report.md)).
