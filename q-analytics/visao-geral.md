---
titulo: Q Analytics, visão geral
resumo: O que é o Q Analytics, para quem foi feito, como é acessado e qual o objetivo de expansão.
atualizado_em: 2026-10-29
fontes: [reunião com Rick, 2026-10-26 (../reunioes/2026-10-26-rick-kickoff.md), diagrama do Rick (2026-10-27), reunião com Todd, 2026-10-26 (../reunioes/2026-10-26-todd-tour-tecnico.md), deck "Analytics Platform" do Todd (2026-10-27), conversa com Sam, 2026-10-29 (../reunioes/2026-10-29-sam-regras-de-negocio.md)]
status: rascunho
---

# Q Analytics, visão geral

## O que é

Ferramenta **principal de relatórios** da Quorum, construída para as concessionárias que usam o **Quorum DMS**. Roda sobre **Apache Superset + Postgres** (open source), desde a migração concluída no início de outubro de 2026 (ver [migracao-powerbi-superset.md](migracao-powerbi-superset.md)). Os dados chegam por uma ferramenta de ingestão feita em casa e o conjunto roda em servidores da própria Quorum, em Calgary (ver [arquitetura.md](arquitetura.md)). O Todd a descreve, no deck, como uma **plataforma reutilizável** de analytics, pensada para ser implantada em vários produtos.

## Quem usa e como

- Cerca de **250 concessionárias** usam o Q Analytics (Rick; o Sam falou em ~240 clientes); a ferramenta de ingestão gerencia **256 bancos de concessionárias** (Todd, referência técnica). Comparação em [clientes/concessionarias.md](../clientes/concessionarias.md).
- **Row-level security (RLS):** cada concessionária enxerga somente os próprios dados. O filtro é passado como metadado da conta do usuário e o login é feito por **Descope** (SSO), com um security manager customizado no Superset (ver [superset-e-seguranca.md](superset-e-seguranca.md)).
- Os dashboards são acessados **diretamente pelo Superset**; alguns podem ser **incorporados (embedded) no DMS**, de forma opcional.

## Uso interno e benchmarking (Sam)

No Power BI havia dois tenants: o **Q Analytics** (voltado ao cliente) e o **Q Insights** (interno). O Superset reúne os dois num só servidor, um tenant e um login. O lado interno (equivalente ao Q Insights) permite **comparar clientes entre si**: por exemplo, dizer a uma concessionária GM de uma província qual é a média de veículos vendidos no mês. A diretoria da Quorum vê os dados de todos os clientes. (Relato do Sam; se o painel interno já existe no Superset ou está planejado não foi dito.)

## Dados

- Parte dos dados é **pré-agregada** (tabelas de resumo atualizadas toda noite por pg_cron) para ganhar desempenho; parte mantém o **detalhe em nível de transação**. Detalhes em [postgres-e-resumos.md](postgres-e-resumos.md).
- A ingestão é feita por uma ferramenta própria, em **Go** segundo o Rick, construída por Todd (e Justin, segundo o Sam) (ver [pipeline-ingestao.md](pipeline-ingestao.md) e [pessoas/diretorio.md](../pessoas/diretorio.md)). Antes, era preciso entrar em cada servidor de cliente; agora agentes enviam os dados para o Postgres central.

## Objetivo e cobertura atual

Expandir a cobertura do Q Analytics para **todos os produtos**, não só o Quorum DMS. Hoje, segundo o diagrama do Rick, os dados fluem para 4 produtos da marca Quorum DMS (Quorum DMS, PowerLane, Communicator e Sales Planner); os produtos de DealerMine e Autovance estão em progresso inicial; o Forecast Tool e o Accessible Accessories têm pouco ou nenhum dado. Tabela completa em [produtos/marcas.md](../produtos/marcas.md). O Descope, usado hoje só aqui, deve ser estendido a todos os produtos (Todd). O deck traz um roteiro de 8 passos para integrar um novo produto (ver [arquitetura.md](arquitetura.md)).

## Responsáveis conhecidos

- Todd: pipeline de ingestão e arquitetura (referência técnica do Q Analytics).
- Sam: team lead de dados e reporting; autor de mais de 70% dos relatórios Power BI originais, que foram migrados; passa para o time de suporte na semana de 2026-11-02 (Rick; a conciliar com o cargo atual).
- Michael: dono do epic de novas stories de relatórios, que o Eduardo começa a atacar na semana de 2026-11-02.

## Para aprofundar

[arquitetura.md](arquitetura.md), [pipeline-ingestao.md](pipeline-ingestao.md), [postgres-e-resumos.md](postgres-e-resumos.md), [superset-e-seguranca.md](superset-e-seguranca.md), [negocio/dominio-concessionaria.md](../negocio/dominio-concessionaria.md) (regras de negócio por trás dos relatórios) e o [glossário](../glossario.md).

## Lacunas e dúvidas

Dúvidas técnicas ficam em cada arquivo específico. Pendentes no nível geral:

- Como funciona o embed no DMS (mecanismo de autenticação, quais dashboards)?
- Quantos relatórios/dashboards existem hoje e onde há catálogo deles?
- O painel interno com benchmarking entre clientes já existe no Superset? Quem na Quorum o usa além da diretoria?
- Quais relatórios usam tabelas de resumo e quais leem transações?
