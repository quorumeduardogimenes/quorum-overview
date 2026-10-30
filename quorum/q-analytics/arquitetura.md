---
titulo: Q Analytics, arquitetura
resumo: Visão técnica de ponta a ponta do Q Analytics: de onde vêm os dados, como entram, onde ficam, como viram dashboards, serviços compartilhados, onde tudo roda (inclusive ambientes de teste) e transcrição do deck de arquitetura do Todd.
atualizado_em: 2026-10-28
fontes: [reunião com Todd, 2026-10-26 (../reunioes/2026-10-26-todd-tour-tecnico.md), ligação com Todd, 2026-10-27, e mensagem dele no Teams do mesmo dia (../reunioes/2026-10-27-todd-ligacao-ingestao.md), deck "Analytics Platform" do Todd (QAnalytics.pptx, slides datados 2026-10-13/14, enviado pelo Eduardo em 2026-10-27, transcrito aqui), reunião com Rick, 2026-10-26 (../reunioes/2026-10-26-rick-kickoff.md), Eduardo (esclarecimentos, 2026-10-27; exploração da versão dev e do Postgres de teste, 2026-10-28)]
status: rascunho
---

# Q Analytics, arquitetura

## Em palavras simples

Pense numa fábrica de relatórios. Em cada concessionária (e em alguns sistemas da própria Quorum) existe um banco de dados com os dados do dia a dia. Um programa leva esses dados, de tempos em tempos, até um grande armazém central (Postgres), arrumando-os no caminho. No armazém, rotinas noturnas preparam resumos prontos, para que consultar seja rápido. Na frente, o Superset desenha os dashboards, e cada concessionária só enxerga os próprios números. Tudo isso roda em servidores da própria Quorum, em um data center em Calgary.

O ponto central do deck do Todd: isso foi desenhado como uma **plataforma reutilizável**. Constrói-se uma vez e cada produto novo só traz suas fontes, regras, modelos e dashboards.

Termos técnicos em [glossario.md](../glossario.md). O passo a passo da ingestão, de ponta a ponta, está em [agente-de-ingestao.md](agente-de-ingestao.md).

## Fluxo de dados (deck, slide 4)

O deck descreve seis etapas, mais um bloco de serviços compartilhados:

| Etapa | Nome no deck | Conteúdo |
|---|---|---|
| 1 | Source systems | Bancos privados, bancos em nuvem, SaaS e APIs de produtos |
| 2 | Ingestion | Conectores seguros, endpoints de API, configurações customizadas |
| 3 | Transformation | Mapeamento e datas, funções, insert/upsert |
| 4 | PostgreSQL | Warehouse de detalhe, modelos analíticos, tabelas de resumo |
| 5 | Superset | Datasets, charts, dashboards |
| 6 | Customers | Identidade, papéis (roles), escopo dos dados |

**Serviços reutilizáveis da plataforma:** autenticação, agendamento (scheduling), monitoramento, contexto de tenant/cliente e administração.

```
Fontes ──► Ingestão + Transformação ──► Postgres primário ──► Postgres réplica ──► Superset ──► Clientes
(bancos,   (clientes > pipelines >       (escrita)             (somente leitura)    (consultas   (identidade,
 APIs)      jobs, via WebSockets)                                                    ao vivo)     papéis, RLS)

Serviços compartilhados: autenticação (Descope) · agendamento · monitoramento · contexto do cliente · administração
```

A estrutura vem do deck; "primário/réplica" e "WebSockets" vêm da reunião com o Todd. O esquema em texto é uma representação feita por quem documentou (inferido na forma).

## Componentes

| Camada | Componente | Detalhe | Arquivo |
|---|---|---|---|
| Conectividade | Conectores de banco e API; agente instalado na origem | Bancos atrás de firewall, vários endpoints de API combinados, endpoints configurados sob medida | [pipeline-ingestao.md](pipeline-ingestao.md), [agente-de-ingestao.md](agente-de-ingestao.md) |
| Transformação e orquestração | Motor de pipelines configurável | Mapeamento, datas, funções, insert, upsert, resolução de conflito, agendamento | [pipeline-ingestao.md](pipeline-ingestao.md) |
| Armazenamento | Postgres primário + réplica de leitura | Detalhe e histórico, modelos analíticos, tabelas de resumo atualizadas por pg_cron | [postgres-e-resumos.md](postgres-e-resumos.md) |
| Visualização | Apache Superset | Datasets, métricas, charts, dashboards; consultas ao vivo, RLS, customizações, cache Redis | [superset-e-seguranca.md](superset-e-seguranca.md) |
| Identidade | Descope (OAuth) | SSO; hoje só o Q Analytics usa | [superset-e-seguranca.md](superset-e-seguranca.md) |
| Apoio | GitHub | Jobs e pipelines gerenciados via Git e backup/restauração; procedimentos armazenados versionados | [pipeline-ingestao.md](pipeline-ingestao.md), [postgres-e-resumos.md](postgres-e-resumos.md) |

