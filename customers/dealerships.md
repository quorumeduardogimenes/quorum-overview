---
title: Customers: the dealerships
summary: How many dealerships are customers, how each one has its own server and data, and what changes between single stores and groups.
updated_at: 2026-10-29
sources: [conversation with Sam, 2026-10-29 (../meetings/2026-10-29-sam-business-rules.md), meeting with Rick, 2026-10-26, meeting with Todd, 2026-10-26]
status: draft
---

# Customers: the dealerships

## In plain words

Think of a cake recipe ("cookie cutter", as Sam said): all dealerships use the **same mold** of database (same tables and fields), but each one has **its own server** with **its own data**. That is why a report written once works for all of them, and ingestion has to fetch the data from each server separately.

## What we know

- **Number of customers:** Sam said about **240** dealerships, spread across **North America**, each on its **own server**.
- **Same schema** on all servers, with different data on each.
- **Size:** from single stores to **dealer groups** (enterprise groups) with several locations and brands.
- **"Dealer" database:** exists on every server as the entry database. Groups have **additional databases, one per store**, named after the store.
- **Data consumption:** each dealership sees only its own data in the dashboards (see [q-analytics/superset-and-security.md](../q-analytics/superset-and-security.md)).

## Numbers that do not match across sources

| Source | Number | Note |
|---|---|---|
| Sam (2026-10-29) | ~240 customers | Approximate |
| Rick (2026-10-26) | ~250 dealerships | Approximate |
| Todd (2026-10-26) | 256 dealership databases | Technical reference |

Per the guide, Todd's number prevails on technical matters. It is a number of **databases**, not of customers. `(inferred)` The difference may come from groups with several databases (one per store), but this needs to be confirmed.

## Gaps and open questions

- How many of the ~240 customers are single stores and how many are groups? How many databases does the largest group have?
- Are Todd's 256 databases the "Dealer" databases plus the groups' per-store databases?
- Do all customers use Quorum DMS? Are there customers of only other products (for example, AutoVance alone)?
- How are customers and stores identified in the data (dealership code, name)?
