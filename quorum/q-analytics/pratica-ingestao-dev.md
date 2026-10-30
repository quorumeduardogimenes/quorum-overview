---
titulo: Prática do Eduardo na ingestão dev (diário e objetos criados)
resumo: Registro do roteiro prático na versão dev da ferramenta de ingestão e no Postgres de teste: acessos, o que foi explorado (ferramenta e banco de origem), o que o Eduardo criou (schema, destination server, pipeline, tabela, primeiro job e dashboard de teste), consultas e SQL usados, correção do job, lições, regras de segurança e próximos passos.
atualizado_em: 2026-10-29
fontes: [Eduardo, 2026-10-28 e 2026-10-29 (relatos do que viu, consultou e criou, resultados das consultas e do job, prints e README da ferramenta, em conversa com o assistente deste projeto), Todd, 2026-10-28 (mensagens repassadas pelo Eduardo), ligação com Todd, 2026-10-27 (../reunioes/2026-10-27-todd-ligacao-ingestao.md)]
status: rascunho
---

# Prática do Eduardo na ingestão dev (diário e objetos criados)

Este arquivo é o diário do roteiro prático combinado com o Todd (ver [agente-de-ingestao.md](agente-de-ingestao.md)). Os conceitos (destination servers, campos de um job, agendamento, snapshots, padrões dos jobs de produção) estão em [pipeline-ingestao.md](pipeline-ingestao.md), o Postgres de teste em [ambiente-de-teste-postgres.md](ambiente-de-teste-postgres.md) e o dashboard de teste no Superset em [superset-e-seguranca.md](superset-e-seguranca.md). Prints e telas não ficam arquivados: o que foi visto está descrito em texto. Nenhum endereço, credencial, usuário ou código de banco é registrado (guia, regra 9).

## Em palavras simples

O Eduardo percorreu o caminho dos dados em ambiente de teste: leu de um banco de origem (o SQL Server de teste do DealerMine), passou pela ferramenta de ingestão, gravou no Postgres de teste e montou um dashboard de teste no Superset Dev. Primeiro explorou sem alterar nada. Depois criou só objetos novos e pessoais. O primeiro job (contatos por dia e tipo) rodou sem erros, o upsert foi confirmado e o dashboard v1 está pronto. A próxima etapa é uma v2 com cartões de KPI e comparação com o mês anterior.

## Acessos (2026-10-28)

| Sistema | Situação | Fonte |
|---|---|---|
| Ferramenta de ingestão, produção | Acesso sem admin (o Eduardo olhou, só leitura, jobs agendados do DealerMine em produção) | Eduardo |
| Ferramenta de ingestão, dev | Acesso, com admin (segundo o Eduardo). O cliente "DM Colo" aparece e está conectado | Eduardo |
| pgAdmin 4 web e Postgres de teste | Funcionando; conexão cadastrada como `test db` | Eduardo; ver [ambiente-de-teste-postgres.md](ambiente-de-teste-postgres.md) |
| Superset Dev | Admin (configurado pelo Todd). O Todd já criou a conexão para o database `datamigration` | Todd, via Eduardo |

Como o Eduardo chega à dev, que fica atrás do firewall, não foi registrado. Não se contorna o firewall: só vale o acesso que o Todd liberou.

## Convenção de nomes e objetos criados pelo Eduardo

