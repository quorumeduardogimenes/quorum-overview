---
title: Test Postgres ("test db"): connection through pgAdmin web, databases and Todd's guidance
summary: How Eduardo accesses the test Postgres through pgAdmin 4 web, the three databases (babelfish_db, datamigration, postgres), what Todd said about each one, the state of datamigration, who writes where and Eduardo's own schema, with the first table.
updated_at: 2026-10-29
sources: [Eduardo, 2026-10-28 and 2026-10-29 (connection created with help from another agent in this project, databases seen, read queries on datamigration, schema and first table created, observations in the dev ingestion tool, Todd's guidance relayed), Todd (guidance about the databases, relayed by Eduardo), call with Todd, 2026-10-27 (../meetings/2026-10-27-todd-ingestion-call.md), pgAdmin web access sent by Todd, 2026-10-27 and 2026-10-28]
status: draft
---

# Test Postgres ("test db"): connection, databases and Todd's guidance

This file details the **test destination** mentioned in [ingestion-agent.md](ingestion-agent.md): the Postgres where Eduardo practices. For the production Postgres (primary, replica, summaries), see [postgres-and-summaries.md](postgres-and-summaries.md). The diary of what Eduardo has been doing is in [dev-ingestion-practice.md](dev-ingestion-practice.md).

## In plain words

Think of a shared workshop with three rooms. One of them (`datamigration`) is the room for the data migration test environment, where Eduardo can work. Todd uses the other two for his own experiments migrating data from SQL Server to Postgres. So as not to bump into Todd's work, Eduardo set up his own workbench inside his room: a **schema** (a folder of tables) called `test_edu` and, in the ingestion tool, a **pipeline** of his own.

## How Eduardo accesses it

- **Tool:** **pgAdmin 4 web**, hosted by Quorum. It has its **own login** (pgAdmin username and password) and requires **setting up MFA on first login**. This login is not the Postgres user.
- **Registered connection:** Eduardo registered the test Postgres instance in pgAdmin web under the name **`test db`**, with help from another agent in this project (2026-10-28).
- **pgAdmin address:** the first URL Todd sent was wrong (a typo on his part); he sent the correct one on 2026-10-28. The address, host and passwords are **not kept in this documentation** (see guide, rule 9): they are in Todd's message.
- **How the connections arrived:** since Todd did not give a host or port, Eduardo's reading is that the connection is made from pgAdmin web itself, where the servers already appear registered `(inferred)`.
- **Reach of the account:** the connection user's name resembles that of the `babelfish_db` database `(inferred)`. It may be an account shared with Todd's tests and have reach beyond `datamigration` (not tested). The caution of not touching the other databases applies either way.

## The three databases

When opening `test db`, three databases appear:

| Database | What Todd said | For Eduardo |
|---|---|---|
| `datamigration` | "It's in the test environment" | It is where Eduardo works |
| `babelfish_db` | One of the "other databases", which Todd uses to test migrating from MS SQL to Postgres `(inferred: by elimination, from what he said)`. In the dev ingestion tool, the DM Colo client's Postgres destination server points to it (observed by Eduardo) | Do not touch; it is Todd's test space |
| `postgres` | Same as above `(inferred)` | Do not touch. It is also the default database every Postgres creates (see glossary) |

Todd did not explain what `babelfish_db` is; the name resembles Postgres's Babelfish feature for talking to SQL Server databases (see [glossary.md](../glossary.md)), and the shape of the schema used in the DM Colo Postgres destination (source database prefix followed by `_dbo`) is compatible with how Babelfish represents SQL Server schemas `(inferred, general knowledge, not confirmed at Quorum)`.

## Todd's guidance (original in English and translation)

> "The datamigration database is in the test environment. The other databases there I am using to test migrating from MS sql to postgres. I suggest creating your own schema and job pipeline to separate my work from yours."

In plain terms: the `datamigration` database is in the test environment. Todd is using the other databases there to test migrating from MS SQL to Postgres. He suggests Eduardo create his **own schema and his own job pipeline**, to keep each person's work separate.

Other messages from Todd on the same day, according to Eduardo: he made Eduardo an admin of Superset Dev and passed along the credentials and admin access so that Eduardo could create the `datamigration` destination server in the ingestion tool. Afterwards Eduardo confirmed that Todd **had already created, in Superset Dev, the connection to `datamigration`**.

## State of `datamigration`

On 2026-10-28, read-only queries, run by Eduardo connected to `datamigration` (list in [dev-ingestion-practice.md](dev-ingestion-practice.md)):

- The user and the database were correct.
- **No schemas beyond the four Postgres defaults:** `information_schema`, `pg_catalog`, `pg_toast` and `public`. In other words, the database had no working schemas.
- The user **has permission to create schemas** in the database.
- Eduardo created the **`test_edu`** schema. Nothing is created in `public`, which is everyone's default schema.

On 2026-10-29, Eduardo created in `test_edu` the **`dt_contact_daily`** table (contacts per day, type and direction, coming from the test DealerMine's `DTContactHistory`), with a composite primary key of 5 columns. It is the destination of his first job (details in [dev-ingestion-practice.md](dev-ingestion-practice.md)).

## Who writes where (observed in the dev ingestion tool)

- The destination servers that already existed point to `babelfish_db` (a Postgres) and to a SQL Server database; details in [ingestion-pipeline.md](ingestion-pipeline.md).
- According to Eduardo, **no active pipeline or job wrote to `datamigration`** before his practice. So the hypothesis that the DM Colo client wrote to `datamigration` **was not confirmed**: DM Colo wrote to `babelfish_db` and to SQL Server (Todd's tests).
- `datamigration` became the destination of Eduardo's practice, through a new destination server created by him (DMColoPGTestEdu). On 2026-10-29, his first job started writing to `test_edu.dt_contact_daily`.

## What this means in practice

1. **Work only in `datamigration`**, in the `test_edu` schema. The other two databases are Todd's.
2. **Own pipeline and jobs** in the ingestion tool, without changing the existing ones, reinforcing the recommendation from the 2026-10-27 call (see [ingestion-agent.md](ingestion-agent.md)).
3. **Destination tables created beforehand**, in pgAdmin: the job form warns that destination tables must already exist.
4. **Keep the usual precautions:** the tool's X can delete jobs, and credentials never go into the documentation.
5. **Real data:** the test source database has real exported data; do not copy rows into conversations or documents.

## Gaps and open questions

- Resolved on 2026-10-28: can Eduardo create a schema in `datamigration` (yes); is there a naming convention? (none was given; Eduardo adopted `test_edu`).
- Resolved on 2026-10-29: the table must exist before the job (the form says so). The override field accepts the name with schema (`schema.table`) according to the README; Eduardo's job uses this format.
- Is `datamigration` the same as the data migration environment and the dev of the ingestion tool? Todd only said that `datamigration` is in the test environment.
- Eduardo's question to Todd about "test db" and "data migration db" being the same thing got only the answer above; the relationship with the DM Colo client today is: it does not write to `datamigration` (except through Eduardo's job).
- What is `babelfish_db` and what is the goal of the MS SQL to Postgres migration Todd is testing? Is it related to SQL Server as an ingestion destination or to the DealerMine data migration?
- Is there also a read-only connection to the production Postgres (read-only user that Todd sent) and has it already been registered in pgAdmin web?
