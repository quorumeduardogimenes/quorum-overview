---
titulo: Superset, segurança (RLS e Descope), customizações e dashboard de teste do Eduardo
resumo: Como o Apache Superset serve os dashboards do Q Analytics: consultas ao vivo, SSO via Descope, security manager customizado, RLS, ambientes Dev/Prod, customizações e uso do Claude via MCP; mais o que o Eduardo construiu no Superset Dev (dataset, 10 charts e dashboard v1 sobre o primeiro job de ingestão), lições e o plano da v2 com cartões Handlebars.
atualizado_em: 2026-10-29
fontes: [reunião com Todd, 2026-10-26 (../reunioes/2026-10-26-todd-tour-tecnico.md), deck "Analytics Platform" do Todd (slides 9, 10 e 13, enviado em 2026-10-27), reunião com Rick, 2026-10-26 (../reunioes/2026-10-26-rick-kickoff.md), conversa com Sam, 2026-10-29 (../reunioes/2026-10-29-sam-regras-de-negocio.md), Eduardo (criação do dataset, dos charts, dos filtros e do dashboard de teste no Superset Dev, lista de charts em print, decisões da v2, 2026-10-29), conhecimento geral do Superset de quem documentou (marcado como inferido)]
status: rascunho
---

# Superset, segurança (RLS e Descope), customizações e dashboard de teste do Eduardo

## Em palavras simples

O Superset é a "vitrine": ele não guarda os dados, só pergunta ao Postgres na hora em que alguém abre um dashboard. Para que cada concessionária veja apenas o que é dela, o login (feito pelo Descope) identifica quem é o usuário, e uma peça customizada dentro do Superset usa esses dados de login para decidir quais dashboards a pessoa pode abrir e quais linhas pode ver. A Quorum também acrescentou peças próprias ao Superset (rolagem, medidores) e usa o Claude, ligado ao Superset por MCP, para ajudar a criar gráficos e conferir os números.

## Como o Superset consulta

- **Consultas ao vivo contra o Postgres**, sem pré-sincronização como no Power BI (Todd). A performance vem das tabelas de resumo (ver [postgres-e-resumos.md](postgres-e-resumos.md)).
- Os relatórios usam a **réplica de leitura**.
- Cadeia de objetos do Superset (deck, slide 9): tabelas do Postgres (warehouse e resumos) > **datasets** (acesso semântico às estruturas analíticas) > **métricas** (medidas e cálculos reutilizáveis) > **charts** (KPIs, tendências, comparações, detalhes) > **dashboards** (experiência interativa por produto). O deck diz que o Superset é a camada de entrega; o preparo de dados "pronto para o negócio" fica no warehouse e nos pipelines.

## Segurança

Modelo em 5 camadas (deck, slide 10):

| Camada | Descrição no deck |
|---|---|
| 1. Identidade | Autenticar usuários por um provedor de identidade centralizado |
| 2. Autorização | Aplicar papéis e permissões a funcionalidades e conteúdo |
| 3. Contexto do cliente | Resolver o escopo de cliente/tenant associado ao usuário |
| 4. Escopo dos dados | Restringir consultas, datasets e linhas aos dados do cliente autorizado |
| 5. Acesso ao conteúdo | Expor só dashboards, datasets e relatórios agendados aprovados |

Como isso está implementado na Quorum:

| Tema | O que sabemos | Fonte |
|---|---|---|
| Autenticação (SSO) | **Descope**, via OAuth. Na reunião, o Granola escreveu "dScope"; o deck confirma **Descope** | Deck, slide 13; Todd |
| Controles de acesso | **Metadados do login** e um **security manager customizado do Superset** controlam o acesso a dashboards e a RLS | Deck, slide 13 |
| RLS (row-level security) | Limita cada cliente aos próprios dados; o filtro é **passado como metadado da conta do usuário** | Todd |
| Autorização | Dirigida por uma **tabela de segurança das concessionárias**, sincronizada para o warehouse | Todd |
| Alcance do Descope | **Só o Q Analytics usa hoje**; a intenção é estender a todos os produtos da Quorum | Todd |
| Acesso aos dashboards | Direto pelo Superset; alguns podem ser embutidos no DMS (opcional) | Rick |

## Quem vê o quê (visão de negócio do Sam)

| Quem | O que vê |
|---|---|
| Uma concessionária | Só os próprios dados; **nunca** os de outra |
| Pessoas dentro da concessionária | Acesso por **papel**: alguns veem tudo da loja, outros só um subconjunto |
| Diretoria da Quorum (por exemplo, o "Terry") | **Tudo**, de todos os clientes |

Isso ainda permite a comparação entre clientes no lado interno (ver [visao-geral.md](visao-geral.md)). É a descrição funcional do Sam; não diz como o Superset a implementa.

