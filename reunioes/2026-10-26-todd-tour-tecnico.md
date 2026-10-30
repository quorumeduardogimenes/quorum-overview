---
titulo: Tour técnico do Q Analytics com Todd, 2026-10-26
resumo: Nota (Granola) do 1-on-1 em que Todd apresentou ao Eduardo a arquitetura do Q Analytics: ETL próprio, Postgres, Superset, segurança e próximos passos.
atualizado_em: 2026-10-27
fontes: [anotações do Granola da reunião com Todd, coladas pelo Eduardo em 2026-10-27; a reunião não foi transcrita por Eduardo; deck "Analytics Platform" do Todd, enviado pelo Eduardo em 2026-10-27 (usado nas correções)]
status: confirmado
---

# Tour técnico do Q Analytics com Todd, 2026-10-26

- **Data:** 2026-10-26 (informada pelo Eduardo)
- **Participantes:** Eduardo e Todd (IT Manager / Infrastructure Manager, cerca de 20 anos na Quorum)
- **Tema:** arquitetura e ferramentas do Q Analytics (migração de Synapse/Power BI para Postgres/Superset)
- **Formato:** exploratório. Eduardo vai digerir o conteúdo e voltar com perguntas pontuais.
- **Esta nota é fonte primária:** o texto bruto abaixo (gerado pelo Granola, em inglês) não é reescrito. Correções vão na seção "Correções posteriores".
- **Observação:** a reunião não foi gravada/transcrita pelo Eduardo; o Granola gerou as notas.

## Onde cada fato foi documentado

| Assunto | Arquivo |
|---|---|
| Visão geral da stack, fluxo dos dados, hospedagem, conteúdo do deck do Todd | [q-analytics/arquitetura.md](../q-analytics/arquitetura.md) |
| Ferramenta de ingestão (ETL próprio) | [q-analytics/pipeline-ingestao.md](../q-analytics/pipeline-ingestao.md) |
| Postgres, réplica, pg_cron e tabelas de resumo | [q-analytics/postgres-e-resumos.md](../q-analytics/postgres-e-resumos.md) |
| Superset, RLS, Descope, customizações, MCP com Claude | [q-analytics/superset-e-seguranca.md](../q-analytics/superset-e-seguranca.md) |
| Stack anterior, 5 meses de migração, custo | [q-analytics/migracao-powerbi-superset.md](../q-analytics/migracao-powerbi-superset.md) |
| Fontes ingeridas por produto | [produtos/marcas.md](../produtos/marcas.md) |
| Todd (cargo, tempo de casa) | [pessoas/diretorio.md](../pessoas/diretorio.md) |
| Próximos passos (GitHub, Granola, deck) e perguntas para o Todd | [onboarding/primeiras-semanas.md](../onboarding/primeiras-semanas.md) |
| Conceitos técnicos explicados | [glossario.md](../glossario.md) |

## Notas originais (em inglês, sem edição)

Q Analytics platform migration: Synapse to Postgres and Superset with Eduardo

Context and Background

* Onboarding session for Eduardo Gimenes, new hire at Quorum
* Todd walked Eduardo through the full Q Analytics platform architecture and tooling
* Meeting was exploratory: Eduardo to digest and follow up with targeted questions

Migration from Power BI to Open-Source Stack

* Previous stack: Synapse + SQL Server data warehouse + Power BI Fabric
   * Ingested from Salesforce, Q Cloud (public/private), Azure, and on-premise dealership servers
   * Costly to run, maintain, and deliver; incremental syncs not in place
* New stack: custom ETL tool + Postgres + Apache Superset
   * Migrated in ~5 months, self-hosted in Quorum's Calgary colocation data center
   * Near-zero compute/storage cost vs. previous environment
   * Significant cost savings overall

Data Ingestion Tool (Custom ETL)

* Built in-house: no off-the-shelf tool met security and flexibility requirements
* Clients deployed per dealership or environment; connect back to central server via WebSockets
   * Bypasses firewall whitelisting (previously a security risk with Synapse Agent)
* Pipeline orchestration model: clients > pipelines > jobs
   * 256 dealership databases managed; jobs run once across all associated clients
   * Supports SQL, API, and custom-configured endpoints