## Plataforma x produto (deck, slides 3 e 12)

- **Plataforma compartilhada ("one platform"):** conectividade, transformação, orquestração, warehouse, visualização, segurança e operações.
- **Cada produto ("many products")** fornece suas fontes de dados, regras, modelos e experiência de analytics.
- Lema do deck: construir a plataforma uma vez e depois integrar produtos, clientes e conjuntos de dados. Isso casa com o objetivo de levar todos os produtos ao Q Analytics ([produtos/marcas.md](../produtos/marcas.md)).

## Como integrar um novo produto (deck, slide 11)

Oito passos repetíveis, da descoberta à produção:

1. **Discover:** identificar sistemas de origem e resultados analíticos desejados.
2. **Connect:** estabelecer conectividade de banco ou API.
3. **Configure:** definir endpoints, mapeamentos, jobs e agendamentos.
4. **Model:** criar tabelas de warehouse, lógica e resumos.
5. **Visualize:** construir datasets, charts e dashboards no Superset.
6. **Secure:** aplicar papéis, contexto do cliente e escopo de dados.
7. **Validate:** reconciliar valores, desempenho e acesso.
8. **Release:** publicar, monitorar e operar.

## Onde roda

- **Autohospedado no data center de colocation da Quorum em Calgary** (Todd, reunião). Colocation: a Quorum tem seus próprios servidores em um espaço alugado de um data center. (Definição geral, ver glossário.)
- Custo de computação e armazenamento descrito como "perto de zero" em comparação com o ambiente anterior (Todd), o que bate com "agora só custo de servidores" (Rick). Detalhes em [migracao-powerbi-superset.md](migracao-powerbi-superset.md).
- Ambientes do **Superset**: **Dev** (construir e testar) e **Produção**; a promoção é feita por exportar e importar (ver [superset-e-seguranca.md](superset-e-seguranca.md)). Em 2026-10-28 o Eduardo é admin do Superset Dev, e o Todd já criou ali a conexão com o database `datamigration` do Postgres de teste.

## Ambientes da ferramenta de ingestão (Todd, 2026-10-27)

Existe ambiente de desenvolvimento da ferramenta de ingestão (não é tudo feito direto em produção), mas testar é mais trabalhoso do que num Superset Dev:

| Ambiente | O que é |
|---|---|
| Versões dev | O Todd mantém versões dev do app rodando em um container na rede interna, onde testa as mudanças. Ficam atrás do firewall. Segundo ele, é o melhor lugar para quem quer experimentar (mensagem no Teams). Em 2026-10-28 o Eduardo acessa a dev (com admin, segundo ele) e vê nela o cliente "DM Colo" conectado, o que indica que a dev e o ambiente de migração de dados são o mesmo ambiente `(inferido, a confirmar)` |
| Servidor de teste | Um único cliente rodando em um container no mesmo servidor (ligação). Provavelmente é a versão dev acima `(inferido)`. O Postgres dele está acessível ao Eduardo desde 2026-10-28 (ver [ambiente-de-teste-postgres.md](ambiente-de-teste-postgres.md)) |
| Ambiente de migração de dados (data migration environment) | Tem o cliente "DM Colo", ligado a um banco de teste MSSQL do DealerMine com dados reais exportados. É o sandbox indicado ao Eduardo. Os jobs existentes dele gravam em destinos de teste do Todd (SQL Server e `babelfish_db`); o destino da prática do Eduardo é o database `datamigration`, schema `test_edu` |
| Produção | Os clientes de produção conectam de volta a **um único servidor**; por isso, para alguns jobs, testar exige implantar clientes novos ou recriar o ambiente. Recursos de segurança impedem clientes de outros ambientes de se conectar |

Mais detalhes, e o que o Eduardo deve fazer neles, em [agente-de-ingestao.md](agente-de-ingestao.md) e [pratica-ingestao-dev.md](pratica-ingestao-dev.md).

## Escala (números citados)

- **256 bancos de concessionárias** gerenciados pela ferramenta de ingestão (Todd, referência técnica). O "cerca de 250 concessionárias" do Rick é aproximação do mesmo universo.
- **108 jobs** no total, entre incrementais e de produção (ligação de 2026-10-27, total confirmado pelo Eduardo).
- Tabelas brutas de até cerca de **425 GB** (Todd).

## Backup (o pouco que se sabe)

- Ligação de 2026-10-27: o **banco é copiado em backup**, mas jobs podem ser adicionados ou apagados; os pipelines incrementais e históricos já existentes **não têm backup**; mudanças em jobs e clientes são **gerenciadas via Git**; procedimentos armazenados do Postgres também ficam no Git (reunião de 2026-10-26).
- Não se sabe qual banco o "backup" cobre, nem a política de recuperação de desastre do Postgres e do Superset. Ver lacunas.