## Ambientes e promoção

- Existe um ambiente **Dev** para construir e testar.
- Para promover para produção usa-se **exportar e depois importar** (Todd).

## Customizações sobre o Superset

| Customização | Motivo / como | Fonte |
|---|---|---|
| Autenticação via Descope (OAuth) | SSO | Deck, slide 13 |
| Security manager customizado | Acesso a dashboards e RLS a partir dos metadados do login | Deck, slide 13 |
| Barra de rolagem e zoom | Feitos com JavaScript; o Superset não tinha rolagem no estilo do Power BI | Todd |
| Gráficos de medidor (gauge) | Construídos em código **Handlebars** | Todd |
| Carregamento preguiçoso (lazy loading) por página de dashboard | Com **cache em Redis** | Todd |

## Claude via MCP

- Há **suporte a MCP no Apache Superset**, que permite integração com IA, com **autenticação pelo Descope** (deck, slide 13).
- Na reunião, o Todd mostrou a **integração MCP com o Claude Desktop**, que pode **gerar gráficos e consultar o Postgres diretamente**.
- Usos citados: criação de gráficos, **auditoria de SQL** e **validação de dados**.
- Criar dashboards exige **refinar a consulta SQL** para garantir que os números estejam corretos.
- Fluxo recomendado: **construir em Dev com o Claude, conferir o resultado e exportar para produção**.

(O acesso do Eduardo a essa integração ainda não foi confirmado; ver lacunas.)

## Dashboard de teste do Eduardo no Superset Dev (2026-10-29)

O Eduardo é admin do Superset Dev (configurado pelo Todd, que também criou a conexão com o database `datamigration`). A partir da tabela carregada pelo primeiro job de ingestão (`test_edu.dt_contact_daily`, ver [pratica-ingestao-dev.md](pratica-ingestao-dev.md)), ele criou só objetos novos e pessoais.

**Convenções adotadas:** nomes **em inglês**; prefixo `DM Colo - ` nos charts e no dashboard; tag **Test Edu** em tudo que é dele (a tag identifica o que é seu, mesmo com o prefixo). Nenhum objeto existente foi alterado.

**Dataset:** `dt_contact_daily` (schema `test_edu`), sobre a conexão do `datamigration`. Quando uma coluna nova entrou na tabela (`appt_date_count`), foi preciso sincronizar as colunas do dataset a partir da origem para ela aparecer nos charts.

**Charts (10):**

| Chart | Tipo | Configuração (relato do Eduardo, seguindo o passo a passo) |
|---|---|---|
| Contacts by Month and Type | Line | Eixo X `contact_day` por mês; métrica `SUM(contact_count)`; dimensão `contact_type` |
| Contacts Shared by Type | Pie (rosca) | `SUM(contact_count)` por `contact_type`; rótulo com categoria e percentual |
| Appointments by Month | Bar | `SUM(with_appointment)` por mês; filtro `contact_type = BookAppt` |
| Days to Appointment | Bar | Média de dias até o agendamento por mês, ponderada por `appt_date_count`; filtro `BookAppt`; valores entre 1 e 21 dias |
| Total Contacts | Big Number | `SUM(contact_count)`; subheader "Total contacts"; formato `,d` |
| Booked Appointments | Big Number | `SUM(CASE WHEN contact_type = 'BookAppt' THEN contact_count ELSE 0 END)` |
| Contacts with Appointment | Big Number | `SUM(with_appointment)` |
| Turned Off Share | Big Number | Razão entre os contatos `Turned Off` e o total; formato percentual |
| Avg Days to Appointment | Big Number | Mesma média ponderada do chart de dias até o agendamento |
| Days with Contacts | Big Number | `COUNT(DISTINCT contact_day)` |

**Dashboard v1 (fechado em 2026-10-29):** nome na convenção `DM Colo - ...` (provavelmente `DM Colo - Contacts`; a confirmar). Layout: linha de 6 big numbers no topo, depois a linha e a rosca, depois as barras. Dois filtros nativos: **Contact Type** (valor, sobre `contact_type`, aplicado só aos charts em que faz sentido) e **Period** (intervalo de tempo sobre `contact_day`). O Superset avisou que filtros de período valem para colunas temporais definidas na seção de filtros de cada chart; segundo o Eduardo, o filtro funcionou mesmo assim, sem alterar os charts.

**Lições (valem para o Q Analytics):**