| Objeto | Onde | Nome | Situação |
|---|---|---|---|
| Schema | Database `datamigration` do Postgres de teste | `test_edu` | Criado em 2026-10-28 |
| Destination server | Ferramenta de ingestão dev | DMColoPGTestEdu (PostgreSQL, aponta para o `datamigration`) | Criado em 2026-10-28; a conexão funcionou. As credenciais foram passadas pelo Todd para isso, e eles alinharam por mensagem (Eduardo) |
| Pipeline | Ferramenta de ingestão dev, cliente DM Colo | DealerMine Test Edu | Criado em 2026-10-28 |
| Tabela de destino | `test_edu` | `dt_contact_daily` (chave primária composta de 5 colunas; 10 colunas de dados, incluindo `appt_date_count`) | Criada em 2026-10-29; coluna `appt_date_count` acrescentada no mesmo dia |
| Job | Pipeline DealerMine Test Edu | Contatos por dia, tipo e entrada/saída; grava em `test_edu.dt_contact_daily` | Rodou sem erros em 2026-10-29: 675 linhas e 12.344 contatos; upsert confirmado; editado depois para gravar `appt_date_count` |
| Dataset, 10 charts e dashboard v1 | Superset Dev | Prefixo `DM Colo - `, tag Test Edu, nomes em inglês | Criados em 2026-10-29; detalhes em [superset-e-seguranca.md](superset-e-seguranca.md) |
| View `v_contact_kpi_monthly`, chart Handlebars e dashboard v2 | `test_edu` e Superset Dev | A criar | Planejados para 2026-10-30 |

A convenção adotada é `test_edu` / "Test Edu" nos nomes do Postgres e da ingestão, e o prefixo `DM Colo - ` com a tag Test Edu no Superset. Sugestão `(inferido)`: manter essa convenção nos próximos objetos, para o Todd reconhecer o que é do Eduardo.

## Linha do tempo

**2026-10-28**

1. **pgAdmin:** o Todd mandou o endereço correto (o primeiro estava errado); o Eduardo criou a conexão `test db` e viu os três databases.
2. **Consultas só de leitura no `datamigration`:** o banco e o usuário estavam certos; os únicos schemas eram os quatro padrão do Postgres; o usuário tem permissão de criar schemas.
3. **Ferramenta de ingestão dev (só leitura):** o cliente DM Colo estava conectado; o Eduardo olhou os destination servers e os pipelines e jobs existentes, sem alterar nada. Resumo em [pipeline-ingestao.md](pipeline-ingestao.md).
4. **Criou** o schema `test_edu`, o destination server DMColoPGTestEdu e o pipeline "DealerMine Test Edu".
5. **Documentação da ferramenta:** o Eduardo colou o README e prints; o resumo está em [pipeline-ingestao.md](pipeline-ingestao.md).

**2026-10-28 e 2026-10-29**

6. **Exploração do banco de origem** pelo Query Executor, só leitura (ver seção abaixo).
7. **Jobs de produção:** o Eduardo colou o código de 5 jobs agendados do pipeline DealerMine de produção, para entender os padrões (resumo em [pipeline-ingestao.md](pipeline-ingestao.md)). O código não fica arquivado.
8. **Mensagem ao Todd** (Teams, inglês): pediu documentação das tabelas do DealerMine e contou que estava escolhendo tabelas para os primeiros jobs. Sem resposta até o momento (ver [../onboarding/perguntas-para-o-todd.md](../onboarding/perguntas-para-o-todd.md)).

**2026-10-29**

9. **Teste do SELECT** do primeiro job no Query Executor: 675 linhas resultantes, 12.344 contatos no total (igual à contagem da tabela) e média de dias até o agendamento entre 1 e 21.
10. **Tabela de destino** criada pelo Eduardo no `datamigration`, schema `test_edu`.
11. **Job configurado** no formulário (tela descrita em [pipeline-ingestao.md](pipeline-ingestao.md)) e **disparado manualmente** pelo botão Run (▶): rodou sem erros. A conferência no pgAdmin deu **675 linhas e 12.344 contatos**, igual ao teste na origem.
12. **Upsert confirmado:** um segundo disparo do mesmo job não duplicou as linhas.
13. **Superset:** dataset, charts e dashboard v1 (ver [superset-e-seguranca.md](superset-e-seguranca.md)). Na média de dias até o agendamento, os valores saíram abaixo de 1 dia, o que era impossível (o mínimo diário na origem era 1).
14. **Correção:** a causa era o peso usado na média ponderada (ver "Lições aprendidas"). O Eduardo acrescentou a coluna `appt_date_count` à tabela de destino, acrescentou a coluna ao `SELECT` do job (alteração no próprio job dele, sem mexer em outros), rodou o job de novo, sincronizou as colunas do dataset e recriou o chart. Os valores passaram a ficar entre 1 e 21 dias.
15. **Dashboard v1 fechado.** O Eduardo decidiu a v2 (cartões Handlebars com comparação com o mês anterior) e aceitou a comparação de mesmo período; o plano está em [superset-e-seguranca.md](superset-e-seguranca.md).

