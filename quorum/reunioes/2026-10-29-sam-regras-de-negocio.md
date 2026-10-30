---
titulo: Conversa de 2 horas com Sam (team lead de dados e reporting), 2026-10-29
resumo: Nota (Granola) da conversa em que o Sam explicou a empresa, o negócio das concessionárias, o Quorum DMS, a evolução da plataforma de relatórios e a cultura de trabalho, e mostrou o DMS; parte da conversa foi para se conhecerem.
atualizado_em: 2026-10-29
fontes: [anotações do Granola da conversa com Sam, coladas pelo Eduardo em 2026-10-29 (resumo do Granola, não a transcrição completa); contexto relatado pelo Eduardo]
status: confirmado
---

# Conversa com Sam, 2026-10-29

- **Data:** 2026-10-29 (quinta-feira), duração de cerca de 2 horas.
- **Participantes:** Eduardo e Sam (team lead de dados e reporting, segundo o Eduardo).
- **Contexto (Eduardo):** o Sam explicou muito das regras de negócio e depois abriu o Quorum DMS e mostrou como o sistema é e como o acesso é feito. **Essa parte (telas e acesso) será documentada depois**, quando o Eduardo enviar prints. A conversa **não foi só técnica**: eles também estavam se conhecendo.
- **Esta nota é fonte primária:** o texto bruto abaixo (Granola, em inglês) não é reescrito. Correções vão em "Correções posteriores".

## Como ler estas notas

Como a conversa misturou apresentação pessoal e conteúdo técnico, nos arquivos de assunto os fatos foram separados assim:

- **Fatos da empresa e do sistema** ditos pelo Sam: registrados normalmente, com a fonte.
- **Analogias e proporções ilustrativas** (corpo humano, "ponta do iceberg 10% / 90%", "cookie cutter"): registradas como didáticas, **não como dado medido**.
- **Histórias pessoais e opiniões do Sam** (origem da empresa contada informalmente, erro que cometeu, "é quem você é, não o que você sabe fazer"): registradas como **visão do Sam**, não como política oficial da Quorum, e só no que ajuda o trabalho.

## Onde cada fato foi documentado

| Assunto | Arquivo |
|---|---|
| Departamentos, estoques, modelo de receita, jornada do cliente, WO/RO, preço por hora x flat rate, peças balcão x interno, mensagens automáticas | [negocio/dominio-concessionaria.md](../negocio/dominio-concessionaria.md) |
| Quantidade de clientes, um servidor por cliente, mesmo esquema, grupos de concessionárias, banco "Dealer" | [clientes/concessionarias.md](../clientes/concessionarias.md) |
| DMS como software de contabilidade, módulos, área de CRM, integração com DealerMine, ambiente de teste | [produtos/quorum-dms.md](../produtos/quorum-dms.md) |
| Communicator, Lead Generator, Sales Planner, AutoVance (independente, adquirido), produtos vendidos juntos ou separados | [produtos/marcas.md](../produtos/marcas.md) |
| História da empresa contada pelo Sam | [empresa/visao-geral.md](../empresa/visao-geral.md) |
| Cultura: escopo, estimativas, transparência, stakeholders como clientes | [empresa/cultura-e-expectativas.md](../empresa/cultura-e-expectativas.md) |
| Flex Reports > Power BI > Postgres/Superset, dois tenants (Q Analytics e Q Insights), fim de logar em ~240 servidores | [q-analytics/migracao-powerbi-superset.md](../q-analytics/migracao-powerbi-superset.md) |
| Relatório interno e benchmarking entre clientes | [q-analytics/visao-geral.md](../q-analytics/visao-geral.md) |
| Quem vê o quê (isolamento entre concessionárias, papéis, diretoria vê tudo) | [q-analytics/superset-e-seguranca.md](../q-analytics/superset-e-seguranca.md) |
| Sam, Justin, Terry | [pessoas/diretorio.md](../pessoas/diretorio.md) |
| Jira e story points | [empresa/modo-de-trabalho.md](../empresa/modo-de-trabalho.md) |
| Acesso ao servidor de teste do DMS, próximos passos | [onboarding/primeiras-semanas.md](../onboarding/primeiras-semanas.md) |
| Termos novos (WO/RO, F&I, flat rate, Flex Reports, Q Insights etc.) | [glossario.md](../glossario.md) |

## Notas originais (em inglês, sem edição)

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

## Correções posteriores

- **2026-10-29, conferência com a documentação:** **DealerMind** é erro de transcrição do Granola; o correto é **DealerMine** (já corrigido em 2026-10-27).
- **2026-10-29, a confirmar:** "AutoVance: acquired from Rick" e "built by Justin/Todd" podem ter nomes errados na transcrição; ver "Lacunas e dúvidas" de [produtos/marcas.md](../produtos/marcas.md) e [pessoas/diretorio.md](../pessoas/diretorio.md).
- **2026-10-29, links:** a última linha da tabela "Onde cada fato foi documentado" passou a apontar para o `glossario.md` (o `glossario-negocio.md` foi removido). O texto bruto das notas não foi alterado.