1. **Média ponderada:** o peso precisa ter o mesmo universo do valor. A primeira versão ponderava `avg_days_to_appt` (calculada só nas linhas com data de agendamento válida) por `with_appointment` (linhas com número de agendamento), o que diluía a média e dava valores abaixo de 1 dia, impossíveis. A correção foi gravar `appt_date_count` na tabela (job e tabela alterados) e usar `SUM(avg * appt_date_count) / SUM(appt_date_count)`.
2. **Contagem distinta não é somável:** `SUM(distinct_customers)` repetiria clientes contatados em mais de um dia; por isso a coluna não virou big number.
3. **Conferir o número contra a origem** (aqui, 12.344 contatos na origem e na tabela) antes de montar gráficos.
4. **Tipo dominante:** `Turned Off` é cerca de 75% dos contatos no banco de teste, o que achata os outros tipos na escala; separar por filtro (BookAppt) ajuda.

**Avaliação do visual e caminhos de melhoria:** o Eduardo achou o visual da v1 fraco (fontes, cores e tamanhos dos big numbers diferentes). Caminhos, do mais simples ao mais flexível `(inferido, conhecimento geral do Superset; não testado na versão da Quorum)`: (1) padronizar tamanho de fonte e esquema de cores em todos os charts e a altura no dashboard; (2) CSS do dashboard, campo "CSS" em Edit properties, que vale só para aquele dashboard (os nomes das classes variam entre versões); (3) chart **Handlebars**, que dá controle total de HTML e CSS (é o que o Todd usa nos medidores).

## Próxima etapa: dashboard v2 com cartões Handlebars (planejada para 2026-10-30)

Nada disto foi criado ainda. Decisões do Eduardo: substituir os big numbers por Handlebars, com a variação em relação ao mês anterior em cada KPI, e o filtro de período padrão no mês atual. **Aceita a comparação de mesmo período** (mês até o último dia com dado contra os mesmos dias do mês anterior).

**Modelagem:** o filtro de período corta o mês anterior fora do resultado do chart, e o Handlebars não faz conta, então o mês anterior precisa vir na mesma linha. Plano: uma **view em `test_edu`** (`v_contact_kpi_monthly`) com uma linha por mês, os 6 KPIs do mês e do mês anterior (`LAG`) e, para cada KPI, a variação, a direção (`up`, `down`, `flat`) e o texto pronto (como `+12.3%` ou `n/a`). Tudo sai de `dt_contact_daily`; o job de ingestão não muda.

**Regra do mesmo período:** o corte é o último dia com dado da tabela (hoje, 15/10/2026, então outubro conta os dias 1 a 15 e setembro também). Meses antigos, completos, comparam o mês inteiro com o mês anterior inteiro.

**Variação:** percentual para contagens e para a média de dias; **pontos percentuais** para a Turned Off Share.

**Superset:** dataset novo sobre a view (`month_start` marcado como temporal); um chart Handlebars "KPI Cards" (recomendado: um chart único com os 6 cartões, para ter mesmo tamanho, fonte e cor), modo bruto, 1 linha, ordenado por mês decrescente, com `month_start` como filtro temporal "sem filtro" para o filtro de período do dashboard agir; dashboard v2 criado com "Save as" a partir da v1, sem alterar a v1; filtro de período padrão "Current calendar month".

**Riscos:** o Superset pode sanitizar o HTML do Handlebars (o Todd usa Handlebars nos medidores, então o recurso existe); a fonte de teste está parada em 15/10, então em novembro "Current calendar month" ficará vazio (trocar o padrão para o mês anterior); a média de dias é aproximada (a coluna está arredondada em 2 casas).

## Lacunas e dúvidas

- Como exatamente o RLS é aplicado: o security manager customizado injeta filtros (por exemplo, RLS filters do Superset) a partir dos metadados do login, ou há também política no Postgres? Que campo identifica a concessionária? E nos grupos de concessionárias, que veem várias lojas?
- Como funciona o embed no DMS e a ligação com o SSO do Descope?
- Os papéis (roles) e permissões: o Sam diz que há acesso por papel dentro da concessionária; quais papéis existem e onde são definidos (Descope, Superset ou a tabela de segurança)?
- A tabela de segurança das concessionárias: de onde vem e com que frequência é sincronizada?
- Versão do Superset, e se as customizações são um fork ou extensões (onde está o código do security manager)?
- Que ferramentas o MCP do Superset expõe e com quais credenciais (somente leitura na réplica?). O Eduardo terá acesso?
- Há um README do Superset? Todd disse que vai gerar um se a documentação for escassa.
- Onde ficam as definições de dashboards exportados (repositório)?
- Existe um padrão visual dos dashboards da Quorum (fontes, cores, cartões de KPI) que o dashboard de teste deveria seguir? Conferir com o Sam ou o Todd.
- O Superset do Dev permite HTML e CSS livres no Handlebars e no CSS do dashboard, ou há sanitização que corta estilos? (A testar na v2.)
