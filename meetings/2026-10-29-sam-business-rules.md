---
title: 2-hour conversation with Sam (data and reporting team lead), 2026-10-29
summary: Note (Granola) from the conversation in which Sam explained the company, the dealership business, Quorum DMS, the evolution of the reporting platform and the work culture, and showed the DMS; part of the conversation was for getting to know each other.
updated_at: 2026-10-29
sources: [Granola notes from the conversation with Sam, pasted by Eduardo on 2026-10-29 (Granola summary, not the full transcript); context reported by Eduardo]
status: confirmed
---

# Conversation with Sam, 2026-10-29

- **Date:** 2026-10-29 (Thursday), about 2 hours long.
- **Participants:** Eduardo and Sam (data and reporting team lead, according to Eduardo).
- **Context (Eduardo):** Sam explained a lot about the business rules and then opened Quorum DMS and showed what the system looks like and how access works. **That part (screens and access) will be documented later**, when Eduardo sends screenshots. The conversation was **not only technical**: they were also getting to know each other.
- **This note is a primary source:** the raw text below (Granola, in English) is not rewritten. Corrections go in "Later corrections".

## How to read these notes

Because the conversation mixed personal introductions and technical content, the facts were separated as follows in the topic files:

- **Company and system facts** stated by Sam: recorded normally, with the source.
- **Illustrative analogies and proportions** (human body, "tip of the iceberg 10% / 90%", "cookie cutter"): recorded as didactic, **not as measured data**.
- **Sam's personal stories and opinions** (informally told origin of the company, a mistake he made, "it's who you are, not what you can do"): recorded as **Sam's view**, not as official Quorum policy, and only where it helps the work.

## Where each fact was documented

| Topic | File |
|---|---|
| Departments, inventories, revenue model, customer journey, WO/RO, hourly price vs. flat rate, over-the-counter vs. internal parts, automated messages | [business/dealership-domain.md](../business/dealership-domain.md) |
| Number of customers, one server per customer, same schema, dealer groups, "Dealer" database | [customers/dealerships.md](../customers/dealerships.md) |
| DMS as accounting software, modules, CRM area, integration with DealerMine, test environment | [products/quorum-dms.md](../products/quorum-dms.md) |
| Communicator, Lead Generator, Sales Planner, AutoVance (independent, acquired), products sold together or separately | [products/brands.md](../products/brands.md) |
| Company history as told by Sam | [company/overview.md](../company/overview.md) |
| Culture: scope, estimates, transparency, stakeholders as customers | [company/culture-and-expectations.md](../company/culture-and-expectations.md) |
| Flex Reports > Power BI > Postgres/Superset, two tenants (Q Analytics and Q Insights), end of logging into ~240 servers | [q-analytics/powerbi-to-superset-migration.md](../q-analytics/powerbi-to-superset-migration.md) |
| Internal report and benchmarking across customers | [q-analytics/overview.md](../q-analytics/overview.md) |
| Who sees what (isolation between dealerships, roles, executive board sees everything) | [q-analytics/superset-and-security.md](../q-analytics/superset-and-security.md) |
| Sam, Justin, Terry | [people/directory.md](../people/directory.md) |
| Jira and story points | [company/ways-of-working.md](../company/ways-of-working.md) |
| Access to the DMS test server, next steps | [onboarding/first-weeks.md](../onboarding/first-weeks.md) |
| New terms (WO/RO, F&I, flat rate, Flex Reports, Q Insights, etc.) | [glossary.md](../glossary.md) |

## Original notes (in English, unedited)

Quorum Company Overview

* Quorum builds dealership management software, founded ~30 years ago
   * Founder built it for his parents’ dealership, started with a few guys in a garage
* Product suite: Quorum DMS, AutoVance (acquired), DealerMind (CRM), Accessible Accessories
   * Products can be sold independently or integrated
   * “DMS agnostic”: AutoVance works with Quorum DMS or any competing DMS
* ~240 dealer customers distributed across North America, each on their own server
   * Same schema across all (“cookie cutter / cookie” analogy), but each with their own data

Dealership Business Structure

* Body analogy: sales = one hand, service = other hand, parts = right foot, accounting = spine/brain
* Key departments: sales, service, parts, body shop (not all dealerships), accounting
* Two distinct inventories:
   * Vehicle inventory: what salespeople sell
   * Parts inventory: used by service and parts departments
