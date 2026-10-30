---
title: Glossary (technical and business)
summary: Technical concepts and dealership business terms that come up in meetings, explained in plain English, separating the general explanation from what Quorum actually does.
updated_at: 2026-10-29
sources: [meeting with Todd, 2026-10-26 (meetings/2026-10-26-todd-technical-tour.md), call with Todd, 2026-10-27 (meetings/2026-10-27-todd-ingestion-call.md), Todd's Word document about the agent (q-analytics/ingestion-agent.md), Todd's "Analytics Platform" deck (2026-10-27), meeting with Rick, 2026-10-26 (meetings/2026-10-26-rick-kickoff.md), conversation with Sam, 2026-10-29 (meetings/2026-10-29-sam-business-rules.md), Eduardo (test Postgres, guidance from Todd, exploration of the dev version of the ingestion tool, the tool's README and screenshots, 2026-10-28; first job in dev ingestion and test dashboard in Superset Dev, 2026-10-29: q-analytics/dev-ingestion-practice.md and q-analytics/superset-and-security.md), general technical knowledge of whoever wrote the documentation]
status: draft
---

# Glossary (technical and business)

This is the documentation's **single glossary**: it brings together the technical terms (data, infrastructure) and the terms of the dealership business and the DMS. (The former `glossario-negocio.md` was merged into this file on 2026-10-29.)

How to read each entry:

- **General** is general-knowledge explanation (it did not come from a meeting and applies to any company).
- **At Quorum** is what someone at the company said, with the source indicated. If something is an assumption, it is marked `(inferred)`.
- Terms stay in English, as they are said in meetings, with a translation or explanation where it helps.

Alphabetical order. New terms are added here whenever they come up in meetings (see [00-documentation-guide.md](00-documentation-guide.md)). Business context is in [business/dealership-domain.md](business/dealership-domain.md).

## Agent

- **General:** a program installed close to a data source that runs tasks requested by a central server and sends the result back.
- **At Quorum:** the program that the infrastructure team installs on the dealerships' servers for the ingestion tool; it runs as a Windows service, calls the central server and receives the jobs (Todd, call of 2026-10-27; Todd's Word document). In the tool, the agent is linked to a "client" through the `client_id`. See [q-analytics/ingestion-agent.md](q-analytics/ingestion-agent.md).

## API

- **General:** a way for one system to ask another for data through an agreed interface, instead of accessing the database directly.
- **At Quorum:** one of the sources the ETL supports, besides SQL and custom endpoints (Todd). Each API client is configured according to the system (Zoho, Powerlane and Veeva differ). See [q-analytics/ingestion-pipeline.md](q-analytics/ingestion-pipeline.md).

## API fan-out

- **General:** taking a list of items and making the same call for each one ("fanning out").
- **At Quorum:** the ETL queries Postgres to get the list of dealerships and repeats the API ingestion for each one (Todd).

## Babelfish

- **General:** a feature that makes a Postgres database "understand" the protocol and the T-SQL dialect of SQL Server, so that applications built for SQL Server can talk to Postgres without a full rewrite. It is commonly used in migrations from SQL Server to Postgres. With this feature, a SQL Server schema usually shows up in Postgres as a schema with a compound name: the name of the SQL Server database, an underscore and the schema name (for example, `<database>_dbo`). (General definition from whoever wrote the documentation.)
- **At Quorum:** there is a database called `babelfish_db` on the test Postgres instance. Todd only said that the other databases there (besides `datamigration`) are used by him to test the migration from MS SQL to Postgres; the relationship between the name and the Babelfish feature was not confirmed `(inferred)`. In the dev ingestion tool, the Postgres destination server of the DM Colo client points to `babelfish_db`, and a job writes there to a table with a schema in that format `(inferred)`. See [q-analytics/postgres-test-environment.md](q-analytics/postgres-test-environment.md) and [q-analytics/ingestion-pipeline.md](q-analytics/ingestion-pipeline.md).

## BDC

- **General:** at dealerships, it usually stands for "Business Development Center", the department that handles contacts and leads (inferred).
- **At Quorum:** Todd mentioned "Carbon60 (BDC data, New Brunswick)" as an example of a source. Exact meaning to be confirmed. The public website also lists "BDC" as a solution area. DealerMine's production jobs deal with contacts and appointments from the appointment center, which seems to be that department `(inferred)`.

## Benchmarking (comparing customers)

- **General:** comparing a company's performance with that of others in the same group or segment.
- **At Quorum:** the internal side of Q Analytics (equivalent to Q Insights) makes it possible to compare customers, for example telling a GM dealership in a province what the average number of vehicles sold in the month is (Sam). Whether the dashboard already exists in Superset is an open question. See [q-analytics/overview.md](q-analytics/overview.md).

## Body shop

- **General:** a shop for bodywork repair and painting.
- **At Quorum:** a department that not every dealership has; it has its own set of tables in the DMS, which also posts to accounting (Sam).

## Bootstrap token (enrollment token)

- **General:** a temporary, single-use credential used only to register a program for the first time; afterwards it is exchanged for a permanent credential.
- **At Quorum:** generated in the server dashboard, in the client's "Deploy" dialog, and pasted into the agent's `config.json`; on first start, the agent exchanges it for a permanent token and deletes the bootstrap one (Todd's Word document). See [q-analytics/ingestion-agent.md](q-analytics/ingestion-agent.md).

## Cache and Redis

- **General:** caching means storing an answer that was already computed so it does not have to be computed again. Redis is an in-memory database widely used for this.
- **At Quorum:** Superset uses Redis for caching, together with lazy loading of the dashboard pages (Todd). See [q-analytics/superset-and-security.md](q-analytics/superset-and-security.md).

## Change tracking

- **General:** a SQL Server feature that records what changed in each table, so that only the differences are synchronized. Whoever reads it needs a specific permission (`VIEW CHANGE TRACKING`).
- **At Quorum:** the ETL uses it; Todd says it is the synchronization method recommended by Microsoft. Before, there were no incremental syncs (Todd). The agent's guide requires this permission on the agent's database account (Todd's Word document).

## Client, pipeline and job (in the ETL)

- **At Quorum:** the ETL's orchestration model: **clients** > **pipelines** > **jobs** (Todd). A job runs once for all the associated clients. The deck adds the execution modes (scheduled, per client, per pipeline, per job, manual/recovery). In the call of 2026-10-27: the program installed at the dealership is the **agent** (linked to the client record through the `client_id`); once the client is deployed, configuration concentrates on the jobs; and for an API client (Zoho, Powerlane) one client has several jobs with different calls. According to the tool's README, pipelines are global entities, assigned to clients in the client dashboard, and a job with a blank Client ID runs on all the clients of the pipeline. In the dev version (Eduardo, 2026-10-28), jobs that are not in any pipeline appear grouped as **Unassigned**. In the 2026-10-29 practice session, Eduardo created a job inside his personal pipeline and the job ran without errors. Details in [q-analytics/ingestion-pipeline.md](q-analytics/ingestion-pipeline.md) and [q-analytics/ingestion-agent.md](q-analytics/ingestion-agent.md). Note: "client" in the tool is the agent's record; for the business, the customer is the dealership (see [customers/dealerships.md](customers/dealerships.md)).

## Colocation (co-lo)

- **General:** the company has its own servers inside a third-party data center, which provides space, power, cooling and network.
- **At Quorum:** Q Analytics runs self-hosted in the colocation data center in Calgary (Todd). The name "DM Colo" for the client of the data migration environment probably refers to this `(inferred)`. Sam also mentioned "Colo" when talking about access to the DMS test server (see "QIS Connector").

## Config snapshot

- **General:** a saved copy of a system's configuration at a given moment, which makes it possible to roll back or to carry the configuration from one environment to another.
- **At Quorum:** the ingestion tool keeps configuration snapshots (pipelines, pipeline-to-client assignments and jobs; not credentials or the "last run" marks) and lets you export, apply and restore them via GitHub (README). Applying a snapshot overwrites the current configuration. Whether it is the official path for promoting from test to production is an open question (Round 3, 1). See [q-analytics/ingestion-pipeline.md](q-analytics/ingestion-pipeline.md).

## Conflict handling / upsert

- **General:** "upsert" = update + insert. If the record already exists, it is updated; if it does not exist, it is inserted. It avoids duplicating data when the same load runs again. To know whether the record already exists, a **key** is needed (a PRIMARY KEY or UNIQUE on the destination table).
- **At Quorum:** the ETL has conflict handling with upsert logic (Todd; deck, slide 6 "Persist"). In the job, the key is the **conflict column** and the action on conflict is `nothing` (ignore) or `update` (update); the column must match the destination's PRIMARY KEY or UNIQUE (README). **Confirmed in practice (Eduardo, 2026-10-29):** two runs of the same job kept the same number of rows in the destination (no duplicates), and, after adding a column to the `SELECT` and running again, the new column was filled in on the existing rows by the `update` action. See [q-analytics/dev-ingestion-practice.md](q-analytics/dev-ingestion-practice.md).

## Container

- **General:** a lightweight package that carries a program and everything it needs to run, isolated from the rest of the server (Docker is the best-known example).
- **At Quorum:** the client of the ingestion tool's test server runs in a container on the same server (Todd, call of 2026-10-27).

## Cookie cutter

- **General:** an English expression for something made with the same mold, identical in every copy.
- **At Quorum:** Sam's analogy: all dealerships have the **same schema** of database (same tables and fields), but each with its own data and its own server. See [customers/dealerships.md](customers/dealerships.md).

## Database vs. schema

- **General:** a Postgres server can contain several **databases**, isolated from one another, and each database contains several **schemas** (folders of tables). Every new Postgres comes with a database called `postgres`, used as the default for administration and the initial connection. A new schema starts out empty, and the `public` schema is the shared default.
- **At Quorum:** the test Postgres instance has three databases: `babelfish_db`, `datamigration` and `postgres`. `datamigration` belongs to the test environment, where Eduardo works, and Todd suggested that he create his own schema there (Todd, via Eduardo, 2026-10-28). Eduardo created the `test_edu` schema the same day. See [q-analytics/postgres-test-environment.md](q-analytics/postgres-test-environment.md).

## Dataset, metric, chart and dashboard (in Superset)

- **General:** in Apache Superset, the **dataset** is the table (or query) that Superset sees; the **metric** is a reusable measure (for example, sum of sales), which can be a custom SQL expression (Custom SQL); the **chart** is an individual graph (the **Big Number** shows a large number, with configurable subtitle and format); the **dashboard** gathers several charts and filters on one page. After changing the source table (for example, adding a column), the dataset's columns must be synchronized ("Sync columns from source"). To copy an existing dashboard, use "Save as".
- **At Quorum:** the deck describes the chain Postgres > datasets > metrics > charts > dashboards (slide 9). In the 2026-10-29 practice session, Eduardo built in Superset Dev a dataset on `test_edu.dt_contact_daily`, 10 charts and a dashboard with two filters (version 1), with English names and the prefix "DM Colo - ". See [q-analytics/superset-and-security.md](q-analytics/superset-and-security.md).

## Dealer (the "Dealer" database)

- **At Quorum:** a database present on every customer server, used as the entry database; dealer groups have additional per-store databases, named after the store (Sam). See [customers/dealerships.md](customers/dealerships.md).

## Dealer group

- **General:** a company that controls several dealerships.
- **At Quorum:** large customers, with several stores and brands, also called enterprise groups (Sam). The website has an enterprise accounts area (leadership in [people/directory.md](people/directory.md)).

## Descope (SSO and OAuth)

- **General:** Descope is a third-party authentication service (identity provider) that offers login, SSO and OAuth. (General definition; Quorum's deck only names it.)
- **At Quorum:** Descope, through OAuth, provides the SSO for Q Analytics and also authenticates access via MCP (deck, slide 13). Only Q Analytics uses it today; the intention is to extend it to all products (Todd). **Correction:** Granola wrote "dScope" in the meeting note; Todd's deck confirms the spelling **Descope**. See [q-analytics/superset-and-security.md](q-analytics/superset-and-security.md).

## Destination server

- **General:** in ingestion tools, it is the registration of a database where data can be written, with the database type (for example, SQL Server or PostgreSQL), the server and the database. A job chooses which destination servers it writes to.
- **At Quorum:** the ingestion tool has a **Destination Servers** tab. In the dev version there were three before 2026-10-28 (two SQL Server and one PostgreSQL pointing to `babelfish_db`), and Eduardo created a fourth, DMColoPGTestEdu, for `datamigration` (Eduardo, 2026-10-28). A job can write to more than one (up to 8, each with its own override table and columns). The form warns that the destination tables must already exist. See [q-analytics/ingestion-pipeline.md](q-analytics/ingestion-pipeline.md).

## Destination table override

- **General:** a job setting that changes the name (and, if the destination accepts it, the schema) under which the table is written, instead of using the same name as the source.
- **At Quorum:** seen in an existing dev job: the source table `dbo.CCCMaster` is written to the Postgres destination with a different schema (the source database prefix followed by `_dbo`) and a lowercase name (Eduardo, 2026-10-28). The job's Table field also accepts schema.table (README). **Confirmed in practice (2026-10-29):** Eduardo's job writes to his destination server with an override in the `schema.table` format (schema `test_edu`) and ran without errors.

## DMS (Dealer Management System)

- **General:** the system a dealership uses to run sales, service, parts and accounting.
- **At Quorum:** the Quorum DMS is, in Sam's description, accounting software that receives data from all areas into a shared ledger. See [products/quorum-dms.md](products/quorum-dms.md).

## DMS agnostic

- **At Quorum:** Sam said that AutoVance is "DMS agnostic": it works with the Quorum DMS or with competitors' DMSs. See [products/brands.md](products/brands.md).

## ETL

- **General:** Extract, Transform, Load: extract data from the source, transform it (clean, adjust) and load it into the destination.
- **At Quorum:** done by an in-house tool, written in Go (Todd; Rick). See [q-analytics/ingestion-pipeline.md](q-analytics/ingestion-pipeline.md).

## F&I (Finance and Insurance)

- **General:** the dealership's business office that handles financing, insurance and paperwork when a vehicle is sold.
- **At Quorum:** a stage of the customer journey, after the sales appointment (Sam). The Autovance Menu presents the F&I menu (see [products/brands.md](products/brands.md)).

## Firewall and whitelist

- **General:** the firewall is the network barrier that controls who comes in and goes out. A whitelist is the list of allowed addresses.
- **At Quorum:** the new ETL avoids having to open addresses in the dealerships' firewalls (something that used to be a security risk with the Synapse agent, according to Granola) because the client connects back, from the inside out, via WebSockets (Todd). The agent's guide confirms that only outbound access to ports 443 (`wss`) or 80 (`ws`) is needed.

## Fixed ops and variable ops

- **General:** *fixed operations* are service and parts (recurring revenue); *variable operations* are vehicle sales (which varies more).
- **At Quorum:** the website uses these terms to describe what the DMS covers ([products/brands.md](products/brands.md)).

## Flat rate vs. hourly rate

- **General:** with *flat rate*, the service has a standard time and a fixed price, even if the mechanic takes more or less time; with hourly pricing, the time spent is charged.
- **At Quorum:** Sam mentioned both models, with an example of US$200/hour.

## Flex Reports

- **At Quorum:** custom Excel reports that preceded Power BI in the history of Quorum's reports (Sam). See [q-analytics/powerbi-to-superset-migration.md](q-analytics/powerbi-to-superset-migration.md).

## Git and GitHub

- **General:** Git keeps the version history of files; GitHub hosts Git repositories.
- **At Quorum:** stored procedures are versioned in Git and can be recreated from the repository; the ETL uses GitHub for snapshots and for backup/restore of jobs and pipelines; changes to jobs and clients are managed via Git; there is a Quorum organization on GitHub (Todd). According to Todd (via Eduardo, 2026-10-29), the queries and procedures of DealerMine's production jobs are also on GitHub.

## Handlebars

- **General:** a template language that builds text/HTML from data. In Superset, the Handlebars chart receives the result of a query (for example, in "Raw" mode, rows without aggregation) and draws it with an HTML/CSS template written by the chart's author; for this reason Superset applies a sanitization that can strip part of the HTML/CSS.
- **At Quorum:** the gauge charts were built in Handlebars code in Superset (Todd). Eduardo plans to use Handlebars on 2026-10-30 to replace the test dashboard's Big Numbers with KPI cards that compare against the previous month (see "MTD / same-period comparison" and [q-analytics/superset-and-security.md](q-analytics/superset-and-security.md)). The effect of the sanitization in Quorum's version is an open question.

## Help desk

- **General:** an internal support team that receives requests for access and for help.
- **At Quorum:** Sam indicated that access to the DMS test environment can be requested from Todd or the help desk. Who staffs the help desk and how it is reached are open questions (see [company/ways-of-working.md](company/ways-of-working.md)).

## Incremental and historical pipelines

- **General:** an **incremental** pipeline loads only what changed since the last run; a **historical** one loads the past all at once (initial load or reload).
- **At Quorum:** they were mentioned in the call of 2026-10-27, with the information that the existing ones have no backup and are work in progress. The general definition above is an assumption about what the term means `(inferred)`; see [q-analytics/ingestion-agent.md](q-analytics/ingestion-agent.md). In dev, the DM Colo client has the DealerMine Historical and DealerMine Incremental pipelines, which Eduardo describes as Todd's tests with SQL Server (Eduardo, 2026-10-28).

## information_schema

- **General:** a set of views that every standard SQL database maintains with the database's own "catalog": which schemas, tables and columns exist. It is used to query the structure with `SELECT`, without changing anything (for example, `information_schema.schemata` lists the schemas and `information_schema.tables` lists the tables). The name is the same in SQL Server and in Postgres.
- **At Quorum:** used by Eduardo on 2026-10-28 to list the schemas of `datamigration` before creating his own; on the source SQL Server it is used to list tables and columns through the Query Executor (2026-10-29: a list of about 1790 tables from DealerMine's test database). See [q-analytics/dev-ingestion-practice.md](q-analytics/dev-ingestion-practice.md).

## Jira and story points

- **General:** Jira is a work management tool; story points are a relative measure of the effort of a story (task).
- **At Quorum:** Sam said that, when the scope changes, the preference is to document it and update the story points in Jira. See [company/culture-and-expectations.md](company/culture-and-expectations.md).

## Last run variable (`last_run_var`)

- **General:** a value that the job saves when it finishes (for example, the most recent date it read) and uses in the next run to read only what is new. It is the simple form of incremental load.
- **At Quorum:** a job field in the ingestion tool, with an initial value and three ways of updating: by timestamp, by an expression over the destination, or by a script on the source (README). It is not included in snapshots. See [q-analytics/ingestion-pipeline.md](q-analytics/ingestion-pipeline.md).

## Lazy loading

- **General:** loading only what the user is going to see right now, and the rest on demand.
- **At Quorum:** lazy loading per dashboard page in Superset, with caching in Redis (Todd).

## Ledger, journal entries and reconciliation

- **General:** the ledger records all financial movements; a journal entry is a record in it; reconciliation checks whether the records match reality (for example, the bank).
- **At Quorum:** done inside the DMS, which brings together the categories sales, cost, expenses, other income and assets/liabilities (Sam).

## MCP (Model Context Protocol)

- **General:** a standard that lets an AI assistant, such as Claude, use external tools (query a database, create charts).
- **At Quorum:** Superset has MCP support, with authentication through Descope (deck); Todd showed the integration with Claude Desktop, to create charts, audit SQL and validate data. Recommended flow: build in Dev, check, export to production.

## MSSQL (Microsoft SQL Server)

- **General:** short for Microsoft SQL Server, Microsoft's relational database (the dialect is T-SQL).
- **At Quorum:** DealerMine's test database in the data migration environment is MSSQL, with real customer data exported (Todd, call of 2026-10-27). SQL Server also appears as a destination database already available in ingestion.

## MTD / same-period comparison

- **General:** MTD (*month to date*) is the month's accumulated total up to today. In the **same-period** comparison, the current partial month is compared with the previous month **up to the same day**, so as not to compare half a month with a whole month. The change in counts and averages is expressed as a **percentage**; for an indicator that is already a percentage (for example, the share of a contact type), **percentage points** are used (the difference between the two percentages).
- **At Quorum:** rule adopted by Eduardo for the v2 test dashboard (accepted on 2026-10-29, to be built on 2026-10-30): the cutoff is the last day with data in the current month, and the previous month is cut off at the same day. See [q-analytics/superset-and-security.md](q-analytics/superset-and-security.md).

## Native filter (Superset)

- **General:** a Superset dashboard filter, created in the dashboard screen and linked to a column of one or more datasets. The types used in practice were **Value** (list of values of a column) and **Time range** (date interval). When the time filter is linked, Superset may warn about temporal columns, but the filter worked.
- **At Quorum:** Eduardo's v1 dashboard (2026-10-29) has two native filters: contact type and period. In v2, the default period filter becomes the current month. See [q-analytics/superset-and-security.md](q-analytics/superset-and-security.md).

## OAuth

- **General:** a standard protocol for one system to confirm who the user is (or to give access to an app) without sharing the password; it is the basis of many "sign in with..." logins.
- **At Quorum:** Descope uses OAuth for Q Analytics SSO (deck, slide 13).

## Over-the-counter

- **General:** a sale made directly to the customer at the counter.
- **At Quorum:** the sale of parts to the end customer, in contrast to the internal sale of parts to service, which has a much larger volume (Sam).

## pgAdmin 4

- **General:** a free graphical application for connecting to a Postgres database, browsing the tables and running queries. It exists as a program installed on the computer and also as a **web** version, hosted on a server, with its own login and MFA.
- **At Quorum:** Todd said that the test server can be added in pgAdmin 4 (call of 2026-10-27). On 2026-10-28 he sent the access to Quorum's **pgAdmin 4 web** (own login, with MFA on first access) and Eduardo registered the test instance as `test db`. See [q-analytics/postgres-test-environment.md](q-analytics/postgres-test-environment.md).

## pg_cron

- **General:** a Postgres extension that schedules tasks inside the database itself, like a cron.
- **At Quorum:** pg_cron jobs run every night and aggregate the raw data into summary tables (Todd). See [q-analytics/postgres-and-summaries.md](q-analytics/postgres-and-summaries.md).

## Plugins (automatic messages)

- **At Quorum:** Sam called "plugins" the automatic messages sent to the team (for example, a parts request) and to customers (for example, an extended warranty reminder); the Communicator supports them. The exact technical meaning of the term is open (see [business/dealership-domain.md](business/dealership-domain.md)).

## Postgres: primary and read replica

- **General:** the primary accepts reads and writes; the replica is a synchronized, read-only copy that takes load off the primary.
- **At Quorum:** all reports query the replica (Todd).

## Pre-aggregation and summary tables

- **General:** computing totals and sums beforehand and storing them in a smaller table, so the query is fast.
- **At Quorum:** they exist because some raw tables reach ~425 GB; the example was 2 minutes versus 2 seconds of loading time (Todd). DealerMine's production jobs also aggregate at the source, with `GROUP BY`, and load only the result (see [q-analytics/ingestion-pipeline.md](q-analytics/ingestion-pipeline.md)); Eduardo's first job follows the same pattern.

## Q Analytics

- **At Quorum:** Quorum's main reporting tool, built on Superset + Postgres; customer-facing (dealerships). In Power BI it was one of two tenants, alongside Q Insights (Sam). See [q-analytics/overview.md](q-analytics/overview.md).

## Q Insights

- **At Quorum:** the internal Power BI tenant, alongside Q Analytics (customer-facing); it made it possible to see the data of all customers. With the migration, Superset brings the two together on one server, one tenant and one login (Sam). See [q-analytics/overview.md](q-analytics/overview.md).

## QIS Connector

- **At Quorum:** Sam said that the DMS test server (ZZZ509) is accessed "via QIS Connector / Colo". What the connector is and its relationship to colocation were not explained (to be confirmed with Sam or Todd). See [products/quorum-dms.md](products/quorum-dms.md).

## Query Executor

- **General:** a screen in a tool that lets you run a query directly against a database, without leaving the tool.
- **At Quorum:** a feature of the ingestion tool for live querying of a client's source database, for auditing (Todd; README). In Eduardo's practice, it is used to explore DealerMine's test SQL Server with only `SELECT` limited by `TOP`. See [q-analytics/dev-ingestion-practice.md](q-analytics/dev-ingestion-practice.md).

## Repair order (RO) and work order (WO)

- **General:** a document that records a repair service done on a vehicle.
- **At Quorum:** the terms are equivalent: the DMS says "work order" and the CRM says "repair order" (Sam).

## Ribbon

- **General:** the bar of buttons grouped by module at the top of a program.
- **At Quorum:** in the Quorum DMS, the ribbon shows the modules service, parts, vehicles, accounting and CRM (Sam, in the demo). See [products/quorum-dms.md](products/quorum-dms.md).

## Row-level security (RLS)

- **General:** a rule that filters the rows each user can see in the same table.
- **At Quorum:** it limits each dealership to its own data; the filter comes as metadata on the user's account (Todd) and is handled by a custom Superset security manager (deck). Sam adds that within the dealership there is role-based access and that management sees everything. See [q-analytics/superset-and-security.md](q-analytics/superset-and-security.md).

## Sandbox

- **General:** an isolated environment where you can experiment without affecting what is in production.
- **At Quorum:** Todd pointed to the data migration environment as Eduardo's sandbox, with the "DM Colo" client linked to DealerMine's MSSQL test database; the recommendation is to create personal pipelines and jobs, without changing the existing ones (call of 2026-10-27). On the test Postgres, Todd also suggested a schema of his own in `datamigration` (2026-10-28), and Eduardo created the `test_edu` schema and the "DealerMine Test Edu" pipeline. On 2026-10-29, the first job of that pipeline loaded the `test_edu.dt_contact_daily` table and Eduardo built a test dashboard in Superset Dev on top of it. For the DMS, there is a safe test server to explore (Sam). See [q-analytics/ingestion-agent.md](q-analytics/ingestion-agent.md), [q-analytics/postgres-test-environment.md](q-analytics/postgres-test-environment.md) and [q-analytics/dev-ingestion-practice.md](q-analytics/dev-ingestion-practice.md).

## Schedule "Manual only"

- **At Quorum:** a job scheduling option in the ingestion tool: the job only runs when someone clicks Run (▶). It is the choice for Eduardo's test jobs. Note: a job with **no schedule at all** runs every time the client connects (README). See [q-analytics/ingestion-pipeline.md](q-analytics/ingestion-pipeline.md).

## Schema

- **General:** a "folder" inside the database that groups tables.
- **At Quorum:** data lands in schemas per product, plus a staging schema (Todd). On the test Postgres, Eduardo has his own schema `test_edu` (see "Database vs. schema").

## Scope creep

- **General:** gradual growth of what has to be delivered, without an adjustment to the deadline or effort.
- **At Quorum:** handled case by case, without a rigid formal process: document the change, update the story points in Jira and resolve it (Sam). See [company/culture-and-expectations.md](company/culture-and-expectations.md).

## Security manager (Superset)

- **General:** the Superset component that decides who can see what (dashboards, datasets, rows). It can be replaced by a customized version in code.
- **At Quorum:** there is a custom security manager that uses the login metadata to control access to dashboards and RLS (deck, slide 13).

## Shadowing

- **General:** following an experienced person while they work, in order to learn by observing.
- **At Quorum:** Rick arranged 3 to 4 hours of shadowing for Eduardo with Sam on the existing reports; Sam said that Eduardo can follow him while he works (see [onboarding/first-weeks.md](onboarding/first-weeks.md)).

## SSO (single sign-on)

- **General:** signing in once and being recognized by several systems.
- **At Quorum:** provided by Descope in Q Analytics (Todd, deck).

## Staging and staged loads

- **General:** an intermediate area where data arrives and is validated before going to the final destination.
- **At Quorum:** the ETL does staged loads: with a conflict key, the data goes to an intermediate table and is merged into the destination (README); there is also a staging schema in Postgres (Todd).

## Stored procedure

- **General:** a block of SQL code stored and executed inside the database.
- **At Quorum:** all of them are versioned in Git and can be deleted and recreated from the repository (Todd).

## Synapse, SQL Server and Power BI Fabric

- **General:** Azure Synapse is Microsoft's data warehouse/analytics service; SQL Server is Microsoft's relational database; Power BI is Microsoft's BI tool and Fabric is the platform that integrates it.
- **At Quorum:** it was the previous stack (Synapse + SQL Server data warehouse + Power BI Fabric), expensive to operate and maintain (Todd). See [q-analytics/powerbi-to-superset-migration.md](q-analytics/powerbi-to-superset-migration.md). The deck says SQL Server is being added as a **destination** database of the new ingestion, and Todd said in the call of 2026-10-27 that it is already available.

## System Control table

- **At Quorum:** a DMS table that Sam mentioned when telling the story of a bulk-change error on a live server, fixed with a backup. The table's exact role was not explained.

## T-SQL

- **General:** the SQL Server dialect of SQL.
- **At Quorum:** appears among the databases used by the products, alongside Postgres and MySQL (Rick).

## Tenant (multi-tenant)

- **General:** a "tenant" is an isolated customer inside a shared system. Multi-tenant: several customers use the same platform, each seeing only their own data.
- **At Quorum:** the deck uses "tenant / client context" as a shared service: it resolves which customer (dealership) the user belongs to, and the data scope follows from that. In Power BI there were two tenants (Q Analytics and Q Insights), now unified in Superset (Sam).

## Vehicle inventory vs. parts inventory

- **At Quorum:** two distinct inventories: the vehicle inventory is what the sales team sells; the parts inventory is used by service and parts (Sam).

## View

- **General:** a SQL query stored in the database under a name, used as if it were a table. It does not hold its own data: it runs the query on every use. It serves to hide complicated logic (for example, window functions such as `LAG`, which fetches the value of the previous row) behind a simple name, which Superset reads as a dataset.
- **At Quorum:** Eduardo plans to create, on 2026-10-30, the view `test_edu.v_contact_kpi_monthly` (one row per month, with the current month's and the previous month's value for each KPI and the change already computed) to feed the v2 dashboard. Only create new, personal objects, never alter the existing ones. See [q-analytics/superset-and-security.md](q-analytics/superset-and-security.md).

## Warehouse (data warehouse)

- **General:** a central database organized for analysis and reporting, fed by several systems.
- **At Quorum:** Postgres plays this role; the dealerships' security table is synchronized to the warehouse (Todd).

## Watermark

- **General:** a mark of the point up to which data has already been processed, so the next run starts from there.
- **At Quorum:** an ETL watermark table stores the last run; the service data has its own watermark to recover from failed updates (Todd). See also "Last run variable".

## WebSockets

- **General:** a connection that stays open in both directions between client and server, instead of isolated requests.
- **At Quorum:** the ETL clients call the central server over WebSockets, so there is no need to open the dealership's firewall (Todd). The agent uses `wss` (WebSocket with TLS) and sends 500 rows per message by default (Todd's Word document).

## Weighted average

- **General:** an average in which each value counts with a weight. To group averages from several days, you do not take the average of the averages: you add up `value × weight` and divide by the sum of the weights. The weight must have **the same universe as the value**: an average computed only over the rows that have a certain date must be weighted by the count of those rows, not by the count of another group. Distinct counts (`COUNT(DISTINCT ...)`) also cannot be summed across days.
- **At Quorum:** error and fix in Eduardo's first dashboard (2026-10-29): the average days to appointment came out below 1 day because the weight was the count of contacts with an appointment (`with_appointment`) and the average only considered the rows with a valid appointment date. The fix was to store the `appt_date_count` column in the job and use the metric `SUM(avg_days_to_appt * appt_date_count) / NULLIF(SUM(appt_date_count), 0)`; the values then fell between 1 and 21 days. See [q-analytics/dev-ingestion-practice.md](q-analytics/dev-ingestion-practice.md) and [q-analytics/superset-and-security.md](q-analytics/superset-and-security.md).

## Windows Credential Manager

- **General:** Windows' own credential vault, where programs store passwords and tokens in a protected way.
- **At Quorum:** the agent stores its permanent token there after enrollment, instead of leaving it as plain text on disk (Todd's Word document).

## Windows Service

- **General:** a program that runs in the background on Windows, starts with the machine and does not need anyone to be logged in.
- **At Quorum:** the ingestion agent is installed as a service (name cited: `QAnalyticsDIClient`), by default as `LocalSystem`, with a recommendation to use a dedicated service account in production; logs go to the Windows Event Log (Todd's Word document).

## Zoho CRM and Zoho Desk

- **General:** Zoho products; CRM manages customer relationships and Desk is a support/help-desk system.
- **At Quorum:** cited by Todd as examples of ETL sources; in the call of 2026-10-27 he clarified that Zoho is **internal** (CRM + Desk), not a customer-facing data source, and that several jobs run against a single configured Zoho client.
