---
title: Q Analytics, overview
summary: What Q Analytics is, who it was built for, how it is accessed and what the expansion goal is.
updated_at: 2026-10-30
sources: [follow-up with Rick, 2026-10-30 (../meetings/2026-10-30-rick-follow-up.md), meeting with Rick, 2026-10-26 (../meetings/2026-10-26-rick-kickoff.md), Rick's diagram (2026-10-27), meeting with Todd, 2026-10-26 (../meetings/2026-10-26-todd-technical-tour.md), Todd's "Analytics Platform" deck (2026-10-27), conversation with Sam, 2026-10-29 (../meetings/2026-10-29-sam-business-rules.md)]
status: draft
---

# Q Analytics, overview

## What it is

Quorum's **main reporting tool**, built for the dealerships that use **Quorum DMS**. It runs on **Apache Superset + Postgres** (open source), since the migration completed in early October 2026 (see [powerbi-to-superset-migration.md](powerbi-to-superset-migration.md)). The data arrives through an in-house ingestion tool and the whole stack runs on Quorum's own servers, in Calgary (see [architecture.md](architecture.md)). Todd describes it, in the deck, as a **reusable** analytics **platform**, designed to be deployed across several products.

## Who uses it and how

- About **250 dealerships** use Q Analytics (Rick; Sam mentioned ~240 customers); the ingestion tool manages **256 dealership databases** (Todd, technical reference). Comparison in [customers/dealerships.md](../customers/dealerships.md).
- **Row-level security (RLS):** each dealership sees only its own data. The filter is passed as metadata of the user's account and login is done through **Descope** (SSO), with a custom security manager in Superset (see [superset-and-security.md](superset-and-security.md)).
- The dashboards are accessed **directly through Superset**; some can be **embedded in the DMS**, optionally.

## Internal use and benchmarking (Sam)

In Power BI there were two tenants: **Q Analytics** (customer-facing) and **Q Insights** (internal). Superset brings the two together on one server, one tenant and one login. The internal side (equivalent to Q Insights) lets you **compare customers against each other**: for example, telling a GM dealership in a province what the average number of vehicles sold in the month is. Quorum's leadership sees the data of all customers. (Sam's account; whether the internal panel already exists in Superset or is planned was not said.)

## Data

- Part of the data is **pre-aggregated** (summary tables refreshed every night by pg_cron) for performance; part keeps **transaction-level detail**. Details in [postgres-and-summaries.md](postgres-and-summaries.md).
- Ingestion is done by an in-house tool, in **Go** according to Rick, built by Todd (and Justin, according to Sam) (see [ingestion-pipeline.md](ingestion-pipeline.md) and [people/directory.md](../people/directory.md)). Before, you had to log into each customer server; now agents send the data to the central Postgres.

## Goal and current coverage

Expand Q Analytics coverage to **all products**, not just Quorum DMS. Today, according to Rick's diagram, data flows for 4 products of the Quorum DMS brand (Quorum DMS, PowerLane, Communicator and Sales Planner); the DealerMine and Autovance products are in early progress; the Forecast Tool and Accessible Accessories have little or no data. Full table in [products/brands.md](../products/brands.md). Descope, used today only here, is to be extended to all products (Todd). The deck brings an 8-step roadmap for onboarding a new product (see [architecture.md](architecture.md)).

## Rebranding and AI direction (Rick, 2026-10-30)

Source: [meetings/2026-10-30-rick-follow-up.md](../meetings/2026-10-30-rick-follow-up.md) (Granola summary).

- Q Analytics is to be **rebranded as "Mori Metrics" next year**. "Mori" is the AI brand: everything AI at Quorum falls under it (see [products/brands.md](../products/brands.md)). Date and scope of the rename were not given.
- **Longer-term vision:** an **MCP server connected to the dashboards**, so users can query the data through AI. Rick's view: the AI harness is the visualization tool of the future. Superset already supports MCP for builders (see [superset-and-security.md](superset-and-security.md)); the vision above is about end users.
- **First new page of this kind:** the Agent Console rebuild, an internal BDC report that includes Mori AI calls (see [agent-console-report.md](agent-console-report.md) and [the task](../tasks/dr-2907-agent-console-rebuild.md)).

## Known owners

- Todd: ingestion pipeline and architecture (technical reference for Q Analytics).
- Sam: data and reporting team lead; author of more than 70% of the original Power BI reports, which were migrated; moves to the support team in the week of 2026-11-02 (Rick; to be reconciled with the current role).
- Michael: owner of the epic of new report stories, which Eduardo starts tackling in the week of 2026-11-02.
- Patrick Manning: business partner for the Agent Console rebuild (epic DR-2907, created on 2026-10-29). Whether this epic is Michael's epic is unknown.

## To go deeper

[architecture.md](architecture.md), [ingestion-pipeline.md](ingestion-pipeline.md), [postgres-and-summaries.md](postgres-and-summaries.md), [superset-and-security.md](superset-and-security.md), [business/dealership-domain.md](../business/dealership-domain.md) (business rules behind the reports) and the [glossary](../glossary.md).

## Gaps and open questions

Technical questions are in each specific file. Pending at the general level:

- How does the embed in the DMS work (authentication mechanism, which dashboards)?
- How many reports/dashboards exist today and where is there a catalog of them?
- Does the internal panel with benchmarking across customers already exist in Superset? Who at Quorum uses it besides leadership?
- Which reports use summary tables and which read transactions?
- When and how will the rebranding to "Mori Metrics" happen, and does it affect the product name for dealers, the login or the URLs?
