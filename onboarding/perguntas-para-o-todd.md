---
titulo: Perguntas para o Todd
resumo: Lista oficial das perguntas ainda em aberto que o Eduardo vai fazer ao Todd, em inglês, mais os exemplos que ele quer ver na prática. Perguntas respondidas são removidas.
atualizado_em: 2026-10-29
fontes: [Claude Doc "Perguntas para o Todd e guia de estudo do Q Analytics" do Eduardo (revisão 16), apagado em 2026-10-27 a pedido do Eduardo depois de seu conteúdo ser copiado para este arquivo; Rodada 3 acrescentada em 2026-10-27 a pedido do Eduardo, a partir da ligação e da mensagem do Todd do mesmo dia; poda de perguntas respondidas em 2026-10-28 a pedido do Eduardo, com base na exploração da dev e no README da ferramenta de ingestão; mensagem ao Todd e dúvida respondida pelo formulário do job, 2026-10-29 (Eduardo)]
status: rascunho
---

# Perguntas para o Todd

Este arquivo é a **fonte** da lista: o documento vivo original (um Claude Doc) foi apagado a pedido do Eduardo em 2026-10-27. As perguntas ficam em inglês porque serão enviadas ao Todd (Microsoft Teams ou reunião). Para alterá-las, siga a seção 7 do [guia](../00-guia-da-documentacao.md).

**Regra da lista:** pergunta respondida sai daqui. A **numeração original das rodadas foi mantida**, então há números faltando de propósito. O que foi removido e por quê está em [primeiras-semanas.md](primeiras-semanas.md), seção "Perguntas de follow-up para o Todd". Contexto técnico em [../q-analytics/agente-de-ingestao.md](../q-analytics/agente-de-ingestao.md), [../q-analytics/pipeline-ingestao.md](../q-analytics/pipeline-ingestao.md) e [../q-analytics/arquitetura.md](../q-analytics/arquitetura.md).

## Rodada 1

2. How is the Postgres warehouse organized? Which schemas hold the detail tables, the analytical models and the summary tables? Is it a star schema?
3. Which dashboards read summary tables and which read transaction-level data? Is there a report catalog or a list of datasets?
4. Are the exported Superset datasets and dashboards stored in Git, and who reviews them?
5. How is row-level security applied in practice? Which field identifies the dealership, and is the filter only in the Superset security manager, or also in Postgres?
6. Are the dashboards templated by product, with predefined dashboards for each product, or can customers have their own customized reports?

## Rodada 2

1. SQL Server as a destination database: what is the use case, which products need it, and when? (I can already see the SQL Server destination servers in Dev.)
2. Job and pipeline configuration is covered by the snapshots and GitHub. How are the Postgres data and the Superset configuration backed up? What is the disaster recovery plan?
3. Besides SQL Server change tracking, do the other sources (APIs, Postgres, MySQL) rely only on the last run variable of the job to pick up changes?
4. When a scheduled job fails, is there any alert, or do we have to check the Runtime Logs? How do we rerun it for a time window?
5. If a pg_cron refresh fails, how do we rerun it and how do we get alerted?
6. How are roles and permissions defined: in Descope, in Superset or in the dealership security table? Who maintains that table, and how is a new user added?

## Rodada 3 (surgidas depois da ligação de 2026-10-27)

1. Is the configuration snapshot with GitHub export/apply the official way to promote a pipeline or job from Dev to Production, given that each client is registered with only one server? Who reviews it?
2. The Dev version is behind the firewall. How should I access it from Brazil? Is the data migration environment (the "DM Colo" client) the same as the Dev version?
3. In the client deployment process, step 1 says the client provides a username and password for their environment. Is that a database account or access to the dealership server?

## Exemplos sugeridos

It would help me a lot to see these in practice. Could you show them live, or record a short video?

- Could you show me one client (dealership) with its pipelines and jobs? Then, if a second dealership uses the same source system, is it enough to associate it with the existing job?
- Could you walk me through one simple dashboard going from Dev to Production: export, import and validation?
- Could you open the same dashboard with users from two different dealerships, so I can see how row-level security works?
- Could you pick one KPI and trace it back to its source: Superset dataset, Postgres table, pg_cron summary and source system?

## Já enviado ao Todd, sem resposta (fora das rodadas)

- **2026-10-29, mensagem em inglês pelo Teams:** existe documentação das tabelas do DealerMine? O Eduardo disse que está montando pipeline e jobs próprios com o cliente DM Colo, listou tabelas que achou interessantes (`DTContactHistory`, `DTContactList`, `DTSalesProduct`, `DMVehicleDetail`, `ServiceHistMaster`) e explicou que muitas das 1790 tabelas estão vazias no teste. Quando o Todd responder, atualizar esta seção e [../q-analytics/pratica-ingestao-dev.md](../q-analytics/pratica-ingestao-dev.md).

## Lacunas e dúvidas

- A ideia de desfazer a exclusão de jobs pelo X não entrou na lista (é uma ideia de melhoria, ver [../q-analytics/ideias-de-melhoria.md](../q-analytics/ideias-de-melhoria.md)). Incluí-la se o Eduardo pedir.
- Dúvidas novas da prática que ainda não viraram pergunta: atribuição do pipeline ao cliente no painel do cliente (o primeiro job vai mostrar), "Unassigned" e a diferença entre 1776 e 108 jobs. Candidata nova: o que significa o tipo de contato `Turned Off`, que responde por cerca de 75% de `DTContactHistory` no banco de teste. O Eduardo decide se viram uma Rodada 4.
- A dúvida "a tabela de destino precisa existir antes do job?" foi **respondida em 2026-10-29** pelo próprio formulário do job ("Target tables must already exist") e saiu da lista.