## Consultas usadas

Todas só leem, exceto as criações. No Query Tool do pgAdmin, conectado ao `datamigration`:

```sql
SELECT current_user, current_database();
SELECT schema_name FROM information_schema.schemata ORDER BY schema_name;
SELECT has_database_privilege(current_user, current_database(), 'CREATE') AS posso_criar_schema;
CREATE SCHEMA test_edu;  -- criação do schema
```

No Query Executor (SQL Server de origem), as consultas de exploração foram: lista de tabelas pelo `INFORMATION_SCHEMA.TABLES`; contagem de linhas pelo catálogo (`sys.tables` com `sys.partitions`, que não varre os dados); colunas pelo `INFORMATION_SCHEMA.COLUMNS`; chaves primárias pelo `INFORMATION_SCHEMA`; conferência de unicidade das chaves por `COUNT(DISTINCT ...)`; e contagem de `DTContactHistory` por tipo de contato e sentido, com primeira e última data.

## Exploração do banco de origem (DealerMine de teste)

O banco tem **1790 tabelas** (um prefixo `adp_import_` é muito frequente; o significado é desconhecido e foram descartadas). Não há documentação das tabelas; o Eduardo perguntou ao Todd. O que a contagem pelo catálogo mostrou (só números, sem dados):

| Tabela | Linhas | Observação |
|---|---|---|
| `DTContactHistory` | 12.344 | Histórico de contatos; 84 colunas; de 2025-09-22 a 2026-10-15. Usada no primeiro job |
| `ServiceHistMaster` | 41.973 | Histórico de serviço; candidata futura (receita e custo) |
| `Names1IdxMaster`, `SerindexMaster` | 9.854 e 9.853 | Cadastros de clientes e de veículos por cliente; evitar |
| `DMVehicleDetail` | 10.764 | Detalhe de veículos; a coluna `Key` é única (10.764 distintas, nenhuma nula) |
| `DTContactList` | 1.561 | Lista de contatos; evitar linhas, só contagens |
| `DTSalesProduct` | 46 | Produto por linha de venda; a coluna `Productkey` se repete (7 valores para 46 linhas) e cada produto tem uma descrição e várias linhas de venda; a chave natural é `productkey` + `selling_line` (inferido pelas contagens) |
| `DTStores`, `DTBranch` | 1 e 1 | O banco de teste tem uma loja e uma filial |
| `DTCalls`, `DTCallType`, `DTSalesTypes`, `DTSalesPrefixes`, `DTScheduledMaintenance`, `am_deals`, `am_Vehicles`, `DealerMineNetRepairs`, `DTContactListCounts` | 0 | Vazias no teste |

Não apareceram na contagem `ADPSalesTypes`, as tabelas `twilio_*` e a tabela `dbo.dbname`, que os jobs de produção usam; não existem neste banco `(inferido, pelo resultado da consulta)`. Nem `DMVehicleDetail` nem `DTSalesProduct` têm PRIMARY KEY na origem.

**`DTContactHistory`:** os tipos de contato presentes são `Turned Off` (9.302, cerca de 75%), `BookAppt` (cerca de 3.033) e quatro tipos com menos de 10 linhas cada (`BookingEService`, `DeleteAppt`, `VehicleAdd`, `ApptBooked`). O sentido (`orgOutIn`) vem quase sempre vazio; quando preenchido, aparece com maiúsculas diferentes. Os tipos usados nos jobs de produção (`ConfirmAppt`, `Recall`, `CSI` etc.) não aparecem. Colunas de texto livre e de nomes de pessoas ficaram fora do job. O significado de `Turned Off` é dúvida.

## O primeiro job: contatos por dia, tipo e sentido

