---
title: "Quorum DMS: what it is and how the data is organized"
summary: The DMS as accounting software that receives the data from all areas of the dealership, its modules, the CRM area, the "Dealer" database and the existence of a test environment.
updated_at: 2026-10-29
sources: [conversation with Sam, 2026-10-29 (../meetings/2026-10-29-sam-business-rules.md)]
status: draft
---

# Quorum DMS: what it is and how the data is organized

DMS stands for Dealer Management System. Public view of the product in [brands.md](brands.md); the screens and access will be documented later, from Eduardo's screenshots.

## In plain words

Sam described the DMS as, at its core, **accounting software**. Service, sales, parts and body shop each have their own tables, but **all of them write to the same ledger** (the accounting). That is why the DMS is the "spine" of the dealership's business: to understand where the numbers come from, you need to understand how each area feeds the accounting.

## How the data is organized (Sam)

- Each area (service, sales, parts and body shop) has **its own set of tables**, and all of them write to the **accounting** (shared ledger schema).
- **Accounting categories:** sales, cost, expenses, other income, assets and liabilities.
- **Journal entries and reconciliations** are done inside the DMS itself.
- **"Dealer" database:** always present on every server, it is the entry-point database. Dealer groups have additional databases per store (see [customers/dealerships.md](../customers/dealerships.md)).

## Modules (seen in Sam's demonstration)

The DMS button strip (ribbon) shows: **service, parts, vehicles, accounting and CRM**. In the **CRM** area: prospects (potential customers), sales appointments and service appointments. **DealerMine** integrates with the DMS by sending appointments and prospects.

## Products tied to the DMS

**Communicator** is the DMS messaging app, **AutoVance** works with the DMS or with others, and **PowerLane** is a product of the brand. Details in [brands.md](brands.md).

## Test environment

There is a **DMS test server** (identified by Sam as ZZZ509), reached through the "QIS Connector" / colocation. Sam said it is **safe to explore** and that Eduardo **should not connect to named customer environments**. The access request should be made to Todd or the help desk, and Sam will help. Steps and screens will be documented when Eduardo sends the screenshots. (No address or credential is recorded here.)

## Gaps and open questions

- What is the "QIS Connector" and how does it relate to the colocation ("Colo")? Is it the same "DM Colo" from the ingestion tool?
- On what technology (SQL Server? another) do the DMS databases run? The ingestion tool uses SQL Server and change tracking (see [q-analytics/ingestion-pipeline.md](../q-analytics/ingestion-pipeline.md)).
- What are the table and schema names for each area and for the accounting (data map)?
- How is access to the test DMS done in practice (document with the screenshots)?
