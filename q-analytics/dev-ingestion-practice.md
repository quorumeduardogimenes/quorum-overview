---
title: Eduardo's practice on the dev ingestion tool (diary and objects created)
summary: Record of the hands-on plan on the dev version of the ingestion tool and the test Postgres: access, what was explored (tool and source database), what Eduardo created (schema, destination server, pipeline, table, first job and test dashboard), queries and SQL used, job fix, lessons, security rules and next steps.
updated_at: 2026-10-29
sources: [Eduardo, 2026-10-28 and 2026-10-29 (reports of what he saw, queried and created, results of the queries and the job, screenshots and README of the tool, in conversation with this project's assistant), Todd, 2026-10-28 (messages relayed by Eduardo), call with Todd, 2026-10-27 (../meetings/2026-10-27-todd-ingestion-call.md)]
status: draft
---

# Eduardo's practice on the dev ingestion tool (diary and objects created)

This file is the diary of the hands-on plan agreed with Todd (see [ingestion-agent.md](ingestion-agent.md)). The concepts (destination servers, a job's fields, scheduling, snapshots, patterns of the production jobs) are in [ingestion-pipeline.md](ingestion-pipeline.md), the test Postgres in [postgres-test-environment.md](postgres-test-environment.md) and the test dashboard in Superset in [superset-and-security.md](superset-and-security.md). Screenshots and screens are not archived: what was seen is described in text. No address, credential, user or database code is recorded (guide, rule 9).

## In plain words

Eduardo walked the path of the data in a test environment: he read from a source database (DealerMine's SQL Server test database), went through the ingestion tool, wrote to the test Postgres and built a test dashboard in Superset Dev. First he explored without changing anything. Then he created only new, personal objects. The first job (contacts per day and type) ran without errors, the upsert was confirmed and dashboard v1 is ready. The next stage is a v2 with KPI cards and comparison against the previous month.

## Access (2026-10-28)

| System | Status | Source |
|---|---|---|
| Ingestion tool, production | Access without admin (Eduardo looked, read only, at DealerMine's scheduled jobs in production) | Eduardo |
| Ingestion tool, dev | Access, with admin (according to Eduardo). The "DM Colo" client shows up and is connected | Eduardo |
| pgAdmin 4 web and test Postgres | Working; connection registered as `test db` | Eduardo; see [postgres-test-environment.md](postgres-test-environment.md) |
| Superset Dev | Admin (set up by Todd). Todd has already created the connection to the `datamigration` database | Todd, via Eduardo |

How Eduardo reaches dev, which sits behind the firewall, was not recorded. The firewall is not worked around: only the access Todd granted counts.

## Naming convention and objects created by Eduardo

| Object | Where | Name | Status |
|---|---|---|---|
| Schema | `datamigration` database of the test Postgres | `test_edu` | Created on 2026-10-28 |
| Destination server | Dev ingestion tool | DMColoPGTestEdu (PostgreSQL, points to `datamigration`) | Created on 2026-10-28; the connection worked. The credentials were passed by Todd for this, and they aligned by message (Eduardo) |
| Pipeline | Dev ingestion tool, DM Colo client | DealerMine Test Edu | Created on 2026-10-28 |
| Destination table | `test_edu` | `dt_contact_daily` (composite primary key of 5 columns; 10 data columns, including `appt_date_count`) | Created on 2026-10-29; column `appt_date_count` added the same day |
| Job | DealerMine Test Edu pipeline | Contacts per day, type and inbound/outbound; writes to `test_edu.dt_contact_daily` | Ran without errors on 2026-10-29: 675 rows and 12,344 contacts; upsert confirmed; later edited to write `appt_date_count` |
| Dataset, 10 charts and dashboard v1 | Superset Dev | Prefix `DM Colo - `, Test Edu tag, names in English | Created on 2026-10-29; details in [superset-and-security.md](superset-and-security.md) |
| View `v_contact_kpi_monthly`, Handlebars chart and dashboard v2 | `test_edu` and Superset Dev | To be created | Planned for 2026-10-30 |

The convention adopted is `test_edu` / "Test Edu" in the Postgres and ingestion names, and the prefix `DM Colo - ` with the Test Edu tag in Superset. Suggestion `(inferred)`: keep this convention for the next objects, so Todd recognizes what is Eduardo's.

## Timeline

**2026-10-28**

1. **pgAdmin:** Todd sent the correct address (the first was wrong); Eduardo created the `test db` connection and saw the three databases.
2. **Read-only queries on `datamigration`:** the database and user were right; the only schemas were the four Postgres defaults; the user has permission to create schemas.
3. **Dev ingestion tool (read only):** the DM Colo client was connected; Eduardo looked at the destination servers and the existing pipelines and jobs, without changing anything. Summary in [ingestion-pipeline.md](ingestion-pipeline.md).
4. **Created** the `test_edu` schema, the DMColoPGTestEdu destination server and the "DealerMine Test Edu" pipeline.
5. **Tool documentation:** Eduardo pasted the README and screenshots; the summary is in [ingestion-pipeline.md](ingestion-pipeline.md).

**2026-10-28 and 2026-10-29**

6. **Exploration of the source database** through the Query Executor, read only (see section below).
7. **Production jobs:** Eduardo pasted the code of 5 scheduled jobs from the production DealerMine pipeline, to understand the patterns (summary in [ingestion-pipeline.md](ingestion-pipeline.md)). The code is not archived.
8. **Message to Todd** (Teams, in English): asked for documentation of the DealerMine tables and said he was choosing tables for the first jobs. No answer so far (see [../onboarding/questions-for-todd.md](../onboarding/questions-for-todd.md)).

**2026-10-29**

9. **Test of the SELECT** for the first job in the Query Executor: 675 resulting rows, 12,344 contacts in total (equal to the table count) and average days to appointment between 1 and 21.
10. **Destination table** created by Eduardo in `datamigration`, schema `test_edu`.
11. **Job configured** in the form (screen described in [ingestion-pipeline.md](ingestion-pipeline.md)) and **triggered manually** with the Run button (▶): it ran without errors. The check in pgAdmin gave **675 rows and 12,344 contacts**, equal to the test at the source.
12. **Upsert confirmed:** a second run of the same job did not duplicate the rows.
13. **Superset:** dataset, charts and dashboard v1 (see [superset-and-security.md](superset-and-security.md)). In the average days to appointment, the values came out below 1 day, which was impossible (the daily minimum at the source was 1).
14. **Fix:** the cause was the weight used in the weighted average (see "Lessons learned"). Eduardo added the `appt_date_count` column to the destination table, added the column to the job's `SELECT` (a change to his own job, without touching others), ran the job again, synced the dataset's columns and recreated the chart. The values now sit between 1 and 21 days.
15. **Dashboard v1 closed.** Eduardo decided on v2 (Handlebars cards with comparison against the previous month) and accepted the same-period comparison; the plan is in [superset-and-security.md](superset-and-security.md).

## Queries used

All only read, except the creations. In pgAdmin's Query Tool, connected to `datamigration`:

```sql
SELECT current_user, current_database();
SELECT schema_name FROM information_schema.schemata ORDER BY schema_name;
SELECT has_database_privilege(current_user, current_database(), 'CREATE') AS posso_criar_schema;
CREATE SCHEMA test_edu;  -- schema creation
```

In the Query Executor (source SQL Server), the exploration queries were: list of tables through `INFORMATION_SCHEMA.TABLES`; row count through the catalog (`sys.tables` with `sys.partitions`, which does not scan the data); columns through `INFORMATION_SCHEMA.COLUMNS`; primary keys through `INFORMATION_SCHEMA`; key uniqueness check with `COUNT(DISTINCT ...)`; and a count of `DTContactHistory` by contact type and direction, with first and last date.

## Exploration of the source database (DealerMine test)

The database has **1790 tables** (a prefix `adp_import_` is very frequent; its meaning is unknown and they were set aside). There is no documentation of the tables; Eduardo asked Todd. What the catalog count showed (numbers only, no data):

| Table | Rows | Note |
|---|---|---|
| `DTContactHistory` | 12,344 | Contact history; 84 columns; from 2025-09-22 to 2026-10-15. Used in the first job |
| `ServiceHistMaster` | 41,973 | Service history; future candidate (revenue and cost) |
| `Names1IdxMaster`, `SerindexMaster` | 9,854 and 9,853 | Customer records and vehicles per customer; avoid |
| `DMVehicleDetail` | 10,764 | Vehicle detail; the `Key` column is unique (10,764 distinct, none null) |
| `DTContactList` | 1,561 | Contact list; avoid rows, counts only |
| `DTSalesProduct` | 46 | Product per sales line; the `Productkey` column repeats (7 values for 46 rows) and each product has one description and several sales lines; the natural key is `productkey` + `selling_line` (inferred from the counts) |
| `DTStores`, `DTBranch` | 1 and 1 | The test database has one store and one branch |
| `DTCalls`, `DTCallType`, `DTSalesTypes`, `DTSalesPrefixes`, `DTScheduledMaintenance`, `am_deals`, `am_Vehicles`, `DealerMineNetRepairs`, `DTContactListCounts` | 0 | Empty in the test |

`ADPSalesTypes`, the `twilio_*` tables and the `dbo.dbname` table, which the production jobs use, did not appear in the count; they do not exist in this database `(inferred, from the query result)`. Neither `DMVehicleDetail` nor `DTSalesProduct` has a PRIMARY KEY at the source.

**`DTContactHistory`:** the contact types present are `Turned Off` (9,302, about 75%), `BookAppt` (about 3,033) and four types with fewer than 10 rows each (`BookingEService`, `DeleteAppt`, `VehicleAdd`, `ApptBooked`). The direction (`orgOutIn`) is almost always empty; when filled, it appears with different capitalization. The types used in the production jobs (`ConfirmAppt`, `Recall`, `CSI` etc.) do not appear. Free-text columns and columns with people's names were left out of the job. The meaning of `Turned Off` is an open question.

## The first job: contacts per day, type and direction

**Idea (pattern of the production jobs):** aggregate at the source and load only the result, with no rows of personal data.

**Upsert key** (the 5 grouping columns): `contact_day`, `contact_type`, `org_in_out`, `storeno`, `branchno`. Nulls and blanks are replaced by `(blank)`, `UNKNOWN` and `-1`, because key columns do not accept null.

**Calculated columns:** `contact_count` (contacts), `distinct_customers` (distinct customers), `with_appointment` (contacts with `ApptNo`), `avg_days_to_appt` (average days between the contact and the appointment, ignoring dates before 2000), `appt_date_count` (number of rows with a valid appointment date, the correct weight for the average) and `loaded_at` (load date and time).

```sql
SELECT
    CAST(ContactDate AS DATE)                                        AS contact_day,
    COALESCE(NULLIF(LTRIM(RTRIM(ContactType)), ''), '(blank)')       AS contact_type,
    COALESCE(NULLIF(UPPER(LTRIM(RTRIM(orgOutIn))), ''), 'UNKNOWN')   AS org_in_out,
    COALESCE(StoreNo, -1)                                            AS storeno,
    COALESCE(Branch, -1)                                             AS branchno,
    COUNT(*)                                                         AS contact_count,
    COUNT(DISTINCT CustNo)                                           AS distinct_customers,
    SUM(CASE WHEN ApptNo IS NOT NULL THEN 1 ELSE 0 END)              AS with_appointment,
    CAST(AVG(CASE WHEN ApptDate >= '2000-01-01'
                  THEN CAST(DATEDIFF(DAY, ContactDate, ApptDate) AS DECIMAL(10,2))
             END) AS DECIMAL(10,2))                                  AS avg_days_to_appt,
    SUM(CASE WHEN ApptDate >= '2000-01-01' THEN 1 ELSE 0 END)        AS appt_date_count,
    GETDATE()                                                        AS loaded_at
FROM DTContactHistory
WHERE ContactDate IS NOT NULL
GROUP BY CAST(ContactDate AS DATE),
         COALESCE(NULLIF(LTRIM(RTRIM(ContactType)), ''), '(blank)'),
         COALESCE(NULLIF(UPPER(LTRIM(RTRIM(orgOutIn))), ''), 'UNKNOWN'),
         COALESCE(StoreNo, -1),
         COALESCE(Branch, -1)
```

```sql
CREATE TABLE test_edu.dt_contact_daily (
    contact_day date NOT NULL, contact_type varchar(25) NOT NULL,
    org_in_out varchar(25) NOT NULL, storeno integer NOT NULL, branchno integer NOT NULL,
    contact_count integer, distinct_customers integer, with_appointment integer,
    avg_days_to_appt numeric(10,2), loaded_at timestamp,
    PRIMARY KEY (contact_day, contact_type, org_in_out, storeno, branchno)
);
ALTER TABLE test_edu.dt_contact_daily ADD COLUMN appt_date_count integer;  -- fix for the average
```

**Job configuration:** type Database (SQL); DM Colo Client ID; Target Table and destination override `test_edu.dt_contact_daily` (with schema); only the DMColoPGTestEdu destination server; conflict on the 5 key columns with action `update`; scheduling Manual only. **Result:** 675 rows and 12,344 contacts; the second run kept 675 (upsert confirmed).

## Lessons learned

1. **Weighted average:** the weight must have the same universe as the value. The first version of the chart weighted `avg_days_to_appt` by `with_appointment`, but the average only considers rows with a valid appointment date; the denominator was inflated and the result came out below 1 day, which is impossible. Fixed with `appt_date_count`.
2. **Aggregate at the source and keep the numerator and denominator:** columns like `appt_date_count` allow averages and ratios to be rebuilt over any period, in Superset or in a view.
3. **A distinct count is not summable across days:** `distinct_customers` cannot be summed by month.
4. **Check against the source:** the count of 12,344 contacts at the source and at the destination was the proof that nothing was lost.
5. **Run it twice:** the second run proves the upsert does not duplicate. Adding a column to the `SELECT` and running again filled it (action `update`), without recreating the table.
6. **Key without nulls:** replace nulls and blanks with fixed values before writing, as the production jobs do.
7. **Change only what is yours:** the fix was made in the job Eduardo himself created, opening it through the pencil on his own job.

## Tool map for a job

| I need... | Where | Caution |
|---|---|---|
| See the source tables and columns | Query Executor (DM Colo client) | `SELECT` only; do not copy data rows |
| Create the destination table | pgAdmin, schema `test_edu` | It must exist before the job; with a PRIMARY KEY that matches the upsert key |
| Create the job | DealerMine Test Edu pipeline, Add Job | Client ID filled in (blank runs on all clients of the pipeline); only Eduardo's destination server; open through Add Job, never through the pencil of other people's existing jobs |
| Run | The job's Run button (▶) | Never Run All |
| Check | pgAdmin; Runtime Logs and Data Transfer History in the tool | |

## Security rules applied

- Create only new, personal objects. Never change or delete what already exists, not even on dev, even as an admin.
- Clicking the X in the tool can delete jobs: do not click.
- Do not use Run All, snapshot Apply, Runtime Capacity, Delete on destination servers or the accounts area.
- The `babelfish_db` and `postgres` databases, the DealerMine Historical and DealerMine Incremental pipelines and the existing jobs (including the production ones, only viewed) are Todd's: look only.
- New jobs `(inferred, agreed in conversation, not by Todd)`: manual scheduling, Client ID filled in with DM Colo and writing only to Eduardo's destination server.
- The test database has real exported data: do not copy data rows into conversations or documents, avoid tables with personal data or passwords and prefer aggregated results.
- No credentials, IPs, addresses, user names or database codes in the documentation.

## Next steps

1. **Dashboard v2 (2026-10-30):** view `v_contact_kpi_monthly` in `test_edu`, dataset, Handlebars chart with the 6 cards and same-period comparison, dashboard v2 with a default filter on the current month (full plan in [superset-and-security.md](superset-and-security.md)).
2. Record the job form fields not yet seen (client, pipeline, conflict, scheduling, variables, last run).
3. Optional: jobs on `DTSalesProduct` (key `productkey` + `selling_line`), `DMVehicleDetail` (key `Key`) and `ServiceHistMaster`; and an incremental job with the last-run variable, like production job 5.
4. Gather the remaining questions and take them to Todd, showing what has already been tested.

## Gaps and open questions

- Did the "DealerMine Test Edu" pipeline need to be assigned to the DM Colo client in the client's dashboard? The job ran without errors; the step was not recorded.
- What does it mean that `Turned Off` is about 75% of the `DTContactHistory` contacts?
- Which DealerMine tables are transactional, supporting or legacy? No documentation (request sent to Todd, no answer).
- How Eduardo accesses dev and whether it is the same as the data migration environment (Round 3, 2; DM Colo showing up on dev is a sign, not a confirmation).
- How to promote what was tested to production (Round 3, 1; snapshots with GitHub seem to be the path, to be confirmed).