**Ideia (padrão dos jobs de produção):** agregar na origem e carregar só o resultado, sem linhas de dados pessoais.

**Chave do upsert** (as 5 colunas de agrupamento): `contact_day`, `contact_type`, `org_in_out`, `storeno`, `branchno`. Nulos e vazios são trocados por `(blank)`, `UNKNOWN` e `-1`, porque colunas de chave não aceitam nulo.

**Colunas calculadas:** `contact_count` (contatos), `distinct_customers` (clientes distintos), `with_appointment` (contatos com `ApptNo`), `avg_days_to_appt` (média de dias entre o contato e o agendamento, ignorando datas anteriores a 2000), `appt_date_count` (quantidade de linhas com data de agendamento válida, o peso correto da média) e `loaded_at` (data e hora da carga).

```sql
SELECT
    CAST(ContactDate AS DATE)                                        AS contact_day,
    COALESCE(NULLIF(LTRIM(RTRIM(ContactType)), ''), '(blank)')       AS contact_type,
    COALESCE(NULLIF(UPPER(LTRIM(RTRIM(orgOutIn))), ''), 'UNKNOWN')   AS org_in_out,
    COALESCE(StoreNo, -1)                                            AS storeno,
    COALESCE(Branch, -1)                                             AS branchno,
    COUNT(*)                                                         AS contact_count,
    COUNT(DISTINCT CustNo)                                           AS distinct_customers,
    SUM(CASE WHEN ApptNo IS NOT NULL THEN 1 ELSE 0 END)              AS with_appointment,
    CAST(AVG(CASE WHEN ApptDate >= '2000-01-01'
                  THEN CAST(DATEDIFF(DAY, ContactDate, ApptDate) AS DECIMAL(10,2))
             END) AS DECIMAL(10,2))                                  AS avg_days_to_appt,
    SUM(CASE WHEN ApptDate >= '2000-01-01' THEN 1 ELSE 0 END)        AS appt_date_count,
    GETDATE()                                                        AS loaded_at
FROM DTContactHistory
WHERE ContactDate IS NOT NULL
GROUP BY CAST(ContactDate AS DATE),
         COALESCE(NULLIF(LTRIM(RTRIM(ContactType)), ''), '(blank)'),
         COALESCE(NULLIF(UPPER(LTRIM(RTRIM(orgOutIn))), ''), 'UNKNOWN'),
         COALESCE(StoreNo, -1),
         COALESCE(Branch, -1)
```

```sql
CREATE TABLE test_edu.dt_contact_daily (
    contact_day date NOT NULL, contact_type varchar(25) NOT NULL,
    org_in_out varchar(25) NOT NULL, storeno integer NOT NULL, branchno integer NOT NULL,
    contact_count integer, distinct_customers integer, with_appointment integer,
    avg_days_to_appt numeric(10,2), loaded_at timestamp,
    PRIMARY KEY (contact_day, contact_type, org_in_out, storeno, branchno)
);
ALTER TABLE test_edu.dt_contact_daily ADD COLUMN appt_date_count integer;  -- correção da média
```

**Configuração do job:** tipo Database (SQL); Client ID do DM Colo; Target Table e override de destino `test_edu.dt_contact_daily` (com schema); só o destination server DMColoPGTestEdu; conflito nas 5 colunas da chave com ação `update`; agendamento Manual only. **Resultado:** 675 linhas e 12.344 contatos; o segundo disparo manteve 675 (upsert confirmado).

## Lições aprendidas