* Key features:
   * Conflict handling (upsert logic), staged loads, change tracking (Microsoft-recommended sync method)
   * Job scheduling: per-minute, hourly, daily, workday-specific, or one-time
   * API fan-out: queries Postgres for dealer list, loops ingestion per dealer
   * GitHub integration for snapshots, backup/restore of jobs and pipelines
   * Query executor for live auditing of source databases
* Sources ingested: QDMS, AutoVance, Communicator, DealerMind, Powerlane, Zoho CRM, Zoho Desk, Carbon60 (BDC data, New Brunswick)
* Data lands in Postgres under per-product schemas; staging schema also present

Postgres Architecture and Summary Tables

* Primary (writable) + read-only replica; all reports hit the read-only server
* pg_cron jobs run nightly to aggregate raw data into summary tables
   * Incremental refresh only processes new/changed records, not full re-aggregation
   * ETL watermark table tracks last-run state; service data has its own watermark for failed-refresh recovery
* Some raw tables are very large (up to ~425 GB); summary tables exist for performance
   * Example: 2-minute load vs. 2-second load drove the decision to pre-aggregate
* All stored procedures versioned in Git; can be dropped and recreated from repo

Apache Superset and Reporting

* Runs live queries against Postgres (no pre-sync like Power BI)
* Row-level security limits each customer to their own data; passed as user account metadata
* dScope used for SSO/authentication; authorization driven by dealership security table synced into warehouse
   * Only Q Analytics uses dScope today; intent is to roll out across all Quorum products
* Dev environment for building/testing; export-then-import workflow to promote to production
* Custom additions built on top of Superset:
   * Scrollbar/zoom via JavaScript (Superset lacked Power BI-style scrolling)
   * Gauge charts built in Handlebars code
   * Lazy loading per dashboard page with Redis caching
* MCP integration with Claude Desktop: can generate charts and query Postgres directly
   * Useful for chart creation, SQL auditing, and data validation
   * Dashboard creation requires SQL query refinement to ensure data accuracy
   * Recommended workflow: build in Dev with Claude, vet output, export to production

Next Steps

* Send Eduardo a Granola invite and share today's meeting transcript (Todd)
* Share the high-level architecture PowerPoint deck with Eduardo (Todd)
* Create a new GitHub account using quoruminfotech.com email, then send the new ID to Todd via Teams (Eduardo)
Todd will add Eduardo to the Quorum GitHub org directly.
* Point Eduardo to QAnalytics GitHub repos and READMEs for the data ingestion tool (Todd)
Todd to pull or generate a Superset README if documentation is sparse.

## Correções posteriores

- **2026-10-27, Eduardo:** em "Sources ingested", **DealerMind** é erro de transcrição do Granola; o correto é **DealerMine** (marca do diagrama do Rick). Nos demais arquivos a grafia usada é DealerMine.
- **2026-10-27, Eduardo:** a lista "Sources ingested" são **exemplos** de fontes que o Todd deu, não uma lista fechada. A reunião mostrou o Q Analytics e a ferramenta de ingestão por trás, uma plataforma para configurar clientes e agendar pipelines de extração e transformação.
- **2026-10-27, Eduardo:** os itens de "Next Steps" do Todd (convite do Granola, transcrição, deck de arquitetura) **já foram compartilhados** com o Eduardo. O deck foi enviado ao projeto em 2026-10-27 e está transcrito em [q-analytics/arquitetura.md](../q-analytics/arquitetura.md).
- **2026-10-27, Eduardo:** para o número de concessionárias, a referência técnica é o Todd (**256** bancos); o "cerca de 250" do Rick é aproximação.
- **2026-10-27, deck do Todd (slide 13):** **dScope** é erro de transcrição do Granola; o sistema de SSO é o **Descope** (OAuth). O deck acrescenta que o acesso a dashboards e a RLS usam os metadados do login e um security manager customizado do Superset, e que o MCP do Superset também autentica pelo Descope. Nos demais arquivos a grafia usada é Descope.
- **2026-10-27 (nota de quem documentou):** a data da reunião não consta nas notas do Granola; 2026-10-26 foi informada pelo Eduardo.
