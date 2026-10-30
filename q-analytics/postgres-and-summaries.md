---
title: Postgres, read replica and summary tables
summary: How the Q Analytics Postgres is organized: primary and replica, schemas, detail, model and summary layers, nightly pg_cron jobs and watermarks.
updated_at: 2026-10-27
sources: [meeting with Todd, 2026-10-26 (../meetings/2026-10-26-todd-technical-tour.md), Todd's "Analytics Platform" deck (slide 8, sent on 2026-10-27)]
status: draft
---

# Postgres, read replica and summary tables

## In plain words

Imagine a library with millions of index cards (raw data, some tables reach ~425 GB). Looking for an answer card by card takes minutes. So, every night, a prepared routine checks only the new or changed cards and updates an already-summed "summary sheet". The dashboards read the summary sheet and answer in seconds. In addition, there are two copies of the database: one where you write and another read-only, used by the reports, so that reading never gets in the way of writing.

## Architecture

- **Primary (writable) + read-only replica.** All reports query the **replica** (Todd).
- Raw data arrives in **per-product schemas**, plus a **staging** schema (see [ingestion-pipeline.md](ingestion-pipeline.md)).

## Three data layers (deck, slide 8)

The deck separates detailed analytical storage from report-optimized models:

| Layer | What it is |
|---|---|
| Detail & history | Transformed source data, retained in warehouse structures that allow traceability and analytical depth |
| Analytical models | "Business-ready" tables that organize common or domain-specific dimensions, measures and reporting logic |
| Scheduled summary aggregation | Scheduled processing in the database that refreshes compact summary tables, so dashboards do not repeatedly scan the detailed transactional data |

The deck sums it up: PostgreSQL is both the durable warehouse and the compute layer, close to the data.

## Summary tables

- They exist for **performance**: some raw tables are up to about **425 GB**.
- Example given by Todd: loading a report in **2 minutes** versus **2 seconds** with a pre-aggregated table motivated the decision to pre-aggregate.
- For Quorum this is the "pre-aggregated" part Rick mentioned; the rest keeps transaction-level detail (see [overview.md](overview.md)).

## Nightly refresh with pg_cron

- **pg_cron jobs run every night** and aggregate the raw data into summary tables.
- **Incremental refresh:** only processes new or changed records, without re-aggregating everything.
- An **ETL watermark table** stores the state of the last run. The **service data** has its own watermark, used to **recover from failed refreshes**.

## Versioned code

- **All stored procedures are versioned in Git**; they can be deleted and recreated from the repository (Todd).

## Gaps and open questions

- What are the summary tables and which reports do they serve (catalog)?
- How do the "detail & history", "analytical models" and "summary" layers map onto the actual Postgres schemas?
- What time does the nightly run happen and how long does it take? What happens if it fails?
- How does the "service data" watermark differ from the general one, and why does only it have its own recovery?
- How much data does each per-product schema take up? Is there a retention/archiving policy for the large tables (~425 GB)?
- What does replication look like (streaming? typical lag) and is there high availability?
- In which repository are the stored procedures and how is deployment done?
- With Microsoft SQL Server as a new destination (deck), will the summary tables also be replicated there?