* Revenue model: vehicle sale is the “10% iceberg tip”; recurring service revenue is the 90%
   * Retaining customers for service, oil changes, tires drives the real money
* Customers range from single-store dealers to dealer groups (enterprise groups) with multiple locations and brands

Dealership Workflows and Terminology

* Customer journey: sales appointment, F&I/business office, forms, accessories install, service visits
* Work order = repair order = WO = RO (DMS calls it “work order”; CRM calls it “repair order”)
* Over-the-counter part sales vs. internal parts sales to service (much larger volume)
* Pricing models: hourly (e.g., $200/hr) vs. flat rate (fixed price regardless of time)
* Automated messages (“plugins”): sent to staff (e.g., parts requests) and customers (e.g., extended warranty reminders)

Quorum DMS Deep Dive

* DMS is accounting software that aggregates data from all business areas into a shared ledger schema
   * Each area (service, sales, parts, body shop) has its own table schemas, all writing to accounting
   * Accounting categories: sales, cost, expenses, other income, assets/liabilities
   * Journal entries and reconciliations handled within the DMS
* DMS modules visible in the ribbon: service, parts, vehicles, accounting, CRM
   * CRM area: customer prospects, sales appointments, service appointments
   * DealerMind integrates with DMS to push appointments and customer prospects
* “Dealer” database: always present on every server as the entry-point database
   * Dealer groups have additional named databases per store (e.g., “Airport Hyundai”)

Additional Quorum Products

* Communicator: messaging app tied to DMS; internal staff messaging + outbound customer messaging; supports automated messages
* Lead Generator: tracks leads and marketing ROI (e.g., radio ad spend vs. vehicles sold); may still be active
* Powerlane: mentioned; details not covered
* Sales Planner: work in progress
* AutoVance: deal structuring tool (cash, finance, lease scenarios); acquired from Rick

Analytics Platform Evolution

* History: custom Excel Flex Reports → Power BI (connecting to dealership servers) → centralized Postgres warehouse + Apache Superset
* Data ingestion tool (built by Justin/Todd): agents connect to each client, pull data into central Postgres
   * Replaces having to log into ~240 servers individually
* Previously two separate Power BI tenants: Q Analytics (customer-facing) and Q Insights (internal)
   * Superset consolidates both into one server, one tenant, one login
* Internal reporting (Q Insights equivalent) enables cross-customer benchmarking
   * Example: “You’re a GM dealer in this province; average vehicles sold this month is X”
* Row-level security enforces data isolation
   * Dealerships never see each other’s data
   * Within a dealership, role-based access (some see all, some see a subset)
   * Quorum C-suite (e.g., Terry) can see everything across all customers

Working Culture and Soft Skills

* Scope creep (“expansão de escopo”): handled case by case, no rigid formal process
   * Preference: document changes, update Jira story points, but just get it done
   * Key principle: transparency over emotion; give honest time estimates, own revisions
* Treat all stakeholders as customers, regardless of seniority (janitor = CEO)
* Honesty after mistakes is what keeps people employed; Sam’s story: accidentally updated 1,297 rows in the System Control table on a live server, reversed it and hit 2,478, fixed via backup
* “It’s who you are, not what you can do”: soft skills and character matter more than technical knowledge
* Managing expectations: estimates will sometimes be wrong in both directions; communicate proactively

Onboarding and Next Steps

* Sam to help Eduardo get access to the test server (ZZZ509) via QIS Connector / Colo
   * Test server is safe to explore; avoid connecting to named customer environments
* Eduardo building personal onboarding documentation using AI (Claude/Granola) to capture concepts, glossary, and knowledge gaps
* Sam available to reconnect later today; may shadow while Sam works on something
* Access request for DMS test environment to be raised with Todd or the help desk

## Later corrections

- **2026-10-29, check against the documentation:** **DealerMind** is a Granola transcription error; the correct name is **DealerMine** (already corrected on 2026-10-27).
- **2026-10-29, to be confirmed:** "AutoVance: acquired from Rick" and "built by Justin/Todd" may have wrong names in the transcription; see "Gaps and open questions" in [products/brands.md](../products/brands.md) and [people/directory.md](../people/directory.md).
- **2026-10-29, links:** the last row of the "Where each fact was documented" table now points to `glossary.md` (the former business glossary, `glossario-negocio.md`, was removed). The raw text of the notes was not changed.
