---
titulo: Migração de Power BI + Azure Synapse para Superset + Postgres
resumo: Histórico, motivação financeira, datas, stack anterior e responsáveis pela migração da stack de relatórios da Quorum.
atualizado_em: 2026-10-29
fontes: [reunião com Rick, 2026-10-26 (../reunioes/2026-10-26-rick-kickoff.md), reunião com Todd, 2026-10-26 (../reunioes/2026-10-26-todd-tour-tecnico.md), conversa com Sam, 2026-10-29 (../reunioes/2026-10-29-sam-regras-de-negocio.md)]
status: rascunho
---

# Migração de Power BI + Azure Synapse para Superset + Postgres

## Resumo

A Quorum migrou sua camada de relatórios de **Power BI + Azure Synapse** para **Apache Superset + Postgres**, ambos open source. A migração foi concluída no início de outubro de 2026 e levou cerca de **5 meses** (Todd).

## Evolução da plataforma de relatórios (Sam)

Em ordem cronológica, segundo o Sam:

1. **Flex Reports:** relatórios customizados em Excel.
2. **Power BI:** conectando-se aos servidores das concessionárias. Havia **dois tenants**: o **Q Analytics** (voltado ao cliente) e o **Q Insights** (interno).
3. **Postgres central + Superset:** a ferramenta de ingestão (construída por Todd e, segundo o Sam, Justin) usa agentes ligados a cada cliente para levar os dados ao Postgres central. Isso **substitui entrar manualmente em cerca de 240 servidores**. O Superset reúne Q Analytics e Q Insights em **um servidor, um tenant e um login**.

## Antes e depois

| Aspecto | Antes | Depois |
|---|---|---|
| BI / visualização | Power BI (Fabric), dois tenants (cliente e interno) | Apache Superset, um tenant |
| Armazenamento/processamento | Azure Synapse + data warehouse SQL Server | Postgres (primário + réplica de leitura) |
| Ingestão | Synapse (agente nas concessionárias, que exigia liberação de firewall) | Ferramenta própria, clientes conectados via WebSockets |
| Fontes ingeridas | Salesforce, Q Cloud (público/privado), Azure e servidores locais das concessionárias | Ver [pipeline-ingestao.md](pipeline-ingestao.md) |
| Sincronização | Sem sincronização incremental | Upsert, change tracking e atualização incremental |
| Hospedagem | Azure | Autohospedado no data center de colocation da Quorum em Calgary |
| Custo | cerca de US$ 22.000 a 24.000 por mês (mais de US$ 250 mil por ano) | apenas custo de servidores (Rick); computação e armazenamento perto de zero (Todd) |
| Licenciamento | proprietário | open source |

Observação: as notas usam "$"; assumido dólar (inferido). "Hospedagem: Azure" para o ambiente anterior é inferido a partir de Synapse e Azure serem fontes/serviços citados; confirmar.

Segundo o Todd, a stack anterior era cara de operar, manter e entregar. O novo ambiente representa economia significativa.

## Linha do tempo

- Antes do Power BI: Flex Reports em Excel (Sam; sem datas).
- Power BI: relatórios construídos ao longo de cerca de **10 anos**, majoritariamente por Sam (mais de 70%).
- Migração: cerca de **5 meses** de duração (Todd), concluída há cerca de **26 dias** da reunião de 2026-10-26, isto é, por volta de 2026-09-30, "início de outubro" segundo o Rick. Início aproximado em maio de 2026 (inferido, por conta).

## Quem fez

- **Todd:** construiu a ferramenta de ingestão (em Go, segundo o Rick); as notas dizem que ele cuidou de cerca de 70% da construção da migração. O Sam cita também o **Justin** como co-construtor da ferramenta (nome a confirmar).
- **Sam:** autor de mais de 70% dos relatórios originais em Power BI; Todd e Sam fizeram a migração dos relatórios para Postgres/Superset.

## Lacunas e dúvidas

- Qual a divisão exata de trabalho entre Todd e Sam? As notas dizem que Todd cuidou de ~70% da construção da migração e que "Todd & Sam fizeram a migração"; confirmar.
- Quem é o Justin e qual foi o papel dele?
- Todos os relatórios do Power BI foram migrados? Há algum descartado ou pendente?
- Houve período de execução em paralelo? O Power BI/Synapse já foi desligado?
- Quais foram os principais problemas e aprendizados da migração?
- Qual o custo mensal atual dos servidores em Calgary?
- O que é "Q Cloud (público/privado)" e o Salesforce no ambiente anterior? Continuam como fontes?
- Quando o Power BI substituiu os Flex Reports, e o que aconteceu com os relatórios em Excel dos clientes?
