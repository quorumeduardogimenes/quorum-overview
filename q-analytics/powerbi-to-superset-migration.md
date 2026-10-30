---
title: Migration from Power BI + Azure Synapse to Superset + Postgres
summary: History, financial motivation, dates, previous stack and owners of the migration of Quorum's reporting stack.
updated_at: 2026-10-29
sources: [meeting with Rick, 2026-10-26 (../meetings/2026-10-26-rick-kickoff.md), meeting with Todd, 2026-10-26 (../meetings/2026-10-26-todd-technical-tour.md), conversation with Sam, 2026-10-29 (../meetings/2026-10-29-sam-business-rules.md)]
status: draft
---

# Migration from Power BI + Azure Synapse to Superset + Postgres

## Summary

Quorum migrated its reporting layer from **Power BI + Azure Synapse** to **Apache Superset + Postgres**, both open source. The migration was completed in early October 2026 and took about **5 months** (Todd).

## Evolution of the reporting platform (Sam)

In chronological order, according to Sam:

1. **Flex Reports:** custom reports in Excel.
2. **Power BI:** connecting to the dealerships' servers. There were **two tenants**: **Q Analytics** (customer-facing) and **Q Insights** (internal).
3. **Central Postgres + Superset:** the ingestion tool (built by Todd and, according to Sam, Justin) uses agents connected to each client to bring the data to the central Postgres. This **replaces logging in manually to about 240 servers**. Superset brings Q Analytics and Q Insights together in **one server, one tenant and one login**.

## Before and after

| Aspect | Before | After |
|---|---|---|
| BI / visualization | Power BI (Fabric), two tenants (customer and internal) | Apache Superset, one tenant |
| Storage/processing | Azure Synapse + SQL Server data warehouse | Postgres (primary + read replica) |
| Ingestion | Synapse (agent at the dealerships, which required a firewall opening) | Own tool, clients connected via WebSockets |
| Ingested sources | Salesforce, Q Cloud (public/private), Azure and the dealerships' local servers | See [ingestion-pipeline.md](ingestion-pipeline.md) |
| Synchronization | No incremental synchronization | Upsert, change tracking and incremental refresh |
| Hosting | Azure | Self-hosted in Quorum's colocation data center in Calgary |
| Cost | about US$ 22,000 to 24,000 per month (more than US$ 250 thousand per year) | only server costs (Rick); compute and storage close to zero (Todd) |
| Licensing | proprietary | open source |

Note: the notes use "$"; US dollar assumed (inferred). "Hosting: Azure" for the previous environment is inferred from Synapse and Azure being cited sources/services; to be confirmed.

According to Todd, the previous stack was expensive to operate, maintain and deliver. The new environment represents significant savings.

## Timeline

- Before Power BI: Flex Reports in Excel (Sam; no dates).
- Power BI: reports built over about **10 years**, mostly by Sam (more than 70%).
- Migration: lasted about **5 months** (Todd), completed about **26 days** before the 2026-10-26 meeting, that is, around 2026-09-30, "early October" according to Rick. Approximate start in May 2026 (inferred, by calculation).

## Who did it

- **Todd:** built the ingestion tool (in Go, according to Rick); the notes say he handled about 70% of the migration build. Sam also mentions **Justin** as a co-builder of the tool (name to be confirmed).
- **Sam:** author of more than 70% of the original Power BI reports; Todd and Sam did the migration of the reports to Postgres/Superset.

## Gaps and open questions

- What is the exact division of work between Todd and Sam? The notes say Todd handled ~70% of the migration build and that "Todd & Sam did the migration"; to be confirmed.
- Who is Justin and what was his role?
- Were all the Power BI reports migrated? Were any discarded or left pending?
- Was there a parallel-run period? Have Power BI/Synapse already been shut down?
- What were the main problems and lessons learned from the migration?
- What is the current monthly cost of the servers in Calgary?
- What are "Q Cloud (public/private)" and Salesforce in the previous environment? Do they remain as sources?
- When did Power BI replace Flex Reports, and what happened to the customers' Excel reports?
