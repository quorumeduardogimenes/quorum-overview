---
title: Data ingestion pipeline (in-house ETL)
summary: In-house platform that moves data from the sources into Postgres: client configuration, scheduling of extraction and transformation pipelines, jobs (fields, form, scheduling, upsert, snapshots), destination servers, patterns in the production DealerMine jobs, execution modes, features and example sources.
updated_at: 2026-10-29
sources: [meeting with Todd, 2026-10-26 (../meetings/2026-10-26-todd-technical-tour.md), call with Todd, 2026-10-27 (../meetings/2026-10-27-todd-ingestion-call.md), Todd's Word document about the agent (summarized in ingestion-agent.md), Todd's "Analytics Platform" deck (slides 5, 6, 7 and 13, sent on 2026-10-27), Eduardo (clarifications, 2026-10-27; observations on the dev version of the tool, screenshots and README text from the tool's repository, pasted by him on 2026-10-28; job form, code of 5 scheduled production DealerMine jobs and first test job run successfully, 2026-10-29), meeting with Rick, 2026-10-26 (Go language) (../meetings/2026-10-26-rick-kickoff.md)]
status: draft
---

# Data ingestion pipeline (in-house ETL)

## In plain words

It is a tool that moves data from the sources (dealership databases and APIs) into Postgres, with a control panel. At each dealership there is a small program (the **client**, also called the **agent**) that calls the central server and stays on the line. Through the central platform the team configures the clients and schedules the extraction and transformation **pipelines**; the server sends the tasks ("fetch the new sales"), the client runs them on the local database, the data is adjusted along the way (fields, dates, rules) and written to the destination. Tasks can have a schedule and repeat on their own, but they can also be run by hand.

In the meeting, Todd showed Q Analytics and, behind it, this ingestion tool: a platform that lets you **configure the clients, schedule data extraction and transformation pipelines** and other features (Eduardo, 2026-10-27).

**For the end-to-end flow, the deployment step by step, the test environments and the cautions (X can delete jobs), read [ingestion-agent.md](ingestion-agent.md). Eduardo's practice diary is in [dev-ingestion-practice.md](dev-ingestion-practice.md).**

## Why it was built in-house

No off-the-shelf tool met the **security and flexibility** requirements (Todd). Granola also records that the new ETL **avoids the need to whitelist addresses in the firewall**, something that used to be a security risk with the Synapse agent (Eduardo said the old structure is not the focus).

## How it connects

- **Clients** are installed **per dealership or per environment** and **connect back to the central server via WebSockets** (Todd).
- Because the client makes the outbound connection, **there is no need to open inbound access in the dealership's firewall**. The agent guide confirms it only needs outbound access to port 443 (`wss`) or 80 (`ws`).
- The agent's first registration uses a **single-use token** generated in the panel; afterwards the server recognizes the agent by a permanent token stored in the Windows credential vault. Step by step in [ingestion-agent.md](ingestion-agent.md).
- The tool's language is **Go** (Rick; confirmed by the repository, which Eduardo described as server + client + React panel).

## Connectivity (deck, slide 5)

- **Databases behind a firewall:** secure ingestion reaches operational databases that are not exposed to the public internet.
- **Multiple API endpoints:** a source can combine several endpoints to collect related entities, transactions and reference data.
- **Custom-configured endpoints:** configurable paths, parameters and mappings for specific product or customer integrations.

## Configuration-driven transformation (deck, slide 6)

Four steps to convert varied data into consistent analytical structures:

1. **Map:** API variables and source columns to warehouse fields.
2. **Standardize:** define dates, data types, default values and normalization.
3. **Manipulate:** apply reusable field-level and record-level functions.
4. **Persist:** insert new records or upsert existing ones, with conflict resolution.

## Orchestration model: clients > pipelines > jobs

| Level | What it is (according to the notes, the deck, the Word document and the README) |
|---|---|
| Client | The record, in the tool, of a dealership or environment; the program installed there is the **agent**, linked to the record by the `client_id`. Once deployed, the configuration concentrates on the jobs. For APIs, a client is the "front door" configured according to the system (Zoho, Powerlane, etc.) |
| Pipeline | A defined sequence of related jobs. **Pipelines are global entities** and are **assigned to clients** in the client panel (README; the Word document says they are assigned to the agent at deployment) |
| Job | The unit that runs: an extraction, transformation or load task. A job runs **once for all associated clients** (Todd). In an API client, several jobs can call different APIs |

There are **incremental** pipelines (only what changed) and **historical** ones (load of the past) (Todd; see glossary). The existing ones have no backup and are work in progress.

**Execution modes (deck, slide 7):**

| Mode | What it does |
|---|---|
| Scheduled | Recurring execution by time |
| Client | Runs all the work configured for a client |
| Pipeline | Runs a defined sequence of related jobs |
| Job | Runs a single task |
| Manual / recovery | Directed execution, for validation or restart |

The deck says a common execution framework improves repeatability, operational visibility and recovery control.

Scale: **256 dealership databases** managed (Todd, who is the technical reference; Rick's "about 250" is an approximation) and **108 jobs** in total, between incremental and production (call of 2026-10-27). A screenshot of the tool showed a total of **1776 jobs**; the difference was not explained (open question, see gaps).

Supported source types: **SQL, API and custom-configured endpoints**.

## How the tool works (README, summarized on 2026-10-28)

Eduardo pasted the README of the tool's repository and screenshots of the screens, just for context. The raw text and the repository are not archived here; what follows is a summary. **The dev version's screen is newer than the README** (for example, the README does not describe choosing a destination server per job).

**Job types:** `db` (reads from a database) and `api` (calls an API).

**Fields of a database job:**

| Field | What it is for |
|---|---|
| Client ID | Which client the job runs on. **Blank = all clients in the pipeline** |
| Pipeline | Which pipeline the job belongs to |
| Database config (scope and ids) | Which of the client's database configurations to use |
| Source query | The `SELECT` that runs on the source |
| Table | Destination table (accepts schema.table) |
| Conflict column(s) and conflict action | Upsert key and what to do on conflict: `nothing` (ignore) or `update` (update). **The key must match a PRIMARY KEY or UNIQUE constraint of the destination table** |
| Now columns | Columns filled with the date and time of the load |
| `{{key}}` variables | Values that go into the query |
| Last run variable | Marker of the point of the last load (see below) |
| Enabled | Turns the job on and off |
| Schedule | When it runs (see below) |

**Last run variable (incremental load):** stores the point up to which the job has already read (for example, a date) and hands it to the query on the next round. It has an initial value and three ways of being updated: by timestamp, by an expression over the destination, or by a script on the source.

**Scheduling:** manual, one date/time, every N minutes or hours, every N days at a set time, or on days of the week at a set time. **Without a schedule, the job runs when the client connects.** The screen has a "Manual only" option.

**Writing:** with a conflict key, it uses a **staging table and merge**; `GENERATED` columns in the destination are ignored. The README also has the SQL Server to Postgres **type mapping** table (check the README when a type fails).

**Manual execution:** Run button (▶) on the job, with a confirmation dialog; the job goes through Queued, Running and then shows the result. There is also **Run All**, which runs everything and should be avoided.

**Observability:** Runtime Logs, Data Transfer History, Active Jobs and **Query Executor** (live query against a client's source database). There is also **Runtime Capacity** (limits on simultaneous execution; do not touch).

**Configuration snapshots and GitHub:** it is possible to store configuration snapshots (pipelines, pipeline-to-client assignments and jobs; it does **not** include credentials or the "last run" markers) and export, apply or restore via GitHub. Applying a snapshot overwrites the configuration: do not use it without alignment.

**Access and security:** local login or OIDC; enrollment tokens (Deploy dialog) and worker credentials.

**Destination servers** are a tab of their own (see below).

## Job form in the dev version (observed on 2026-10-29)

Eduardo opened the job form (titled "Edit Job", also used to create) and showed the top part. What is in it:

| Section or field | What it does |
|---|---|
| Job Type | Choice of type, for example "Database (SQL)" |
| Show Code | Button at the top; opens the job's code (not explored) |
| SQL Query (required) | The source query. The gray example is `SELECT TOP 10000 * FROM MyTable` |
| Target Table (required) | Name of the destination table. The gray example is `my_table` |
| Destination Servers | With no selection, it writes to the default PostgreSQL. Accepts **up to 8 servers**, each with **its own table and its own override columns**. The form warns: **"Target tables must already exist"** (the destination table must exist beforehand) |
| Destination Table Override | Table name at that destination; Eduardo uses the `schema.table` format |
| Destination Column Overrides | Pairs of source column and destination column; columns without a pair keep their name; **the conflict keys follow these overrides**. For API jobs, the names from the API field map are used, not the API paths |
| Add Destination and Remove Destination | Adds or removes a destination from the job |

The client, pipeline, conflict, scheduling, variables and last run fields are further down in the form and **have not yet been recorded in detail**. Always open it through **+ Add Job** on Eduardo's pipeline, never through the pencil of an existing job.

**Confirmed in practice (2026-10-29), with Eduardo's first job:**

- **Destination with `schema.table` override:** the job writes, through Eduardo's destination server, to the `dt_contact_daily` table in the `test_edu` schema, and ran **without errors** (the `schema.table` format works in the override).
- **Table created beforehand:** the destination table was created manually in Postgres before the job, as the form requires, with a PRIMARY KEY equal to the job's conflict columns.
- **Upsert:** two runs of the same job kept the same total number of rows (675) and, after adding a column to the `SELECT` (and to the destination, with `ALTER TABLE ... ADD COLUMN`), a new run filled it through the `update` action. The destination table must get the column before the job starts sending it.
- **Execution:** manual, through the job's Run button (▶), with "Manual only" scheduling.
- **Assigning the pipeline to the client:** the job ran, but Eduardo did not record whether it was necessary to assign the pipeline to the DM Colo client in the client panel (question kept open).

The step by step, the `SELECT` and the table are in [dev-ingestion-practice.md](dev-ingestion-practice.md).

## Patterns in the production DealerMine jobs (observed on 2026-10-29)

Eduardo pasted the code of 5 scheduled jobs from the production DealerMine pipeline, only to understand the patterns. The code is **not archived** (it contains names of internal databases and servers); according to Todd, via Eduardo, all these queries and procedures are on GitHub. Summary:

| Job (summary) | What it does |
|---|---|
| 1 | Daily counts of scheduling targets by call type (confirmation, CSI, recalls, maintenance, etc.), store and branch, across several dealership databases |
| 2 | Monthly report of the scheduling center (calls, time, appointments, goal), with temporary tables. It had dates and a database hard-coded in the text, looks like a working copy `(inferred)` |
| 3 | Contacts by month, store and branch for one database (uses the `{{dbname}}` variable) |
| 4 | The same as 3, for several databases; limits to 100 thousand rows per database |
| 5 | Contacts by day, type, reason, schedule and direction; **incremental**: rereads from one day before the last run (`{{last_run}}`) |

**Recurring patterns:**

1. **Aggregates at the source** (`GROUP BY` with counts) and loads only the result; it does not copy whole tables.
2. **Several databases at once:** jobs 1, 4 and 5 build a query with `UNION ALL`, walking through the list of dealership databases and calling each one by linked server, from a central database that has the call tables (`twilio_*`) and the database registry. This does not exist in the DM Colo test database.
3. **Job variables:** `{{datestart}}`, `{{dateend}}`, `{{dbname}}` and `{{last_run}}`. That the name `last_run` is the form's Last run variable is `(inferred)`.
4. **Incremental with overlap:** job 5 goes back one day before the last run, and the upsert avoids duplicating.
5. **Composite key:** the grouping columns (database, store, branch, date, type) form the upsert key `(inferred)`. Nulls and blanks are replaced with texts such as `(blank)` and `unknown` before writing.
6. **Postgres-ready output:** lowercase snake_case columns, plus `created_at` and `updated_at` in the `SELECT` itself.
7. **Subject:** the dealerships' scheduling center (BDC): contacts by type (schedule, confirm, recall, maintenance, CSI, sale and others) by store and branch. The hierarchy in the data is database, store (`storeno`) and branch (`branchno`).
8. One job queries a users table whose name suggests passwords, only to filter by user type; do not read that data.

Eduardo's first job applied patterns 1, 5 and 6 (aggregate at the source, normalize nulls for a composite key and snake_case output), at test scale, over a single contacts table (see [dev-ingestion-practice.md](dev-ingestion-practice.md)).

## Destination servers and anatomy of a job (observed in the dev version, 2026-10-28)

What Eduardo saw while exploring the dev version of the tool (his account; not official documentation of the tool):

**Destination servers.** The **Destination Servers** tab lists the servers where jobs can write. Each has a type (SQL Server or PostgreSQL), a server and a database; the credentials are encrypted. Before Eduardo's practice there were three in dev:

| Destination server | Type | Destination database |
|---|---|---|
| DMColoBabelfish | SQL Server | One of Todd's SQL Server test databases |
| DMColoMSSQL | SQL Server | The same SQL Server test database |
| DMColoPG | PostgreSQL | `babelfish_db` (inferred: the same as the test Postgres seen in pgAdmin) |

Eduardo later created **DMColoPGTestEdu**, PostgreSQL pointing to `datamigration` (see [dev-ingestion-practice.md](dev-ingestion-practice.md)). As Eduardo observed, the tab does not show which pipelines and jobs use each server (improvement idea QA-2 in [../improvement-ideas.md](../improvement-ideas.md)).

**Pipelines of the DM Colo client in dev:**

- **DealerMine Historical** and **DealerMine Incremental:** Todd's tests with SQL Server, according to Eduardo. Eduardo did not detail them.
- **Unassigned:** jobs that do not belong to any pipeline. Two were running.
- **DealerMine Test Edu:** created by Eduardo; received the first job on 2026-10-29, which ran without errors.

**Anatomy of a job**, from an Unassigned job that runs for all clients:

| Field | What the job had |
|---|---|
| Source query | An unfiltered `SELECT` (reads the whole table) of service order columns (number, date, operation code, cause, complaint, correction, store, branch and others) from the `dbo.CCCMaster` table of the DealerMine test database |
| Target table | `dbo.CCCMaster` |
| Destination servers | Two: SQL Server (DMColoMSSQL) and Postgres (DMColoPG) |
| Destination table override | In Postgres, the table is written under another name: schema with the source database's prefix followed by `_dbo`, table `cccmaster` in lowercase |
| Write mode | Upsert |
| Other settings | They exist, but Eduardo did not detail them |

The other Unassigned job seemed to have misconfigured destinations; Eduardo does not consider it important and it must not be touched.

**Readings `(inferred)`:**

- The job exists to write the same data to SQL Server and to Postgres (via Babelfish) and compare, in Todd's test of using SQL Server as a destination (deck, slide 13).
- The schema format of the override follows the way Babelfish represents SQL Server schemas in Postgres (general knowledge).
- "CCC" are the three text columns: Complaint, Cause, Correction of a service order. Free-text fields can contain customer data; that is why rows from the test data are not copied.

## Main features

| Feature | What it does | Note |
|---|---|---|
| Conflict handling (upsert) | If the record already exists, updates it; if not, inserts | See glossary. Key = the job's conflict column (README). **Confirmed in practice on 2026-10-29:** two runs kept 675 rows and a column added afterwards was filled by the `update` action |
| Staged loads | Data first arrives in a staging table and is then merged into the destination | README; there is also a staging schema in Postgres |
| Destination table override | Changes the name (and schema) with which the table arrives at a destination server | Seen in an existing job, 2026-10-28; **the `schema.table` format worked in Eduardo's job (2026-10-29)**; see glossary |
| Change tracking | Detects what changed in the source; it is the synchronization method recommended by Microsoft | Confirmed for **SQL Server**: the agent's account needs the `VIEW CHANGE TRACKING` permission (agent guide). For the other sources, the README only shows the last run variable (`last_run_var`) |
| Scheduling | Manual, once, by interval, daily or by days of the week | Todd; README |
| API fan-out | Queries Postgres to get the list of dealerships and repeats the ingestion for each one | |
| GitHub integration | Snapshots and backup/restore of jobs and pipelines. In the call of 2026-10-27: changes managed via Git for all jobs and clients | README: configuration snapshots with export, apply and restore. The current incremental and historical pipelines have no backup (see gaps) |
| Query Executor | Lets you query the source databases live for auditing | README and dev screen |
| Batching | The agent sends 500 rows per WebSocket message by default | Agent guide |

## Examples of ingested sources

Todd cited as **examples** of sources (not a closed list, according to Eduardo): **QDMS, AutoVance, Communicator, DealerMine, Powerlane, Zoho CRM, Zoho Desk and Carbon60 (BDC data, New Brunswick)**. The Granola note wrote "DealerMind"; the correction to DealerMine was confirmed by Eduardo.

In the call of 2026-10-27, Todd added that **Zoho (CRM and Desk) is internal**, used by Quorum itself, and not a source of customer data; and that **Powerlane** is ingested by **API** (one client, several jobs). Each API client is configured according to the system (Zoho, Veeva and Powerlane differ). For tests there is a **DealerMine MSSQL** database with real exported data.

Relation to coverage by product (Rick's diagram): [products/brands.md](../products/brands.md).

## Where the data lands

In Postgres, in **schemas separated by product**, plus a **staging schema**. See [postgres-and-summaries.md](postgres-and-summaries.md). The deck says ingestion is evolving, including **Microsoft SQL Server as a destination database**; in the call, Todd said the **SQL Server destination is already available** (see [architecture.md](architecture.md)). In the dev version, each job's destination is defined by the destination servers (section above); for the test Postgres, see [postgres-test-environment.md](postgres-test-environment.md).

## Documentation and code

- The code and READMEs of the tool are in the **QAnalytics repositories on GitHub**. Eduardo saw the tool's README (pasted by him on 2026-10-28) and does not intend to expose the repository to the assistant.
- There is a **Word document** from Todd about the ingestion agent process, summarized in [ingestion-agent.md](ingestion-agent.md).
- There is no known documentation of the **DealerMine tables**; Eduardo asked Todd on 2026-10-29 (no answer so far).

## Gaps and open questions

The most important questions about the process (moving from test to production, failures, change tracking for the other sources) are in [ingestion-agent.md](ingestion-agent.md). The rest are here:

- In the deck, "Client" appears as the top of the hierarchy; everything indicates it is the client record linked to the agent by `client_id`, to be confirmed with Todd.
- What does "Unassigned" (jobs without a pipeline) mean and how does it relate to "runs for all clients"? The README says a blank Client ID runs on all clients in the pipeline.
- Did the "DealerMine Test Edu" pipeline need to be assigned to the DM Colo client in the client panel? The job ran without errors, but the step was not recorded and Eduardo did not answer; it remains open.
- Why does a screenshot show 1776 jobs if Todd said 108?
- Security of the WebSocket channel: the Word document shows `wss` (TLS) and a token per client; details on token rotation and revocation are missing.
- What reusable transformation functions exist (deck, "Manipulate")? The production jobs do the transformation inside the `SELECT` (aggregation, null normalization, date and time columns) and use variables; no catalog of functions was seen.
- (low priority) What is Carbon60 and how does it relate to Quorum? BDC may mean "Business Development Center" (inferred; the production jobs deal with contacts and appointments of the scheduling center).
