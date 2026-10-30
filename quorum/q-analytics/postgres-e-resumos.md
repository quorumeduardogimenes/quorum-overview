---
titulo: Postgres, réplica de leitura e tabelas de resumo
resumo: Como o Postgres do Q Analytics é organizado: primário e réplica, esquemas, camadas de detalhe, modelos e resumo, jobs noturnos pg_cron e watermarks.
atualizado_em: 2026-10-27
fontes: [reunião com Todd, 2026-10-26 (../reunioes/2026-10-26-todd-tour-tecnico.md), deck "Analytics Platform" do Todd (slide 8, enviado em 2026-10-27)]
status: rascunho
---

# Postgres, réplica de leitura e tabelas de resumo

## Em palavras simples

Imagine uma biblioteca com milhões de fichas (dados brutos, algumas tabelas chegam a ~425 GB). Procurar uma resposta ficha por ficha leva minutos. Então, toda noite, uma rotina preparada confere só as fichas novas ou alteradas e atualiza uma "folha de resumo" já somada. Os dashboards leem a folha de resumo e respondem em segundos. Além disso, existem duas cópias do banco: uma onde se escreve e outra só para leitura, usada pelos relatórios, para que a leitura nunca atrapalhe a escrita.

## Arquitetura

- **Primário (gravável) + réplica somente leitura.** Todos os relatórios consultam a **réplica** (Todd).
- Os dados brutos chegam em **esquemas por produto**, mais um esquema de **staging** (ver [pipeline-ingestao.md](pipeline-ingestao.md)).

## Três camadas de dados (deck, slide 8)

O deck separa o armazenamento analítico detalhado dos modelos otimizados para relatório:

| Camada | O que é |
|---|---|
| Detail & history | Dados de origem transformados, retidos em estruturas do warehouse que permitem rastreabilidade e profundidade analítica |
| Analytical models | Tabelas "prontas para o negócio" que organizam dimensões, medidas e lógica de relatório comuns ou específicas do domínio |
| Scheduled summary aggregation | Processamento agendado no banco que atualiza tabelas de resumo compactas, para que os dashboards não varram repetidamente os dados transacionais detalhados |

O deck resume: o PostgreSQL é tanto o warehouse durável quanto a camada de cálculo, perto dos dados.

## Tabelas de resumo

- Existem para **desempenho**: algumas tabelas brutas têm até cerca de **425 GB**.
- Exemplo dado pelo Todd: carregar um relatório em **2 minutos** contra **2 segundos** com tabela pré-agregada motivou a decisão de pré-agregar.
- Para a Quorum isso é a parte "pré-agregada" que o Rick mencionou; o restante mantém detalhe em nível de transação (ver [visao-geral.md](visao-geral.md)).

## Atualização noturna com pg_cron

- Jobs **pg_cron rodam toda noite** e agregam os dados brutos em tabelas de resumo.
- **Atualização incremental:** só processa registros novos ou alterados, sem reagregar tudo.
- Uma **tabela de watermark do ETL** guarda o estado da última execução. Os **dados de serviço** têm um watermark próprio, usado para **recuperar de atualizações que falharam**.

## Código versionado

- **Todos os stored procedures são versionados no Git**; podem ser apagados e recriados a partir do repositório (Todd).

## Lacunas e dúvidas

- Quais são as tabelas de resumo e a que relatórios atendem (catálogo)?
- Como as camadas "detail & history", "analytical models" e "summary" se mapeiam nos esquemas reais do Postgres?
- Qual o horário da execução noturna e quanto tempo leva? O que acontece se falhar?
- Como o watermark de "service data" difere do geral, e por que só ele tem recuperação própria?
- Quanto de dado cada esquema por produto ocupa? Há política de retenção/arquivamento das tabelas grandes (~425 GB)?
- Como é a replicação (streaming? atraso típico) e há alta disponibilidade?
- Em qual repositório estão os stored procedures e como se faz o deploy?
- Com o Microsoft SQL Server como novo destino (deck), as tabelas de resumo também serão replicadas lá?
