---
titulo: Reunião de onboarding com Rick (CTO), 2026-10-26
resumo: Nota da primeira reunião com o Rick: stack e arquitetura de relatórios, plano da primeira semana e horário de trabalho.
atualizado_em: 2026-10-27
fontes: [notas do Eduardo na reunião de 2026-10-26, diagrama do Rick (2026-10-27)]
status: confirmado
---

# Reunião de onboarding com Rick (CTO), 2026-10-26

- **Data:** 2026-10-26 (segunda-feira)
- **Participantes:** Eduardo e Rick (CTO da Quorum)
- **Tema:** stack e arquitetura de relatórios; plano da primeira semana; horário de trabalho
- **Esta nota é fonte primária:** o texto bruto abaixo não é reescrito. Correções vão na seção "Correções posteriores".

## Onde cada fato foi documentado

| Assunto | Arquivo |
|---|---|
| O que é o Q Analytics, RLS, acesso, agregação | [q-analytics/visao-geral.md](../q-analytics/visao-geral.md) |
| Migração Power BI + Synapse para Superset + Postgres, custo, responsáveis | [q-analytics/migracao-powerbi-superset.md](../q-analytics/migracao-powerbi-superset.md) |
| Marcas, produtos, cores de cobertura, infraestrutura | [produtos/marcas.md](../produtos/marcas.md) |
| Rick, Todd, Sam, Michael | [pessoas/diretorio.md](../pessoas/diretorio.md) |
| Horário, fuso, check-in de quarta | [empresa/modo-de-trabalho.md](../empresa/modo-de-trabalho.md) |
| Plano de hoje, desta semana e da próxima | [onboarding/primeiras-semanas.md](../onboarding/primeiras-semanas.md) |

## Ações combinadas

- Rick envia o diagrama e as notas da call e agenda um check-in na quarta (30 min).
- Eduardo faz 1-on-1 com Todd (30 a 60 min) hoje ou amanhã para a arquitetura.
- Eduardo acompanha Sam nos relatórios existentes (3 a 4 horas) nesta semana.
- Na semana seguinte, Eduardo começa o epic de novas stories de relatórios do Michael.

## Notas originais (em inglês, sem edição)

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

## Correções posteriores

- **2026-10-27, diagrama do Rick:** as quatro marcas são Quorum DMS, **DealerMine**, Autovance e Accessible Accessories. O **Powerlane** não é marca, é produto da marca Quorum DMS; nas notas originais ele aparece no lugar do DealerMine. As cores da legenda, no diagrama, significam: roxo = dados fluindo no Q Analytics, amarelo = progresso inicial, vermelho = pouco ou nenhum dado. Detalhes em [produtos/marcas.md](../produtos/marcas.md).
- **2026-10-26, fuso (Eduardo):** o time está no Canadá (maioria em MT), não nos EUA. Ver [empresa/modo-de-trabalho.md](../empresa/modo-de-trabalho.md).
