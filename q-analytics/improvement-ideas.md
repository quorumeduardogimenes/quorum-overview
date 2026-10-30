---
title: Improvement ideas for Q Analytics
summary: Informal backlog of ideas Eduardo can propose to improve Q Analytics and the ingestion tool, with the context and status of each.
updated_at: 2026-10-28
sources: [Eduardo, 2026-10-27 and 2026-10-28; call with Todd, 2026-10-27 (../meetings/2026-10-27-todd-ingestion-call.md)]
status: draft
---

# Improvement ideas for Q Analytics

An informal list, not Quorum's official backlog. Each idea has where it came from and what is still to be confirmed. Before proposing anything to the team, confirm with Todd that the idea makes sense and does not already exist.

| Idea | Origin | Status |
|---|---|---|
| **Undo job deletion via the X** in the ingestion tool | Todd warned that clicking the X can delete jobs, and that the current pipelines have no backup (call of 2026-10-27). Eduardo believes there is currently no way to undo it (not confirmed) and sees a chance to add value here | Idea. Still to confirm with Todd whether there is any way to recover |
| **See, in the "Destination Servers" tab, the active pipelines and jobs that use each destination server** | Eduardo, 2026-10-28, while exploring the dev version of the ingestion tool and trying to find out which destination server the jobs write to | Idea. Still to confirm with Todd whether this information already exists on another screen |

## Idea 1: undo job deletion

- **Problem:** one click on the X deletes the job and, according to Todd, the current incremental and historical pipelines have no backup. It is a risk for someone who is learning and for operations.
- **Possible paths (all `inferred`, nothing agreed):** ask for confirmation before deleting; a "trash bin" that keeps the deleted job for a while; take an automatic snapshot (the tool already has a GitHub integration for snapshots, see [ingestion-pipeline.md](ingestion-pipeline.md)) before each deletion.
- **Before proposing:** ask Todd whether recovery already exists (for example, restoring from Git) and whether there is interest. Do this after practicing in the tool, so as to speak from experience.

## Idea 2: see who uses each destination server

- **What it is:** on the tool's **Destination Servers** screen of the ingestion tool (the list of servers where the data is written), add an option to see **the active pipelines and jobs that use that server as a destination**.
- **Problem:** the list shows the destination servers but, according to Eduardo, does not show who writes to each one. To know whether a server is in use, you have to look pipeline by pipeline and job by job `(inferred, to be confirmed)`. This makes it harder to answer simple questions such as "where do this client's jobs write?" or "is anything active writing to this database?".
- **Why it helps:** it makes the impact clear before changing or removing a destination server, avoids confusing test servers with the ones actually in use and supports whoever is learning the tool. It matches the caution in idea 1: the more visible what depends on what, the lower the risk of deleting something by accident.
- **In plain words:** it is like a phone's contact list showing, on each contact, the conversations and appointments linked to it, instead of forcing you to search one by one.
- **Before proposing:** ask Todd whether this view already exists on another screen (for example, inside the pipeline or the job) and whether it makes sense for the tool.

## Gaps and open questions

- Does the ingestion tool already have confirmation or recovery for deletions? (Eduardo believes not)
- Does the relationship between destination servers and jobs already appear on any screen of the ingestion tool?
- Who decides and prioritizes improvements to the tool (Todd, Rick)?
