---
title: Onboarding meeting with Rick (CTO), 2026-10-26
summary: Note from the first meeting with Rick: stack and reporting architecture, first-week plan and working hours.
updated_at: 2026-10-27
sources: [Eduardo's notes from the 2026-10-26 meeting, Rick's diagram (2026-10-27)]
status: confirmed
---

# Onboarding meeting with Rick (CTO), 2026-10-26

- **Date:** 2026-10-26 (Monday)
- **Participants:** Eduardo and Rick (Quorum's CTO)
- **Topic:** stack and reporting architecture; first-week plan; working hours
- **This note is a primary source:** the raw text below is not rewritten. Corrections go in the "Later corrections" section.

## Where each fact was documented

| Topic | File |
|---|---|
| What Q Analytics is, RLS, access, aggregation | [q-analytics/overview.md](../q-analytics/overview.md) |
| Migration from Power BI + Synapse to Superset + Postgres, cost, owners | [q-analytics/powerbi-to-superset-migration.md](../q-analytics/powerbi-to-superset-migration.md) |
| Brands, products, coverage colors, infrastructure | [products/brands.md](../products/brands.md) |
| Rick, Todd, Sam, Michael | [people/directory.md](../people/directory.md) |
| Hours, time zone, Wednesday check-in | [company/ways-of-working.md](../company/ways-of-working.md) |
| Plan for today, this week and next week | [onboarding/first-weeks.md](../onboarding/first-weeks.md) |

## Agreed actions

- Rick sends the diagram and the call notes and schedules a check-in on Wednesday (30 min).
- Eduardo has a 1-on-1 with Todd (30 to 60 min) today or tomorrow for the architecture.
- Eduardo shadows Sam on the existing reports (3 to 4 hours) this week.
- The following week, Eduardo starts Michael's epic of new report stories.

## Original notes (in English, unedited)

Tech Stack and Reporting Architecture

* Migrated from Power BI + Azure Synapse to Apache Superset + Postgres (open source)
   * Migration completed ~26 days ago, beginning of October
   * Was costing ~$22,000–$24,000/month ($250K+/year); now just server costs
   * Todd built the data ingestion pipeline in Go; handles ~70% of the migration build
* Q Analytics: primary reporting tool, built for Quorum DMS dealerships
   * 250 dealerships; row-level security ensures each sees only their own data
   * Dashboards accessed directly via Superset (some optionally embedded in DMS)
   * Some data pre-aggregated for performance; some transaction-level detail retained
* Four brands under Quorum/Valsoft: Quorum DMS, AutoVance, Powerlane, Accessible Accessories
   * Purple = fully in Q Analytics; Yellow = early ETL, Red = limited reports
   * Goal: expand Q Analytics coverage to all products, not just Quorum DMS
   * Varied infrastructure: AWS (AutoVance, Accessible Accessories), mixed DB stacks (Postgres, MySQL, T-SQL)

First Week Plan and Working Hours

* Today/tomorrow: 1-on-1 session with Todd (30–60 min) to walk through architecture
* This week: shadow Sam on existing reports (3–4 hours); Sam moves to support team next week
   * Sam built 70+% of original Power BI reports over 10 years; Todd & Sam did the  migration from Power BI to PG/Superset
* Next week: begin tackling Michael's epic of new report stories
* Working hours: Eduardo to work 8-hour days starting 9 or 10 AM Brazil time (3 hrs ahead of Rick/Todd)
* Rick to send diagram/notes from this call and book a Wednesday check-in (30 min)

## Later corrections

- **2026-10-27, Rick's diagram:** the four brands are Quorum DMS, **DealerMine**, Autovance and Accessible Accessories. **Powerlane** is not a brand; it is a product of the Quorum DMS brand; in the original notes it appears in place of DealerMine. The legend colors in the diagram mean: purple = data flowing in Q Analytics, yellow = early progress, red = little or no data. Details in [products/brands.md](../products/brands.md).
- **2026-10-26, time zone (Eduardo):** the team is in Canada (most of it in MT), not in the US. See [company/ways-of-working.md](../company/ways-of-working.md).
