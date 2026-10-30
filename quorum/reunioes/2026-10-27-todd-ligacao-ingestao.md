---
titulo: Ligação com Todd sobre o fluxo de ingestão e a implantação de clientes, 2026-10-27
resumo: Nota (Granola) de uma ligação em que Todd explicou ao Eduardo o ambiente de teste da ferramenta de ingestão, como um cliente (agente) é implantado, como os jobs e os clientes de API são configurados e por onde o Eduardo deve começar a praticar.
atualizado_em: 2026-10-27
fontes: [anotações do Granola da ligação com Todd, coladas pelo Eduardo em 2026-10-27; mensagem do Todd no Teams, antes da ligação, colada pelo Eduardo (ver "Correções posteriores"); relato do Eduardo sobre o contexto (acessos enviados pelo Todd, aviso sobre o X) e esclarecimentos; documento Word "QAnalytics Data Ingestion Agent 2.docx" do Todd, enviado pelo Eduardo em 2026-10-27 (resumido em ../q-analytics/agente-de-ingestao.md)]
status: confirmado
---

# Ligação com Todd sobre ingestão de dados, 2026-10-27

- **Data:** 2026-10-27 (confirmada pelo Eduardo).
- **Participantes:** Eduardo e Todd (IT Manager / Infrastructure Manager).
- **Contexto (Eduardo):** o Todd enviou os acessos que o Eduardo pediu e o **avisou para ter cuidado ao mexer na ferramenta de ingestão, porque clicar no X pode deletar jobs**. Aproveitando, o Eduardo perguntou se existe uma instância de dev da ferramenta e como funciona, em linhas gerais, a criação de novos fluxos de ingestão. O Todd respondeu primeiro por mensagem no Teams e depois ligou (a mensagem está em "Correções posteriores").
- **Esta nota é fonte primária:** o texto bruto abaixo (gerado pelo Granola, em inglês) não é reescrito. Correções vão em "Correções posteriores".
- **Observação:** o Eduardo disse que deixou passar alguns detalhes da conversa por causa do inglês; por isso a documentação detalhada está nos arquivos de assunto.

## Onde cada fato foi documentado

| Assunto | Arquivo |
|---|---|
| Ingestão de ponta a ponta, cliente = agente, implantação em 4 passos, guia técnico do agente (Word), versões dev e ambientes de teste, jobs e Git, clientes de API, sandbox do Eduardo | [q-analytics/agente-de-ingestao.md](../q-analytics/agente-de-ingestao.md) |
| Modelo clientes > pipelines > jobs, recursos, resumo e links | [q-analytics/pipeline-ingestao.md](../q-analytics/pipeline-ingestao.md) |
| Ambientes (dev, teste, migração de dados), destino SQL Server disponível, backup | [q-analytics/arquitetura.md](../q-analytics/arquitetura.md) |
| Ideia de desfazer a exclusão de jobs pelo X | [q-analytics/ideias-de-melhoria.md](../q-analytics/ideias-de-melhoria.md) |
| Fontes: Zoho (interno), Powerlane (API), banco de teste do DealerMine | [produtos/marcas.md](../produtos/marcas.md) |
| Todd (equipe de infraestrutura implanta agentes; fornece acessos) | [pessoas/diretorio.md](../pessoas/diretorio.md) |
| Próximos passos e situação das perguntas para o Todd | [onboarding/primeiras-semanas.md](../onboarding/primeiras-semanas.md) |
| Termos novos (agente, container, pgAdmin, MSSQL, pipelines incremental e histórico, sandbox) | [glossario.md](../glossario.md) |

## Notas originais (em inglês, sem edição)

Data ingestion workflow and client deployment setup with Todd

Test Environment Architecture

* Single client on the test server, running in a container on the same server
* Clients in production connect back to only one server, so testing requires full environment recreation
* Security features prevent cross-environment client connections (e.g., can’t hit a DealerMind or dealership server client from outside)

Jobs and Configuration

* 108 incremental and production jobs exist, took ~6 hours to create
* Changes managed via Git across all jobs and clients
* Database is backed up, but jobs can be added or deleted
* Post-deployment, configuration work is focused on jobs, not the client itself
   * Once a client is deployed, the team just connects back to it

Client Deployment Process

* Team does not touch dealership or production servers directly
* Process:
   1. Client provides username and password for their environment
   2. Infrastructure team deploys the agent
   3. Team sets up the database connection once the agent appears in the tool
   4. Pipelines are then assigned to the agent

API Clients and Internal Tools

* API tool integration is a work in progress
* Each API client config is driven by the specific client’s requirements (Zoho, Veeva, Powerlane all differ)
* Zoho is internal (CRM + Desk), not a customer-facing data source
   * Multiple jobs can run against a single configured Zoho client
* Same model applies to Powerlane: one client, multiple jobs with different API calls

Sandbox Access and Next Steps

* DM Colo client in the data migration environment connects to a test DealerMind MSSQL database with exported real client data
* Recommended: create a new personal pipeline and jobs rather than modifying existing ones
   * Existing incremental and historical pipelines have no backup and are a work in progress
* Word document available outlining the data ingestion agent process
* Todd to send Postgres credentials and IP address for the test server (can be added via pgAdmin 4)
* SQL Server destination available; Todd to provide a table if needed

Follow-Up

* Send outstanding questions to Todd via Microsoft Teams (Eduardo)Explore the tool and repo first, then compile questions after digesting the materials.
* Share Postgres credentials and server IP address (Todd)Needed so Eduardo can connect via pgAdmin 4 and see the destination server.
* Create a personal pipeline and jobs in the data migration environment (Eduardo)Use the DealerMind MSSQL test database to practice SQL queries and explore the tool hands-on.

## Correções posteriores

- **2026-10-27, Eduardo:** **DealerMind** é erro de transcrição do Granola; o correto é **DealerMine**. Nos demais arquivos a grafia usada é DealerMine.
- **2026-10-27, Eduardo:** a ligação aconteceu em 2026-10-27 (a data não consta nas notas).
- **2026-10-27, Eduardo:** os **108 jobs** são o total (incrementais e de produção).
- **2026-10-27, Eduardo:** o **Veeva** não é prioridade agora; fica apenas citado como mais um cliente de API com configuração própria.
- **2026-10-27, Eduardo:** os dois primeiros itens de "Follow-Up" têm o detalhe da ação grudado no nome do responsável no texto do Granola ("(Eduardo)Explore the tool...", "(Todd)Needed so..."); são detalhes de cada ação, não frases à parte.
- **2026-10-27, Eduardo (complemento, mensagem anterior à ligação, no Teams):** antes de ligar, o Todd tentou responder por mensagem. Texto original, em inglês:
  - Eduardo: "This is actually a question I have: there is no Dev instance for this data ingestion tool, right? All decisions and implementations are reviewed and made directly in production, correct?"
  - Todd: "I have dev versions of the app running in a container on the internal network that I test changes with. The issue for some jobs is that any client will only be registered with a single server so new clients would need to be deployed when testing."
  - Todd: "It would be better to use a dev version is you are playing around, but that version is behind the firewall."
  - Efeito: **existe versão dev** (a premissa do Eduardo de que tudo era feito em produção não procede). Documentado em [q-analytics/agente-de-ingestao.md](../q-analytics/agente-de-ingestao.md).
