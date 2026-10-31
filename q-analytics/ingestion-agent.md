---
title: Ingestion agent (client), deployment, test environments and jobs
summary: How ingestion works end to end in practice: the program that sits at the dealership (agent/client), how it is deployed and registers with the server, test environments, how jobs and API clients are configured, precautions when working in the tool, and Eduardo's practice plan.
updated_at: 2026-10-29
sources: [call with Todd, 2026-10-27 (../meetings/2026-10-27-todd-ingestion-call.md), Todd's Teams message, 2026-10-27, before the call, pasted by Eduardo (dev versions), Word document "QAnalytics Data Ingestion Agent 2.docx" (agent deployment guide), sent by Eduardo on 2026-10-27 and summarized here, Eduardo (Todd's warning about the X; clarifications of 2026-10-27; connection to the test Postgres, Todd's guidance, exploration of the dev version, README and screenshots of the tool, 2026-10-28; first end-to-end job on dev, 2026-10-29), meeting with Todd, 2026-10-26 (../meetings/2026-10-26-todd-technical-tour.md)]
status: draft
---

# Ingestion agent (client), deployment, test environments and jobs

This file details one part of the [ingestion tool](ingestion-pipeline.md): the program installed close to the source database, how it is put into operation, where it can be tested and what Eduardo should (and should not) do in the tool. A job's fields, scheduling and snapshots are in [ingestion-pipeline.md](ingestion-pipeline.md). Improvement ideas are in [../improvement-ideas.md](../improvement-ideas.md) (Q Analytics section). The test Postgres (databases, connection and Todd's guidance) is in [postgres-test-environment.md](postgres-test-environment.md), and the diary of Eduardo's practice (objects created, queries, next steps) is in [dev-ingestion-practice.md](dev-ingestion-practice.md).

## In plain words

At each dealership a program is installed: the **agent**. The first time, it registers with the central server using a **temporary single-use token**, generated in the dashboard, and receives a **permanent token**, kept in the Windows credential vault. From then on it calls the central server on its own, stays on the line and waits for orders: "read these tables and send 500 rows per message". The central server also hands it the database password, so nobody needs to leave it written in a file. Whoever configures **what** it does (the jobs) is the data team, directly in the central dashboard; whoever **installs** the agent is the infrastructure team.

## Ingestion end to end (summary view)

Put together from the call with Todd, the Word document and the earlier meetings. Each step has its source indicated.

| Step | What happens | Who | Source |
|---|---|---|---|
| 1. Client access | The dealership (customer) provides a username and password for its environment | Customer | Todd (call) |
| 2. Deploy the agent | The agent is installed as a Windows service and registers with the server using a single-use token | Infrastructure team | Todd; Word |
| 3. Connect to the database | When the agent shows up in the tool, the connection to the dealership's database is configured | Data team | Todd |
| 4. Assign pipelines | Pipelines are assigned to the agent; each pipeline has jobs | Data team | Todd |
| 5. Run | The server sends the jobs, by schedule, by client, by pipeline, by job or manually; the agent reads the local database and sends the rows in batches of 500 by default | Server and agent | Deck; Word |
| 6. Transform and write | The data is mapped, standardized and written to Postgres (upsert, stage loads), in schemas per product | Tool | Deck; Todd (2026-10-26) |
| 7. Summarize | Nightly pg_cron routines aggregate raw data into summary tables | Postgres | Todd (2026-10-26); see [postgres-and-summaries.md](postgres-and-summaries.md) |
| 8. Consume | Superset queries the read replica and builds dashboards, with RLS per dealership | Superset | Todd; deck; see [superset-and-security.md](superset-and-security.md) |

Once the client is deployed, day-to-day work is **in the jobs**, not in the client.

**Walked through in practice on 2026-10-29 (test only):** steps 5, 6 and 8 were done end to end by Eduardo on dev, with a personal job: DealerMine test SQL Server source (aggregated in the `SELECT`) > `test_edu.dt_contact_daily` table in the test Postgres > dataset, charts and dashboard in Superset Dev. Steps 1 to 4 were already in place in the environment (the DM Colo client was already connected). See [dev-ingestion-practice.md](dev-ingestion-practice.md) and [superset-and-security.md](superset-and-security.md).