1. **Média ponderada:** o peso precisa ter o mesmo universo do valor. A primeira versão do chart ponderava `avg_days_to_appt` por `with_appointment`, mas a média só considera linhas com data de agendamento válida; o denominador ficava inflado e o resultado dava abaixo de 1 dia, impossível. Corrigido com `appt_date_count`.
2. **Agregue na origem e guarde numerador e denominador:** colunas como `appt_date_count` permitem refazer médias e razões em qualquer período, no Superset ou numa view.
3. **Contagem distinta não é somável entre dias:** `distinct_customers` não serve para somar por mês.
4. **Confira contra a origem:** a contagem de 12.344 contatos na origem e no destino foi a prova de que nada se perdeu.
5. **Rode duas vezes:** o segundo disparo prova que o upsert não duplica. Acrescentar uma coluna ao `SELECT` e rodar de novo a preencheu (ação `update`), sem recriar a tabela.
6. **Chave sem nulos:** troque nulos e vazios por valores fixos antes de gravar, como fazem os jobs de produção.
7. **Alterar só o que é seu:** a correção foi feita no job criado pelo próprio Eduardo, abrindo-o pelo lápis do job dele.

## Mapa da ferramenta para um job

| Preciso de... | Onde | Cuidado |
|---|---|---|
| Ver tabelas e colunas da origem | Query Executor (cliente DM Colo) | Só `SELECT`; não copiar linhas de dados |
| Criar a tabela de destino | pgAdmin, schema `test_edu` | Precisa existir antes do job; com PRIMARY KEY que coincida com a chave do upsert |
| Criar o job | Pipeline DealerMine Test Edu, Add Job | Client ID preenchido (em branco roda em todos os clientes do pipeline); só o destination server do Eduardo; abrir pelo Add Job, nunca pelo lápis de jobs existentes de outras pessoas |
| Rodar | Botão Run (▶) do job | Nunca Run All |
| Conferir | pgAdmin; Runtime Logs e Data Transfer History na ferramenta | |

## Regras de segurança aplicadas

- Criar só objetos novos e pessoais. Nunca alterar nem apagar o que já existe, nem em dev, mesmo sendo admin.
- Clicar no X da ferramenta pode apagar jobs: não clicar.
- Não usar Run All, Apply de snapshot, Runtime Capacity, Delete em destination servers nem a área de contas.
- Os databases `babelfish_db` e `postgres`, os pipelines DealerMine Historical e DealerMine Incremental e os jobs existentes (inclusive os de produção, só vistos) são do Todd: só olhar.
- Novos jobs `(inferido, combinado em conversa, não pelo Todd)`: agendamento manual, Client ID preenchido com o DM Colo e gravando só no destination server do Eduardo.
- O banco de teste tem dados reais exportados: não copiar linhas de dados para conversas nem documentos, evitar tabelas com dados pessoais ou senhas e preferir resultados agregados.
- Sem credenciais, IPs, endereços, nomes de usuário ou códigos de banco na documentação.

## Próximos passos

1. **Dashboard v2 (2026-10-30):** view `v_contact_kpi_monthly` em `test_edu`, dataset, chart Handlebars com os 6 cartões e comparação de mesmo período, dashboard v2 com filtro padrão no mês atual (plano completo em [superset-e-seguranca.md](superset-e-seguranca.md)).
2. Registrar os campos do formulário do job que ainda não foram vistos (cliente, pipeline, conflito, agendamento, variáveis, last run).
3. Opcional: jobs sobre `DTSalesProduct` (chave `productkey` + `selling_line`), `DMVehicleDetail` (chave `Key`) e `ServiceHistMaster`; e um job incremental com a variável de última execução, como o job 5 de produção.
4. Reunir as dúvidas que sobrarem e levá-las ao Todd, mostrando o que já foi testado.

## Lacunas e dúvidas

- O pipeline "DealerMine Test Edu" precisou ser atribuído ao cliente DM Colo no painel do cliente? O job rodou sem erros; o passo não foi registrado.
- O que significa `Turned Off` ser cerca de 75% dos contatos de `DTContactHistory`?
- Quais tabelas do DealerMine são transacionais, de apoio ou legadas? Sem documentação (pedido enviado ao Todd, sem resposta).
- Como o Eduardo acessa a dev e se ela é o mesmo que o ambiente de migração de dados (Rodada 3, 2; o DM Colo aparecer na dev é um indício, não confirmação).
- Como promover o que foi testado para produção (Rodada 3, 1; os snapshots com GitHub parecem ser o caminho, a confirmar).
