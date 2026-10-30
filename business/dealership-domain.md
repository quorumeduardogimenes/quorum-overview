---
title: How a dealership works (business rules)
summary: Departments, inventories, revenue model, customer journey, terms and pricing models of a dealership, as Sam explained them to Eduardo.
updated_at: 2026-10-29
sources: [conversation with Sam, 2026-10-29 (../meetings/2026-10-29-sam-business-rules.md)]
status: draft
---

# How a dealership works (business rules)

## In plain words

Sam used a human body analogy: **sales** is one hand, **service** is the other hand, **parts** is the right foot and **accounting** is the spine/brain, because everything that happens in the other areas ends up recorded in it. It is a teaching analogy of his, not a company term. For the reports, the useful idea is: each area generates its own data, and the money from all of them shows up in accounting.

## Departments

| Department | Note |
|---|---|
| Sales | Sells vehicles |
| Service | Maintenance and repairs |
| Parts | Sells and supplies parts |
| Body shop | **Not every dealership has one** |
| Accounting | Receives the entries from all areas |

## Two different inventories

- **Vehicle inventory:** what the salespeople sell.
- **Parts inventory:** used by the service and parts departments.

## Revenue model

- The vehicle sale is the "tip of the iceberg": Sam spoke of **10%** of revenue, against **90%** coming from recurring service. These are **illustrative proportions he gave**, not a measured number.
- The real money comes from **retaining the customer** for services: oil changes, tires and maintenance.

## Customer journey

Sales appointment, then the **F&I / business office** (financing and insurance, paperwork), forms, **accessory installation** and, afterwards, service visits.

## Terms and rules

| Topic | What Sam said |
|---|---|
| Work order | **Work order = repair order = WO = RO**. The DMS uses "work order"; the CRM uses "repair order" |
| Parts sales | **Over-the-counter** (for customers) x **internal** (for service). Internal sales have a **much larger volume** |
| Service pricing | **Hourly** (example given: US$ 200/hour) or **flat rate** (fixed price, regardless of the time spent) |
| Automatic messages | Called "plugins" by Sam: sent to staff (for example, a parts order) and to customers (for example, an extended warranty reminder) |

## Why this matters for the reports `(inferred)`

- The same concept has different names across systems (WO in the DMS, RO in the CRM), so reports that combine the two sources need to handle this.
- Since service is the largest part of revenue, service and retention reports tend to be a priority for dealerships.
- Terms and definitions in [glossary.md](../glossary.md); how the data sits in the system, in [products/quorum-dms.md](../products/quorum-dms.md).

## Gaps and open questions

- Whenever Sam says "plugins": what is that technically (Communicator message rules? another module)?
- How do flat rate and hourly pricing appear in the data (tables and fields) and in the existing reports?
- Which existing reports follow each department? (Report catalog not yet mapped.)