## Evolução em andamento (deck, slide 13)

A ingestão está **evoluindo para novos casos de uso, incluindo adicionar o Microsoft SQL Server como banco de destino** (hoje o destino principal é o Postgres). O deck não dá prazo nem motivo. Na ligação de 2026-10-27, o Todd disse que o **destino SQL Server já está disponível** e que fornece uma tabela se o Eduardo precisar. Na dev, os destination servers de SQL Server do DM Colo e o job de teste que grava nos dois destinos sugerem que o Todd está testando esse caso `(inferido)`.

## Transcrição do deck

O deck (14 slides, "QUORUM - CONFIDENTIAL", título "Analytics Platform", subtítulo "A reusable architecture for data ingestion, transformation, warehousing and visualization", outubro de 2026) não fica arquivado no projeto (ver guia); este é o conteúdo por slide, com onde cada um foi documentado:

| Slide | Título | Conteúdo | Documentado em |
|---|---|---|---|
| 1 | Analytics Platform | Capa: Connect, Transform, Warehouse, Visualize; "fundação repetível para implantar analytics em vários produtos" | este arquivo |
| 2 | Agenda overview | Visão da plataforma, conectividade, transformação, orquestração, data warehouse, visualização | este arquivo |
| 3 | A reusable analytics product capability | Separa serviços tecnológicos reutilizáveis do conteúdo específico de cada produto: Connect, Transform, Analyze, Visualize | este arquivo |
| 4 | End-to-end platform architecture | As 6 etapas e os serviços compartilhados (tabela acima) | este arquivo |
| 5 | Flexible data connectivity | Bancos atrás de firewall; múltiplos endpoints de API; endpoints configurados sob medida | [pipeline-ingestao.md](pipeline-ingestao.md) |
| 6 | Configuration-driven data transformation | Map, Standardize, Manipulate, Persist | [pipeline-ingestao.md](pipeline-ingestao.md) |
| 7 | Pipeline orchestration model | Client > Pipelines > Jobs; modos de execução | [pipeline-ingestao.md](pipeline-ingestao.md) |
| 8 | PostgreSQL analytics data warehouse | Detalhe e histórico, modelos analíticos, agregação de resumo agendada | [postgres-e-resumos.md](postgres-e-resumos.md) |
| 9 | Apache Superset analytics experience | Postgres > datasets > métricas > charts > dashboards | [superset-e-seguranca.md](superset-e-seguranca.md) |
| 10 | Security and customer data isolation | Identidade, autorização, contexto do cliente, escopo de dados, acesso a conteúdo | [superset-e-seguranca.md](superset-e-seguranca.md) |
| 11 | Onboarding a new product | Os 8 passos | este arquivo |
| 12 | Technology stack and product model | Superset, Postgres, motor de pipelines, conectores; "uma plataforma, muitos produtos" | este arquivo |
| 13 | Implementation customizations | Descope, controles de acesso, ingestão evoluindo, MCP/IA | [superset-e-seguranca.md](superset-e-seguranca.md), este arquivo |
| 14 | Questions? | Slide final; o texto "Quorum Presentation to ___ on ___" ficou em branco | n/a |

Observação: o deck é uma apresentação genérica e reutilizável, sem números de concessionárias, nomes de produtos da Quorum ou diagrama de infraestrutura (Calgary, réplica, Redis). Esses detalhes vêm da reunião.

## Lacunas e dúvidas

- Hardware, tamanho dos servidores, versões de Postgres e Superset, e como é feito o failover da réplica.
- Linguagem do ETL: o Rick disse Go; nem o Todd na reunião nem o deck citam.
- O que é "Q Cloud (público/privado)" e qual seu papel como fonte? (aparece na stack anterior)
- Backup e recuperação de desastre: qual banco tem backup, com que frequência, e qual o plano de recuperação do Postgres e do Superset (a ligação de 2026-10-27 só diz que "o banco é copiado em backup").
- Monitoramento e alertas: o deck lista "monitoring" como serviço compartilhado; como funciona na prática e como o time percebe que um job falhou?
- Microsoft SQL Server como destino: já está disponível, mas para qual caso de uso, para quais produtos, e quando entra em uso?
- Como levar uma mudança testada do ambiente dev da ingestão para produção? E como o Eduardo acessa a versão dev, que fica atrás do firewall (ele já acessa, o meio não foi registrado), e a dev é de fato o ambiente de migração de dados?
- Existe versão do deck com os dados específicos da Quorum (produtos, infraestrutura)? O deck de outubro de 2026 está datado de 13 e 14/10, antes da reunião.
