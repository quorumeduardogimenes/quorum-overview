---
title: Superset, security (RLS and Descope), customizations and Eduardo's test dashboard
summary: How Apache Superset serves the Q Analytics dashboards: live queries, SSO via Descope, custom security manager, RLS, Dev/Prod environments, customizations and the use of Claude via MCP; plus what Eduardo built in Superset Dev (dataset, 10 charts and dashboard v1 on the first ingestion job), lessons learned and the v2 plan with Handlebars cards.
updated_at: 2026-10-29
sources: [meeting with Todd, 2026-10-26 (../meetings/2026-10-26-todd-technical-tour.md), Todd's "Analytics Platform" deck (slides 9, 10 and 13, sent on 2026-10-27), meeting with Rick, 2026-10-26 (../meetings/2026-10-26-rick-kickoff.md), conversation with Sam, 2026-10-29 (../meetings/2026-10-29-sam-business-rules.md), Eduardo (creation of the dataset, charts, filters and test dashboard in Superset Dev, chart list from a screenshot, v2 decisions, 2026-10-29), general Superset knowledge of the person who wrote the documentation (marked as inferred)]
status: draft
---

# Superset, security (RLS and Descope), customizations and Eduardo's test dashboard

## In plain words

Superset is the "storefront": it does not store the data, it just asks Postgres at the moment someone opens a dashboard. So that each dealership sees only what belongs to it, the login (handled by Descope) identifies who the user is, and a custom piece inside Superset uses that login data to decide which dashboards the person can open and which rows they can see. Quorum also added its own pieces to Superset (scrolling, gauges) and uses Claude, connected to Superset through MCP, to help create charts and check the numbers.

## How Superset queries

- **Live queries against Postgres**, with no pre-sync as in Power BI (Todd). Performance comes from the summary tables (see [postgres-and-summaries.md](postgres-and-summaries.md)).
- Reports use the **read replica**.
- Superset object chain (deck, slide 9): Postgres tables (warehouse and summaries) > **datasets** (semantic access to the analytical structures) > **metrics** (reusable measures and calculations) > **charts** (KPIs, trends, comparisons, details) > **dashboards** (interactive experience per product). The deck says Superset is the delivery layer; the "business-ready" data preparation lives in the warehouse and in the pipelines.

## Security

5-layer model (deck, slide 10):

| Layer | Description in the deck |
|---|---|
| 1. Identity | Authenticate users through a centralized identity provider |
| 2. Authorization | Apply roles and permissions to features and content |
| 3. Customer context | Resolve the customer/tenant scope associated with the user |
| 4. Data scope | Restrict queries, datasets and rows to the authorized customer's data |
| 5. Content access | Expose only approved dashboards, datasets and scheduled reports |

How this is implemented at Quorum:

| Topic | What we know | Source |
|---|---|---|
| Authentication (SSO) | **Descope**, via OAuth. In the meeting, Granola wrote "dScope"; the deck confirms **Descope** | Deck, slide 13; Todd |
| Access controls | **Login metadata** and a **custom Superset security manager** control access to dashboards and RLS | Deck, slide 13 |
| RLS (row-level security) | Limits each customer to its own data; the filter is **passed as metadata of the user's account** | Todd |
| Authorization | Driven by a **dealership security table**, synced to the warehouse | Todd |
| Descope scope | **Only Q Analytics uses it today**; the intention is to extend it to all Quorum products | Todd |
| Dashboard access | Directly through Superset; some can be embedded in the DMS (optional) | Rick |

## Who sees what (Sam's business view)

| Who | What they see |
|---|---|
| A dealership | Only its own data; **never** another dealership's |
| People inside the dealership | Access by **role**: some see everything in the store, others only a subset |
| Quorum leadership (for example, "Terry") | **Everything**, from all customers |

This still allows comparison between customers on the internal side (see [overview.md](overview.md)). This is Sam's functional description; it does not say how Superset implements it.

## Environments and promotion

- There is a **Dev** environment for building and testing.
- To promote to production, **export and then import** is used (Todd).

## Customizations on top of Superset

| Customization | Reason / how | Source |
|---|---|---|
| Authentication via Descope (OAuth) | SSO | Deck, slide 13 |
| Custom security manager | Access to dashboards and RLS based on login metadata | Deck, slide 13 |
| Scroll bar and zoom | Built with JavaScript; Superset did not have Power BI-style scrolling | Todd |
| Gauge charts | Built in **Handlebars** code | Todd |
| Lazy loading per dashboard page | With **Redis cache** | Todd |

## Claude via MCP

- There is **MCP support in Apache Superset**, which enables AI integration, with **authentication through Descope** (deck, slide 13).
- In the meeting, Todd showed the **MCP integration with Claude Desktop**, which can **generate charts and query Postgres directly**.
- Uses mentioned: chart creation, **SQL auditing** and **data validation**.
- Creating dashboards requires **refining the SQL query** to make sure the numbers are correct.
- Recommended flow: **build in Dev with Claude, check the result and export to production**.

(Eduardo's access to this integration has not been confirmed yet; see gaps.)

## Eduardo's test dashboard in Superset Dev (2026-10-29)

Eduardo is an admin of Superset Dev (set up by Todd, who also created the connection to the `datamigration` database). From the table loaded by the first ingestion job (`test_edu.dt_contact_daily`, see [dev-ingestion-practice.md](dev-ingestion-practice.md)), he created only new, personal objects.

**Conventions adopted:** names **in English**; `DM Colo - ` prefix on the charts and the dashboard; **Test Edu** tag on everything that is his (the tag identifies what is yours, even with the prefix). No existing object was changed.

**Dataset:** `dt_contact_daily` (schema `test_edu`), over the `datamigration` connection. When a new column entered the table (`appt_date_count`), the dataset's columns had to be synced from the source for it to show up in the charts.

**Charts (10):**

| Chart | Type | Configuration (Eduardo's account, following the step-by-step guide) |
|---|---|---|
| Contacts by Month and Type | Line | X axis `contact_day` by month; metric `SUM(contact_count)`; dimension `contact_type` |
| Contacts Shared by Type | Pie (donut) | `SUM(contact_count)` by `contact_type`; label with category and percentage |
| Appointments by Month | Bar | `SUM(with_appointment)` by month; filter `contact_type = BookAppt` |
| Days to Appointment | Bar | Average days until the appointment by month, weighted by `appt_date_count`; filter `BookAppt`; values between 1 and 21 days |
| Total Contacts | Big Number | `SUM(contact_count)`; subheader "Total contacts"; format `,d` |
| Booked Appointments | Big Number | `SUM(CASE WHEN contact_type = 'BookAppt' THEN contact_count ELSE 0 END)` |
| Contacts with Appointment | Big Number | `SUM(with_appointment)` |
| Turned Off Share | Big Number | Ratio of `Turned Off` contacts to the total; percentage format |
| Avg Days to Appointment | Big Number | Same weighted average as the days-to-appointment chart |
| Days with Contacts | Big Number | `COUNT(DISTINCT contact_day)` |

**Dashboard v1 (closed on 2026-10-29):** name following the `DM Colo - ...` convention (probably `DM Colo - Contacts`; to be confirmed). Layout: row of 6 big numbers at the top, then the line and the donut, then the bars. Two native filters: **Contact Type** (value, on `contact_type`, applied only to the charts where it makes sense) and **Period** (time range on `contact_day`). Superset warned that period filters apply to temporal columns defined in the filters section of each chart; according to Eduardo, the filter worked anyway, without changing the charts.

**Lessons (valid for Q Analytics):**

1. **Weighted average:** the weight must have the same universe as the value. The first version weighted `avg_days_to_appt` (computed only on rows with a valid appointment date) by `with_appointment` (rows with an appointment number), which diluted the average and gave values below 1 day, which are impossible. The fix was to store `appt_date_count` in the table (job and table changed) and use `SUM(avg * appt_date_count) / SUM(appt_date_count)`.
2. **Distinct counts are not additive:** `SUM(distinct_customers)` would repeat customers contacted on more than one day; that is why the column did not become a big number.
3. **Check the number against the source** (here, 12,344 contacts in the source and in the table) before building charts.
4. **Dominant type:** `Turned Off` is about 75% of the contacts in the test database, which flattens the other types on the scale; separating by filter (BookAppt) helps.

**Assessment of the visuals and paths for improvement:** Eduardo found the v1 look weak (fonts, colors and sizes of the big numbers were different). Paths, from simplest to most flexible `(inferred, general Superset knowledge; not tested on Quorum's version)`: (1) standardize font size and color scheme across all charts and the height in the dashboard; (2) dashboard CSS, the "CSS" field in Edit properties, which applies only to that dashboard (class names vary between versions); (3) **Handlebars** chart, which gives full control of HTML and CSS (this is what Todd uses for the gauges).

## Next step: dashboard v2 with Handlebars cards (planned for 2026-10-30)

None of this has been created yet. Eduardo's decisions: replace the big numbers with Handlebars, with the change versus the previous month on each KPI, and the period filter defaulting to the current month. **He accepts same-period comparison** (month up to the last day with data against the same days of the previous month).

**Modeling:** the period filter cuts the previous month out of the chart result, and Handlebars does not do math, so the previous month has to come in the same row. Plan: a **view in `test_edu`** (`v_contact_kpi_monthly`) with one row per month, the 6 KPIs for the month and for the previous month (`LAG`) and, for each KPI, the change, the direction (`up`, `down`, `flat`) and the ready-made text (such as `+12.3%` or `n/a`). Everything comes from `dt_contact_daily`; the ingestion job does not change.

**Same-period rule:** the cutoff is the last day with data in the table (today, 2026-10-15, so October counts days 1 to 15 and so does September). Older, complete months compare the whole month with the whole previous month.

**Change:** percentage for counts and for the average of days; **percentage points** for Turned Off Share.

**Superset:** new dataset on the view (`month_start` marked as temporal); one Handlebars chart "KPI Cards" (recommended: a single chart with the 6 cards, to get the same size, font and color), raw mode, 1 row, sorted by month descending, with `month_start` as a temporal filter "no filter" so the dashboard's period filter acts; dashboard v2 created with "Save as" from v1, without changing v1; period filter default "Current calendar month".

**Risks:** Superset may sanitize the Handlebars HTML (Todd uses Handlebars in the gauges, so the feature exists); the test source is stalled at 10/15, so in November "Current calendar month" will be empty (switch the default to the previous month); the average of days is approximate (the column is rounded to 2 decimal places).

## Gaps and open questions

- How exactly is RLS applied: does the custom security manager inject filters (for example, Superset RLS filters) from the login metadata, or is there also a policy in Postgres? Which field identifies the dealership? And for dealer groups, which see several stores?
- How does embedding in the DMS work, and the link with Descope SSO?
- Roles and permissions: Sam says there is access by role inside the dealership; which roles exist and where are they defined (Descope, Superset or the security table)?
- The dealership security table: where does it come from and how often is it synced?
- Superset version, and whether the customizations are a fork or extensions (where is the security manager code)?
- Which tools does the Superset MCP expose and with which credentials (read-only on the replica?). Will Eduardo have access?
- Is there a Superset README? Todd said he will generate one if the documentation is scarce.
- Where are the exported dashboard definitions kept (repository)?
- Is there a visual standard for Quorum's dashboards (fonts, colors, KPI cards) that the test dashboard should follow? Check with Sam or Todd.
- Does Dev Superset allow free HTML and CSS in Handlebars and in the dashboard CSS, or is there sanitization that strips styles? (To be tested in v2.)
