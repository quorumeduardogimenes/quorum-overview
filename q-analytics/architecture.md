---
title: Q Analytics, architecture
summary: End-to-end technical view of Q Analytics: where the data comes from, how it enters, where it lives, how it becomes dashboards, shared services, where everything runs (including test environments) and a transcription of Todd's architecture deck.
updated_at: 2026-10-28
sources: [meeting with Todd, 2026-10-26 (../meetings/2026-10-26-todd-technical-tour.md), call with Todd, 2026-10-27, and his Teams message of the same day (../meetings/2026-10-27-todd-ingestion-call.md), Todd's "Analytics Platform" deck (QAnalytics.pptx, slides dated 2026-10-13/14, sent by Eduardo on 2026-10-27, transcribed here), meeting with Rick, 2026-10-26 (../meetings/2026-10-26-rick-kickoff.md), Eduardo (clarifications, 2026-10-27; exploration of the dev version and the test Postgres, 2026-10-28)]
status: draft
---

# Q Analytics, architecture

## In plain words

Think of a report factory. At each dealership (and in some of Quorum's own systems) there is a database with the day-to-day data. A program moves that data, from time to time, to a big central warehouse (Postgres), tidying it up along the way. In the warehouse, nightly routines prepare ready-made summaries, so that querying is fast. At the front, Superset draws the dashboards, and each dealership only sees its own numbers. All of this runs on Quorum's own servers, in a data center in Calgary.

The central point of Todd's deck: this was designed as a **reusable platform**. You build it once and each new product only brings its sources, rules, models and dashboards.

Technical terms in [glossary.md](../glossary.md). The ingestion step by step, end to end, is in [ingestion-agent.md](ingestion-agent.md).

## Data flow (deck, slide 4)

The deck describes six stages, plus a block of shared services:

| Stage | Name in the deck | Content |
|---|---|---|
| 1 | Source systems | Private databases, cloud databases, SaaS and product APIs |
| 2 | Ingestion | Secure connectors, API endpoints, custom configurations |
| 3 | Transformation | Mapping and dates, functions, insert/upsert |
| 4 | PostgreSQL | Detail warehouse, analytical models, summary tables |
| 5 | Superset | Datasets, charts, dashboards |
| 6 | Customers | Identity, roles, data scope |

**Reusable platform services:** authentication, scheduling, monitoring, tenant/customer context and administration.

```
Sources ──► Ingestion + Transformation ──► Primary Postgres ──► Replica Postgres ──► Superset ──► Customers
(databases, (clients > pipelines >         (write)              (read-only)          (live        (identity,
 APIs)       jobs, via WebSockets)                                                    queries)     roles, RLS)

Shared services: authentication (Descope) · scheduling · monitoring · customer context · administration
```

The structure comes from the deck; "primary/replica" and "WebSockets" come from the meeting with Todd. The text diagram is a representation made by whoever documented it (inferred in form).

## Components

| Layer | Component | Detail | File |
|---|---|---|---|
| Connectivity | Database and API connectors; agent installed at the source | Databases behind a firewall, several combined API endpoints, custom-configured endpoints | [ingestion-pipeline.md](ingestion-pipeline.md), [ingestion-agent.md](ingestion-agent.md) |
| Transformation and orchestration | Configurable pipeline engine | Mapping, dates, functions, insert, upsert, conflict resolution, scheduling | [ingestion-pipeline.md](ingestion-pipeline.md) |
| Storage | Primary Postgres + read replica | Detail and history, analytical models, summary tables refreshed by pg_cron | [postgres-and-summaries.md](postgres-and-summaries.md) |
| Visualization | Apache Superset | Datasets, metrics, charts, dashboards; live queries, RLS, customizations, Redis cache | [superset-and-security.md](superset-and-security.md) |
| Identity | Descope (OAuth) | SSO; today only Q Analytics uses it | [superset-and-security.md](superset-and-security.md) |
| Support | GitHub | Jobs and pipelines managed via Git and backup/restore; versioned stored procedures | [ingestion-pipeline.md](ingestion-pipeline.md), [postgres-and-summaries.md](postgres-and-summaries.md) |

## Platform vs. product (deck, slides 3 and 12)

- **Shared platform ("one platform"):** connectivity, transformation, orchestration, warehouse, visualization, security and operations.
- **Each product ("many products")** supplies its data sources, rules, models and analytics experience.
- Deck motto: build the platform once and then onboard products, customers and datasets. This matches the goal of bringing all products into Q Analytics ([products/brands.md](../products/brands.md)).

## How to onboard a new product (deck, slide 11)

Eight repeatable steps, from discovery to production:

1. **Discover:** identify source systems and desired analytical outcomes.
2. **Connect:** establish database or API connectivity.
3. **Configure:** define endpoints, mappings, jobs and schedules.
4. **Model:** create warehouse tables, logic and summaries.
5. **Visualize:** build datasets, charts and dashboards in Superset.
6. **Secure:** apply roles, customer context and data scope.
7. **Validate:** reconcile values, performance and access.
8. **Release:** publish, monitor and operate.

## Where it runs

- **Self-hosted in Quorum's colocation data center in Calgary** (Todd, meeting). Colocation: Quorum has its own servers in space rented from a data center. (General definition, see glossary.)
- Compute and storage cost described as "close to zero" compared with the previous environment (Todd), which matches "now only server costs" (Rick). Details in [powerbi-to-superset-migration.md](powerbi-to-superset-migration.md).
- **Superset** environments: **Dev** (build and test) and **Production**; promotion is done by export and import (see [superset-and-security.md](superset-and-security.md)). On 2026-10-28 Eduardo is an admin of Superset Dev, and Todd has already created there the connection to the `datamigration` database of the test Postgres.

## Ingestion tool environments (Todd, 2026-10-27)

There is a development environment for the ingestion tool (not everything is done directly in production), but testing is more laborious than in a Superset Dev:

| Environment | What it is |
|---|---|
| Dev versions | Todd keeps dev versions of the app running in a container on the internal network, where he tests changes. They sit behind the firewall. According to him, it is the best place for anyone who wants to experiment (Teams message). On 2026-10-28 Eduardo accesses dev (with admin, according to him) and sees the "DM Colo" client connected in it, which indicates that dev and the data migration environment are the same environment `(inferred, to be confirmed)` |
| Test server | A single client running in a container on the same server (call). It is probably the dev version above `(inferred)`. Its Postgres has been accessible to Eduardo since 2026-10-28 (see [postgres-test-environment.md](postgres-test-environment.md)) |
| Data migration environment | Has the "DM Colo" client, linked to a DealerMine MSSQL test database with real exported data. It is the sandbox indicated to Eduardo. Its existing jobs write to Todd's test destinations (SQL Server and `babelfish_db`); the destination of Eduardo's practice is the `datamigration` database, `test_edu` schema |
| Production | The production clients connect back to **a single server**; for that reason, for some jobs, testing requires deploying new clients or recreating the environment. Security features prevent clients from other environments from connecting |

More details, and what Eduardo should do in them, in [ingestion-agent.md](ingestion-agent.md) and [dev-ingestion-practice.md](dev-ingestion-practice.md).

## Scale (cited numbers)

- **256 dealership databases** managed by the ingestion tool (Todd, technical reference). Rick's "about 250 dealerships" is an approximation of the same universe.
- **108 jobs** in total, between incremental and production (call of 2026-10-27, total confirmed by Eduardo).
- Raw tables of up to about **425 GB** (Todd).

## Backup (the little that is known)

- Call of 2026-10-27: the **database is backed up**, but jobs can be added or deleted; the existing incremental and historical pipelines **have no backup**; changes to jobs and clients are **managed via Git**; Postgres stored procedures are also in Git (meeting of 2026-10-26).
- It is not known which database the "backup" covers, nor the disaster recovery policy for Postgres and Superset. See gaps.

## Evolution in progress (deck, slide 13)

Ingestion is **evolving for new use cases, including adding Microsoft SQL Server as a destination database** (today the main destination is Postgres). The deck gives no deadline or reason. In the call of 2026-10-27, Todd said the **SQL Server destination is already available** and that he provides a table if Eduardo needs it. In dev, DM Colo's SQL Server destination servers and the test job that writes to both destinations suggest Todd is testing this case `(inferred)`.

## Deck transcription

The deck (14 slides, "QUORUM - CONFIDENTIAL", title "Analytics Platform", subtitle "A reusable architecture for data ingestion, transformation, warehousing and visualization", October 2026) is not archived in the project (see guide); this is the content per slide, with where each was documented:

| Slide | Title | Content | Documented in |
|---|---|---|---|
| 1 | Analytics Platform | Cover: Connect, Transform, Warehouse, Visualize; "repeatable foundation for deploying analytics across multiple products" | this file |
| 2 | Agenda overview | Platform overview, connectivity, transformation, orchestration, data warehouse, visualization | this file |
| 3 | A reusable analytics product capability | Separates reusable technology services from the content specific to each product: Connect, Transform, Analyze, Visualize | this file |
| 4 | End-to-end platform architecture | The 6 stages and the shared services (table above) | this file |
| 5 | Flexible data connectivity | Databases behind a firewall; multiple API endpoints; custom-configured endpoints | [ingestion-pipeline.md](ingestion-pipeline.md) |
| 6 | Configuration-driven data transformation | Map, Standardize, Manipulate, Persist | [ingestion-pipeline.md](ingestion-pipeline.md) |
| 7 | Pipeline orchestration model | Client > Pipelines > Jobs; execution modes | [ingestion-pipeline.md](ingestion-pipeline.md) |
| 8 | PostgreSQL analytics data warehouse | Detail and history, analytical models, scheduled summary aggregation | [postgres-and-summaries.md](postgres-and-summaries.md) |
| 9 | Apache Superset analytics experience | Postgres > datasets > metrics > charts > dashboards | [superset-and-security.md](superset-and-security.md) |
| 10 | Security and customer data isolation | Identity, authorization, customer context, data scope, content access | [superset-and-security.md](superset-and-security.md) |
| 11 | Onboarding a new product | The 8 steps | this file |
| 12 | Technology stack and product model | Superset, Postgres, pipeline engine, connectors; "one platform, many products" | this file |
| 13 | Implementation customizations | Descope, access controls, evolving ingestion, MCP/AI | [superset-and-security.md](superset-and-security.md), this file |
| 14 | Questions? | Final slide; the text "Quorum Presentation to ___ on ___" was left blank | n/a |

Note: the deck is a generic, reusable presentation, with no dealership numbers, Quorum product names or infrastructure diagram (Calgary, replica, Redis). Those details come from the meeting.

## Gaps and open questions

- Hardware, server sizes, Postgres and Superset versions, and how replica failover is done.
- ETL language: Rick said Go; neither Todd in the meeting nor the deck mention it.
- What is "Q Cloud (public/private)" and what is its role as a source? (appears in the previous stack)
- Backup and disaster recovery: which database is backed up, how often, and what is the recovery plan for Postgres and Superset (the call of 2026-10-27 only says "the database is backed up").
- Monitoring and alerts: the deck lists "monitoring" as a shared service; how does it work in practice and how does the team notice that a job failed?
- Microsoft SQL Server as a destination: it is already available, but for which use case, for which products, and when does it come into use?
- How to take a tested change from the ingestion dev environment to production? And how does Eduardo access the dev version, which sits behind the firewall (he already accesses it, the means was not recorded), and is dev in fact the data migration environment?
- Is there a version of the deck with Quorum-specific data (products, infrastructure)? The October 2026 deck is dated 13 and 14/10, before the meeting.
