---
title: Improvement ideas (global backlog)
summary: Single place for all improvement ideas and suggestions about Quorum's tools, processes and practices, grouped by area (Q Analytics, Jira and agile process, security and governance, metrics and reporting), each with origin, status and what to confirm before proposing it.
updated_at: 2026-10-30
sources: [Eduardo, 2026-10-27 and 2026-10-28 (Q Analytics ideas); call with Todd, 2026-10-27 (../meetings/2026-10-27-todd-ingestion-call.md); conversation with Sam, 2026-10-30 (meetings/2026-10-30-sam-jira-and-agile-process.md); decision by Eduardo on 2026-10-30 to centralize all suggestions in this global file, organized by area; Eduardo's idea of documenting all his work to have metrics later (clarified 2026-10-30)]
status: draft
---

# Improvement ideas (global backlog)

An informal list, **not Quorum's official backlog**. It is the only home for improvement ideas and suggestions, whatever the area. Other files must not keep their own lists; they link here by ID (for example `JIRA-1`).

## How to use this file (for agents)

- **Adding an idea:** put it in the section of its area (create a new `## <Area> suggestions` section if none fits), with the next free ID for that area, using the entry template below, and add a row to the summary table. Update `updated_at`, `sources` and the change log in [index.md](index.md).
- **Origin matters.** Always say who raised the idea and whether it was **stated** by that person or **`(inferred)`** by whoever documented it. An inferred idea is a suggestion for Eduardo to consider, not something Sam, Todd or Rick proposed.
- **Never** record secrets, credentials, addresses or IPs in an entry (principle 9 of the [guide](documentation-guide.md)).
- **Status values:** `idea` (not yet checked with anyone), `to-confirm` (a question to a person is pending), `proposed` (raised with the team), `accepted`, `done`, `dropped`. Move an idea by editing its status and recording the date; do not delete it.
- **Before proposing** anything to the team, confirm with the person named in the entry that the idea makes sense and does not already exist.

Entry template:

```
### <ID>: <short title>
- **Area / Origin / Status:** ...
- **Problem:** what hurts today
- **Possible path:** how it could be done (mark guesses `(inferred)`)
- **Before proposing:** who to ask and what to confirm
```

## Summary

| ID | Area | Idea | Origin | Status |
|---|---|---|---|---|
| QA-1 | Q Analytics | Undo job deletion via the X in the ingestion tool | Eduardo, from Todd's warning (2026-10-27) | idea; confirm with Todd whether recovery exists |
| QA-2 | Q Analytics | See, in the "Destination Servers" tab, the pipelines and jobs that use each server | Eduardo (2026-10-28) | idea; confirm with Todd whether the view exists |
| JIRA-1 | Jira and agile process | Notify when an item enters "Ready for Acceptance" | `(inferred)` from Sam's description (2026-10-30) | idea |
| JIRA-2 | Jira and agile process | One-time cleanup of the stale product backlog | `(inferred)` from Sam's description (2026-10-30) | idea |
| JIRA-3 | Jira and agile process | A single, written intake path for requests | `(inferred)` from Sam's description (2026-10-30) | idea |
| JIRA-4 | Jira and agile process | A light replacement for the missing ceremonies | `(inferred)` from Sam's description (2026-10-30) | idea |
| SEC-1 | Security and governance | Automated Claude skill that scans repositories for exposed secrets | **Sam, stated** (2026-10-30) | idea; owner and plan unknown |
| SEC-2 | Security and governance | Written rules on secrets, credentials and repository hygiene, with an owner | `(inferred)` from Sam's description (2026-10-30) | idea |
| METRICS-1 | Metrics and reporting | Document all work (dates, effort, outcomes) to have metrics and results to show later | **Eduardo, stated** (2026-10-30) | idea; confirm with Rick |

Renumbering note: before 2026-10-30 these lived in `q-analytics/improvement-ideas.md` as "Idea 1" (now QA-1) and "Idea 2" (now QA-2).

## Q Analytics suggestions

Context: [q-analytics/ingestion-pipeline.md](q-analytics/ingestion-pipeline.md), [q-analytics/ingestion-agent.md](q-analytics/ingestion-agent.md).

### QA-1: Undo job deletion
- **Area / Origin / Status:** Q Analytics (ingestion tool). Origin: Todd warned that clicking the X can delete jobs and that the current pipelines have no backup (call of 2026-10-27); Eduardo believes there is currently no way to undo it (not confirmed) and sees a chance to add value. Status: `idea`.
- **Problem:** one click on the X deletes the job and, according to Todd, the current incremental and historical pipelines have no backup. It is a risk for someone who is learning and for operations.
- **Possible path (all `(inferred)`, nothing agreed):** ask for confirmation before deleting; a "trash bin" that keeps the deleted job for a while; an automatic snapshot (the tool already has a GitHub integration for snapshots, see [q-analytics/ingestion-pipeline.md](q-analytics/ingestion-pipeline.md)) before each deletion.
- **Before proposing:** ask Todd whether recovery already exists (for example, restoring from Git) and whether there is interest. Do this after practicing in the tool, so as to speak from experience.

### QA-2: See who uses each destination server
- **Area / Origin / Status:** Q Analytics (ingestion tool). Origin: Eduardo, 2026-10-28, while exploring the dev version of the tool and trying to find out which destination server the jobs write to. Status: `idea`.
- **Problem:** the **Destination Servers** screen lists the servers where data is written but, according to Eduardo, does not show who writes to each one. To know whether a server is in use, you have to look pipeline by pipeline and job by job `(inferred, to be confirmed)`. This makes simple questions hard ("where do this client's jobs write?", "is anything active writing to this database?").
- **Why it helps:** the impact is clear before changing or removing a destination server, test servers are not confused with those in use, and it supports whoever is learning the tool. It matches QA-1: the more visible what depends on what, the lower the risk of deleting something by accident.
- **In plain words:** like a phone's contact list showing, on each contact, the conversations and appointments linked to it, instead of forcing you to search one by one.
- **Possible path:** on that screen, an option to see the active pipelines and jobs that use the server as a destination.
- **Before proposing:** ask Todd whether this view already exists on another screen (for example, inside the pipeline or the job) and whether it makes sense for the tool.

## Jira and agile process suggestions

Context: [company/agile-process-and-jira.md](company/agile-process-and-jira.md). **All four entries below are `(inferred)` by the documenting agent from Sam's description of 2026-10-30; Sam did not propose them.**

### JIRA-1: Notify when an item enters "Ready for Acceptance"
- **Area / Origin / Status:** Jira and agile process. Origin: `(inferred)`. Status: `idea`.
- **Problem:** items sit in Ready for Acceptance with no notification until someone moves them to Done (Sam), so the acceptance flow is broken.
- **Possible path `(inferred)`:** an automatic notification to the requester when an item reaches that status.
- **Before proposing:** ask Sam or Rick who accepts items today and whether a Jira automation is allowed.

### JIRA-2: Clean up the stale product backlog
- **Area / Origin / Status:** Jira and agile process. Origin: `(inferred)`. Status: `idea`.
- **Problem:** much of the product backlog is stale or obsolete (sprint 116) and nobody has time to clean it (Sam).
- **Possible path `(inferred)`:** a one-time cleanup, starting with the items Sam already named as deprecated (ACA pipeline credentials, Synapse references).
- **Before proposing:** ask Sam or Rick who may close or archive items and whether the Jira PBI Creator skill should be involved.

### JIRA-3: A single, written intake path for requests
- **Area / Origin / Status:** Jira and agile process. Origin: `(inferred)`. Status: `idea`.
- **Problem:** requests sometimes bypass product and go straight to dev; product support often does not funnel feature requests and bugs through product (Sam).
- **Possible path `(inferred)`:** a short written rule saying where each kind of request goes and who triages it.
- **Before proposing:** ask Rick (product) who should own the rule and whether product support agrees.

### JIRA-4: A light replacement for the missing ceremonies
- **Area / Origin / Status:** Jira and agile process. Origin: `(inferred)`. Status: `idea`.
- **Problem:** sprint planning, retrospectives, dailies and refinement no longer happen; meetings are ad hoc (Sam).
- **Possible path `(inferred)`:** for example a short regular refinement slot, only if the team wants one.
- **Before proposing:** ask Sam and Rick whether the lack of ceremonies is a conscious choice (see the gaps in [company/agile-process-and-jira.md](company/agile-process-and-jira.md)).

## Security and governance suggestions

Context: [company/governance-and-security-practices.md](company/governance-and-security-practices.md).

### SEC-1: Automated secret-scanning skill
- **Area / Origin / Status:** Security and governance. Origin: **Sam, stated** (2026-10-30): an automated Claude skill should scan repositories for exposed secrets as a baseline; READMEs and `.gitignore` files also need ongoing maintenance. Status: `idea`.
- **Problem:** SOC 2 is a goal but repository scanning for secrets and credentials is not in place.
- **Possible path:** the skill Sam described; its scope and owner were not discussed.
- **Before proposing:** ask Sam and Todd (internal infrastructure, cybersecurity) whether it is already planned.

### SEC-2: Written rules on secrets and repository hygiene
- **Area / Origin / Status:** Security and governance. Origin: `(inferred)`. Status: `idea`.
- **Problem:** there is no firm stance and no written rules on secrets, credentials or repository hygiene (Sam).
- **Possible path `(inferred)`:** a short written policy with a named owner, probably with Todd's area `(inferred)`, which also feeds the SOC 2 effort.
- **Before proposing:** ask Todd and Rick who owns policy and what the SOC 2 timeline is.

## Metrics and reporting suggestions

Context: [tasks/metrics-framework.md](tasks/metrics-framework.md) and [tasks/tasks-index.md](tasks/tasks-index.md).

### METRICS-1: Document all work to have metrics and results to show later
- **Area / Origin / Status:** Metrics and reporting. Origin: **Eduardo, stated** (2026-10-30): document everything about the tasks and projects he works on, to have metrics and results to demonstrate in the future. Eduardo clarified that no dashboard or tool is planned; the earlier mention of Superset was only the agent's speculation. Status: `idea`.
- **Problem:** the results of the data and reporting work (reports delivered, data freshness, legacy reports retired, support resolved) are hard to demonstrate later if the data was not recorded at the time.
- **Possible path:** record dates, estimates, effort, validation results and outcomes from now on in the task files and in the ledger of `tasks/`; the indicators are in the metrics framework. How the results are presented is not decided.
- **Before proposing:** ask Rick whether leadership wants to see results of this kind; check what Jira already provides (worklogs, sprint reports). Time logging per card is pending (Eduardo will find out).

## Gaps and open questions

- Eduardo's own early notes and improvement ideas, reviewed with Sam on 2026-10-30, are not recorded here yet: add them (with area and origin) after the review.
- Does the ingestion tool already have confirmation or recovery for deletions? (Eduardo believes not.) Does the relationship between destination servers and jobs already appear on any screen?
- Who decides and prioritizes improvements: Todd for the ingestion tool, Rick and Sam for the process? Is there an official backlog these ideas should be moved into?