## Client and agent: the same program?

The sources use both words:

- Todd (call) says the infrastructure team "deploys the **agent**" and that, when it "shows up in the tool", the pipelines are assigned to it; he also talks about production **clients** that "connect back" to the server.
- The Word document calls the program the "client agent" and says that `client_id` identifies "this agent instance" and "must match the **client record** on the server".

A reading that fits both sources: **each dealership or environment has a client record on the server; the program installed there is the agent; the two are linked by the `client_id`**. This matches the clients > pipelines > jobs hierarchy in [ingestion-pipeline.md](ingestion-pipeline.md).

## How a client is deployed (Todd, call)

In the "Client Deployment Process" section of the Granola notes, Todd explains that the team **does not directly touch the dealerships' or production servers**. The process, in four steps:

1. The client (the dealership) provides a **username and password for its environment**.
2. The **infrastructure team deploys the agent**.
3. When the agent **shows up in the tool**, the team configures the **database connection**.
4. The **pipelines are assigned to the agent**.

Granola does not say whose username and password in step 1 it is (a database account or server access). The Word document indicates the agent needs a database account with read permissions, which suggests it is that account `(inferred)`.

## Technical deployment guide (Todd's Word document)

The Word document is a guide to deploying the agent on a **Windows server as a Windows service**. Summary of the content (the file is not archived in the project; the Word document's example passwords were deliberately omitted; small name variations in the Word document are typos, according to Eduardo):

**Prerequisites**

- An already compiled agent executable.
- Administrator rights on the target server.
- Network access to the QAnalytics server's WebSocket endpoint (format `wss://.../worker`).
- An **enrollment (bootstrap) token** generated in the server dashboard, for the first setup.

**Folder and configuration**

- A dedicated folder, for example `C:\Program Files\QAnalyticsDI\`, with the executable and a `config.json`. When running as a service, the executable looks for `config.json` in the same folder.
- Fields of `config.json`:

| Field | What it is for |
|---|---|
| `batch_size` | How many rows go in each WebSocket message. 500 is the recommended default |
| `client_id` | Unique identifier of the agent instance; must match the client record on the server |
| `ws_worker_url` | The server's WebSocket URL (use `wss://`, with TLS) |
| `worker_enroll_bootstrap_token` | Single-use token from the dashboard, for the first enrollment; it is deleted automatically afterwards |

**How the agent registers (bootstrap enrollment)**

1. In the server dashboard, open the client's **Deploy** dialog and copy the bootstrap token, which is pasted into `config.json`.
2. On first start, the agent makes a POST to `/api/worker-enroll` with this token.
3. It receives a **permanent per-client token**.
4. It stores the token in the **Windows Credential Manager** (the Windows credential vault).
5. It **deletes** the bootstrap token from `config.json` and reconnects with the new token.

Subsequent restarts skip the enrollment and authenticate with the stored credential. According to the Word document, nothing is kept in plain text on disk.

**Windows service**

- The executable itself has the commands `install`, `start`, `stop`, `restart` and `uninstall`.
- By default it runs as `LocalSystem`; in production a **dedicated service account** is recommended. This account needs: the "Log on as a service" right; read **and modify** access to the installation folder (the agent rewrites `config.json` after enrollment); access to the Credential Manager; and network access to the source database and the QAnalytics server.
- Logs go to the **Windows Event Log** (Application).

**SQL Server database account**

The agent needs an account (login and user) on the source database with, at a minimum:

- `SELECT` on the `dbo` schema (to read the data);
- `VIEW CHANGE TRACKING` on the `dbo` schema (to follow incremental changes).

If there is data in other schemas, the grant is repeated for them. In "worker" mode (the normal one), **the server sends the database credentials to the agent securely**, without storing them in `config.json`; in **standalone** mode (alternative), environment variables are used.

**Troubleshooting (from the Word document)**

| Symptom | What to check |
|---|---|
| Service does not start | `config.json` exists in the executable's folder and is valid JSON |
| Enrollment fails | The bootstrap token has expired; generate another in the dashboard |
| Connects and drops right away | Network to `ws_worker_url`: port 443 (`wss`) or 80 (`ws`) reachable from the server |
| Access denied | The service account has read/modify access to the installation folder |
| Token not stored | The service account can access the Credential Manager |

**Security that the guide makes clear:** the agent only needs **outbound** access to port 443 (or 80) of the server, which confirms why no inbound firewall opening is needed at the dealership (see [ingestion-pipeline.md](ingestion-pipeline.md)).

## Test environments (Todd)

**Dev versions (Todd's Teams message, before the call):** there is indeed a development environment. Todd keeps **dev versions of the app running in a container on the internal network**, where he tests changes. In other words, not everything is reviewed and deployed straight to production (Eduardo had asked about this because he thought that was how it worked). Two caveats from him:

- For some jobs, since **each client only registers with one server**, **new clients need to be deployed** in order to test.
- To **experiment**, it is **better to use the dev version**, but it sits **behind the firewall** (access only through the internal network).

**On the call (Granola):**

- **Test server:** it has **a single client**, running **in a container on the same server**. It is probably the same dev version described above `(inferred)`.
- **Why testing is hard:** production clients only connect back to **one server**; to test, the **whole environment has to be recreated**.
- **Isolation:** security features prevent a client from another environment from connecting (example: a client on a DealerMine or dealership server cannot be accessed from outside).
- **Data migration environment:** it has a client called **"DM Colo"**, which connects to a **DealerMine test database in MSSQL** with **real exported customer data**. It is the "sandbox" Todd pointed Eduardo to.
- **Test destination:** Todd was going to send **Postgres** credentials and access to the test server; it can be reached through **pgAdmin 4**. **Update (2026-10-28):** Eduardo already accesses the test instance through pgAdmin 4 web (connection `test db`), where the databases `babelfish_db`, `datamigration` and `postgres` appear. `datamigration` is the one in the test environment; Todd uses the other two to test the migration from MS SQL to Postgres, and he suggested Eduardo create his own schema and job pipeline. Details in [postgres-test-environment.md](postgres-test-environment.md).
- **SQL Server destination:** available; if Eduardo needs it, Todd provides a table.

**What Eduardo saw on dev (2026-10-28):** the **DM Colo client shows up in the dev version and is connected**, which is a sign that dev and the data migration environment are the same environment `(inferred, to be confirmed with Todd)`. The existing DM Colo jobs write to Todd's test destinations (a SQL Server and `babelfish_db`), not to `datamigration`; Eduardo created his own destination server (DMColoPGTestEdu) for `datamigration`. Details in [ingestion-pipeline.md](ingestion-pipeline.md) and [dev-ingestion-practice.md](dev-ingestion-practice.md).

## Jobs and configuration (Todd, call; README)

- There are **108 jobs** (incremental and production), which took about **6 hours to create** (the total of 108 was confirmed by Eduardo; a screenshot of the tool showed 1776, to be explained).
- Changes are **managed via Git** across all jobs and clients. According to the README, the tool has **configuration snapshots** (pipelines, client assignments and jobs) with export, apply and restore via GitHub.
- The database is **backed up**, but **jobs can be added or deleted**. Todd warned (via Eduardo) that **clicking the X can delete jobs**.
- The **existing incremental and historical pipelines have no backup** and are still **work in progress**.
- What Todd recommends to Eduardo: **create a personal pipeline and new jobs instead of modifying the existing ones.**

In other words: there is Git and the GitHub integration for snapshots, but the current pipelines have no backup. It may be that the feature exists and is not yet turned on for them `(inferred)`; worth asking Todd later, no rush. For now, the rule is not to delete or change the existing ones.

## API clients and internal tools (Todd, call)

- The **API tool** integration is still **work in progress**.
- Each **API client** is configured according to what that system requires: **Zoho, Veeva and Powerlane differ from one another** (Eduardo decided that Veeva is not a priority right now).
- **Zoho is internal** (CRM + Desk) and is **not a data source for customers**; it is used by Quorum itself. Several jobs can run against **a single Zoho client** that is already configured.
- The same goes for **Powerlane**: **one client, several jobs**, each with different API calls.
- In plain words: you configure the API's "front door" once (the client) and each job is a different call going through it.

## Precautions when working in the tool

- **Clicking the X can delete jobs** (Todd's warning). Before clicking any X, confirm what will be removed and in which environment.
- **Do not modify** existing pipelines and jobs, not even on dev and even as an admin; create your own (see plan below).
- Also avoid **Run All**, **apply snapshot**, **Runtime Capacity**, deleting destination servers and the accounts area.
- A **blank Client ID** on a job makes it run on all clients of the pipeline; always fill it in.
- Practice only in the **test environment** (the dev version, which Todd says is best for experimenting, or the data migration environment with the test database), not in production.
- In the test Postgres, **work only in `datamigration`**, inside **your own schema**; the other databases are Todd's (see [postgres-test-environment.md](postgres-test-environment.md)).
- **Never write credentials** (passwords, tokens, server IPs) in this documentation or in notes; they go through Todd's secure channel.
- The test source database has **real customer data**: do not copy or record rows, and avoid tables with personal data (contacts, names, passwords).

## Eduardo's practice plan (agreed on the call)

Situation as of 2026-10-29; the step-by-step detail is in [dev-ingestion-practice.md](dev-ingestion-practice.md).

1. Explore the tool and the repository first; only then compile the remaining questions and send them to Todd through Microsoft Teams. **In progress:** the dev tool was explored and the README read; the question list was pruned; on 2026-10-29 Eduardo sent Todd the first message (documentation of the DealerMine tables), still unanswered.
2. Receive the test Postgres credentials from Todd and connect through pgAdmin 4. **Done on 2026-10-28** (`test db` connection in pgAdmin web).
3. Create a **schema of his own** in the `datamigration` database, a **destination server** for it and a **personal pipeline** with **jobs** in the data migration environment, using the DealerMine test database (MSSQL) to practice SQL queries. **Done:** schema `test_edu`, destination server DMColoPGTestEdu and pipeline "DealerMine Test Edu" (2026-10-28); **first job completed on 2026-10-29** (contacts per day, type and direction, aggregated at the source; table `test_edu.dt_contact_daily`; ran without errors and the upsert was confirmed). **Next:** new jobs, including incremental load with the Last run variable (see [dev-ingestion-practice.md](dev-ingestion-practice.md)).
4. Use Todd's Word document as a reference for the agent process.
5. Close the loop in Superset Dev, with a dataset, charts and dashboard on the loaded table. **Version 1 done on 2026-10-29; version 2 (Handlebars cards with comparison against the previous month) planned for 2026-10-30** (see [superset-and-security.md](superset-and-security.md)).

## Gaps and open questions

- **Flow in practice:** one job was created and run end to end successfully (2026-10-29). What is left is exploring what has not been used yet: incremental load with the Last run variable, time-based scheduling, several destinations in one job and API jobs.
- **Access to dev:** the dev version sits behind the firewall. How does Eduardo (in Brazil) access it? **Partial:** he already has access; the means was not recorded. It remains to confirm whether the data migration environment is the dev version itself (DM Colo showing up on dev is a sign).
- **From test to production:** configuration snapshots with GitHub seem to be the path `(inferred)`; still to confirm that this is the official process, given that clients only register with one server, and who reviews.
- **Deployment step 1:** is the "username and password for the environment" a database account or access to the dealership's server? Who at the dealership provides it?
- **Client vs. agent:** confirm that the "Client" at the top of the deck (clients > pipelines > jobs) is the client record on the server, and how a client is associated with a job. Eduardo's job ran without errors, but the step of assigning the pipeline to the DM Colo client was not recorded.
- **Job failures:** the tool has Runtime Logs, Data Transfer History and the Run button to run again. Still unknown: alerts and reprocessing a time window.
- **Change tracking:** SQL Server uses `VIEW CHANGE TRACKING`; the README only shows the last-run variable for the other sources (API, Postgres, MySQL). To confirm.
- **API:** what is missing in the "API tool" (work in progress) and which calls are currently configured for Powerlane?
- **Backup:** which database is backed up and why the current pipelines have no backup despite the GitHub integration (low priority).
- **Repository:** Eduardo saw the tool's README, but does not intend to expose the repository to the assistant; which repository holds the agent and the server is still unrecorded.
