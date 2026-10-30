---
titulo: Plano das primeiras semanas do Eduardo
resumo: Cronograma, checklist de ações e situação das perguntas de follow-up de onboarding, combinados com o Rick, o Todd e o Sam.
atualizado_em: 2026-10-29
fontes: [reunião com Rick, 2026-10-26 (../reunioes/2026-10-26-rick-kickoff.md), reunião com Todd, 2026-10-26 (../reunioes/2026-10-26-todd-tour-tecnico.md), ligação com Todd, 2026-10-27, e mensagem dele no Teams do mesmo dia (../reunioes/2026-10-27-todd-ligacao-ingestao.md), deck do Todd (enviado em 2026-10-27), Word do Todd sobre o agente (2026-10-27), conversa com Sam, 2026-10-29 (../reunioes/2026-10-29-sam-regras-de-negocio.md), Eduardo (diagrama recebido, deck e Word do Todd compartilhados, acessos recebidos, esclarecimentos, 2026-10-27; conexão ao Postgres de teste, orientação do Todd, exploração e criação de objetos na dev, README e prints da ferramenta de ingestão, 2026-10-28; exploração do banco de origem, jobs de produção, mensagem ao Todd, primeiro job concluído e dashboard de teste no Superset Dev, 2026-10-29)]
status: rascunho
---

# Plano das primeiras semanas do Eduardo

## Cronograma

| Quando | O quê | Com quem | Duração | Status |
|---|---|---|---|---|
| 2026-10-26 | 1-on-1 para passar a arquitetura | Todd | n/i | feito (ver [reunioes/2026-10-26-todd-tour-tecnico.md](../reunioes/2026-10-26-todd-tour-tecnico.md)) |
| 2026-10-27 | Mensagem e ligação sobre ambiente de teste e criação de fluxos de ingestão | Todd | n/i | feito (ver [reunioes/2026-10-27-todd-ligacao-ingestao.md](../reunioes/2026-10-27-todd-ligacao-ingestao.md)) |
| 2026-10-29 | Conversa sobre negócio das concessionárias, DMS, história dos relatórios e cultura; demonstração do DMS | Sam | cerca de 2 h | feito (ver [reunioes/2026-10-29-sam-regras-de-negocio.md](../reunioes/2026-10-29-sam-regras-de-negocio.md)); telas e acesso serão documentados com prints |
| Semana de 2026-10-26 | Shadowing nos relatórios existentes | Sam | 3 a 4 horas | em andamento: a conversa de 2026-10-29 pode ter feito parte; o Sam disse que o Eduardo pode acompanhá-lo enquanto ele trabalha (a combinar mais tarde no mesmo dia) |
| 2026-10-28 (quarta, a confirmar) | Check-in | Rick | 30 min | Rick deve agendar |
| Semana de 2026-10-26 | Receber diagrama e notas da reunião | Rick | n/a | diagrama recebido em 2026-10-27 (ver [produtos/marcas.md](../produtos/marcas.md)); notas escritas: não confirmado |
| Semana de 2026-11-02 | Começar o epic de novas stories de relatórios | Michael (dono do epic) | n/a | pendente |

Contexto: Sam passa para o time de suporte na semana de 2026-11-02 (Rick), por isso o shadowing precisa acontecer nesta semana.

## Ações da reunião com o Todd (2026-10-26)

| Ação | Quem | Status |
|---|---|---|
| Criar uma conta nova no GitHub com o e-mail quoruminfotech.com e enviar o ID ao Todd pelo Teams | Eduardo | pendente (a confirmar) |
| Adicionar o Eduardo à organização GitHub da Quorum | Todd | depende da ação acima |
| Convite do Granola e transcrição da reunião | Todd | já compartilhado, segundo o Eduardo |
| PowerPoint de arquitetura de alto nível | Todd | recebido e transcrito em [q-analytics/arquitetura.md](../q-analytics/arquitetura.md) (2026-10-27) |
| Apontar os repositórios e READMEs do QAnalytics da ferramenta de ingestão | Todd | o Eduardo já viu o README da ferramenta (2026-10-28); resumo em [q-analytics/pipeline-ingestao.md](../q-analytics/pipeline-ingestao.md) |
| Gerar um README do Superset se a documentação for escassa | Todd | a confirmar |

## Ações da ligação com o Todd (2026-10-27)

| Ação | Quem | Status |
|---|---|---|
| Enviar os acessos pedidos | Todd | enviados, segundo o Eduardo |
| Enviar o documento Word sobre o processo do agente de ingestão | Todd | recebido; resumido em [q-analytics/agente-de-ingestao.md](../q-analytics/agente-de-ingestao.md) |
| Enviar credenciais do Postgres e acesso ao servidor de teste (para conectar pelo pgAdmin 4) | Todd | **feito em 2026-10-28:** o Todd enviou a URL correta do pgAdmin web e o Eduardo criou a conexão `test db` (ver [q-analytics/ambiente-de-teste-postgres.md](../q-analytics/ambiente-de-teste-postgres.md)) |
| Explorar a ferramenta e o repositório primeiro, depois compilar as perguntas restantes e enviá-las pelo Microsoft Teams | Eduardo | em andamento: a ferramenta dev foi explorada e o README lido em 2026-10-28; a lista de perguntas foi podada; em 2026-10-29 o Eduardo mandou ao Todd a primeira mensagem (documentação das tabelas do DealerMine), ainda sem resposta |
| Criar um pipeline pessoal e jobs no ambiente de migração de dados, usando o banco de teste MSSQL do DealerMine para praticar SQL e conhecer a ferramenta | Eduardo | **primeiro job concluído (2026-10-29):** pipeline "DealerMine Test Edu" no cliente DM Colo da dev; banco de origem explorado só em leitura; tabela `test_edu.dt_contact_daily` criada; job de contatos por dia, tipo e sentido rodou sem erros (675 linhas, 12.344 contatos, conferidos com a origem) e o upsert foi confirmado. Próximos jobs: a definir (ver [q-analytics/pratica-ingestao-dev.md](../q-analytics/pratica-ingestao-dev.md)) |
| Criar o próprio schema no database `datamigration` e o próprio pipeline de jobs, para separar o trabalho do Eduardo do do Todd (sugestão do Todd, 2026-10-28) | Eduardo | **feito em 2026-10-28:** schema `test_edu`, destination server DMColoPGTestEdu e pipeline criados |
| Criar dataset, chart e dashboard de teste no Superset Dev, a partir da tabela carregada | Eduardo | **versão 1 feita em 2026-10-29:** dataset sobre `test_edu.dt_contact_daily`, 10 charts e um dashboard com filtros de tipo de contato e período, nomes em inglês (ver [q-analytics/superset-e-seguranca.md](../q-analytics/superset-e-seguranca.md)) |
| Dashboard v2 de teste: trocar os Big Numbers por cartões Handlebars com comparação com o mês anterior (percentual ou pontos percentuais), período padrão = mês atual, com view nova de modelagem em `test_edu` | Eduardo | **planejado para 2026-10-30** (plano em [q-analytics/superset-e-seguranca.md](../q-analytics/superset-e-seguranca.md)); só objetos novos e pessoais, nunca alterar os existentes |
| Fornecer uma tabela de destino no SQL Server, se necessário | Todd | só se o Eduardo pedir |

## Ações da conversa com o Sam (2026-10-29)

| Ação | Quem | Status |
|---|---|---|
| Ajudar o Eduardo a obter acesso ao servidor de teste do DMS (identificado como ZZZ509, via "QIS Connector" / colocation) | Sam | pendente |
| Pedir formalmente o acesso ao ambiente de teste do DMS ao Todd ou ao help desk | Eduardo | pendente |
| Voltar a conversar com o Sam mais tarde no mesmo dia; possível shadowing enquanto ele trabalha | Eduardo e Sam | a combinar |
| Enviar os prints do DMS (telas e como o acesso é feito) para documentar [produtos/quorum-dms.md](../produtos/quorum-dms.md) | Eduardo | pendente (segundo momento) |

**Cuidado combinado com o Sam:** o servidor de teste do DMS é seguro para explorar; **não se conectar a ambientes de clientes com nome**.

**Cuidados combinados com o Todd:** clicar no X da ferramenta de ingestão pode deletar jobs; não modificar pipelines e jobs existentes, nem na dev e mesmo sendo admin (os incrementais e históricos não têm backup); praticar só em ambiente de teste (a versão dev que o Todd recomenda, que fica atrás do firewall, ou o ambiente de migração de dados); no Postgres de teste, trabalhar só no `datamigration`, em schema próprio. Credenciais, IPs, endereços, usuários e códigos de banco nunca entram nesta documentação. O banco de teste do DealerMine tem dados reais de clientes: não se registram linhas de dados.

## Perguntas de follow-up para o Todd

A lista de perguntas **ainda em aberto**, em inglês e em três rodadas, está em [perguntas-para-o-todd.md](perguntas-para-o-todd.md), que é a fonte atual. Perguntas respondidas saem de lá; a numeração original é mantida. Esta tabela guarda o histórico (situação em 2026-10-29; os números são os de cada rodada):

| Pergunta (resumo) | Situação |
|---|---|
| Rodada 1, 1: existe Dev da ferramenta de ingestão e como é o processo prático de registrar um novo fluxo | **Respondida e removida da lista (2026-10-28).** Existem versões dev em container na rede interna, atrás do firewall (Todd). O Eduardo explorou a dev, leu o README e criou schema, destination server e pipeline; em 2026-10-29 criou e rodou o primeiro job com sucesso. O passo a passo (campos, formulário, upsert, agendamento, execução manual) está em [q-analytics/pipeline-ingestao.md](../q-analytics/pipeline-ingestao.md) e [q-analytics/pratica-ingestao-dev.md](../q-analytics/pratica-ingestao-dev.md). O que sobrar da prática vira dúvida nova |
| Rodada 1, 2 a 6 (organização do Postgres, dashboards x resumos, Git do Superset, RLS na prática, dashboards por produto) | Em aberto, sem resposta nas fontes novas |
| Rodada 2, 1: SQL Server como destino | **Parcial, mantida:** está disponível; na dev existem destination servers SQL Server e um job que grava nos dois destinos (provável teste do Todd, inferido); falta caso de uso, produtos e prazo |
| Rodada 2, 2: backup e recuperação de desastre | **Parcial, mantida e estreitada:** a configuração de jobs e pipelines tem snapshots e GitHub (README); falta o backup dos dados do Postgres, o do Superset e o plano de recuperação |
| Rodada 2, 3: change tracking e outras fontes | **Parcial, mantida e estreitada:** SQL Server usa change tracking (permissão `VIEW CHANGE TRACKING`); o README só mostra a variável de última execução (`last_run_var`) para o resto; falta confirmar se é só isso |
| Rodada 2, 4: falha de job e reprocessamento | **Parcial, mantida e estreitada:** a ferramenta tem Runtime Logs, Data Transfer History e o botão Run para rodar de novo (README); falta saber de alertas e de reprocessar uma janela de tempo |
| Rodada 2, 5: falha de pg_cron | Em aberto |
| Rodada 2, 6: papéis e permissões | Em aberto |
| Rodada 3, 1: promoção do teste ao produção | **Parcial, mantida e estreitada:** o README descreve snapshots de configuração com exportar, aplicar e restaurar via GitHub, provável caminho `(inferido)`; falta confirmar que é o processo oficial e quem revisa. O Eduardo não vai testar a promoção |
| Rodada 3, 2: acesso à dev atrás do firewall e se é o mesmo que o ambiente de migração | **Parcial, mantida:** o Eduardo já acessa a dev (o meio não foi registrado) e o "DM Colo" aparece nela, indício de que são o mesmo ambiente (inferido). Falta o Todd confirmar |
| Rodada 3, 3: passo 1 da implantação (usuário e senha do cliente) | Em aberto |
| Pergunta do Eduardo ao Todd sobre "test db" e "data migration db" serem a mesma coisa e o DM Colo gravar nele (fora das rodadas) | **Resposta parcial do Todd:** o `datamigration` está no ambiente de teste e os outros databases são testes dele. Pela dev, o DM Colo hoje não grava no `datamigration` (a não ser pelo job do Eduardo, que agora grava lá) |
| Mensagem do Eduardo ao Todd em 2026-10-29 (fora das rodadas): existe documentação das tabelas do DealerMine? | **Enviada, sem resposta.** Ver [perguntas-para-o-todd.md](perguntas-para-o-todd.md) |

**Dúvidas novas, sem número de rodada** (surgidas em 2026-10-28 e 2026-10-29; o Eduardo vai testá-las na prática antes de levá-las ao Todd; só as que sobrarem viram uma Rodada 4, se ele quiser):

- O pipeline "DealerMine Test Edu" precisou ser atribuído ao cliente DM Colo no painel do cliente? O job rodou sem erros, mas o passo não foi registrado; continua em aberto.
- O que são os jobs "Unassigned" e como se relacionam com "roda para todos os clientes"?
- Por que um print mostra 1776 jobs se o Todd falou em 108?
- O que significa o tipo de contato `Turned Off`, que responde por cerca de 75% de `DTContactHistory` no banco de teste (9.302 de 12.344 contatos)?
- Para o dashboard: existe um padrão visual dos dashboards da Quorum (fonte, cores, tamanho dos números)? E o Handlebars do Superset da Quorum aceita HTML/CSS próprio, ou a sanitização remove? (ver [q-analytics/superset-e-seguranca.md](../q-analytics/superset-e-seguranca.md))

**Respondidas e removidas desta lista:** qual coluna o upsert usa como chave (README, 2026-10-28: a coluna de conflito do job, que deve coincidir com PRIMARY KEY ou UNIQUE do destino); como executar um job manualmente (README, 2026-10-28: botão Run, com confirmação; usado na prática em 2026-10-29); e se a tabela de destino precisa existir antes do job (formulário do job, 2026-10-29: sim, "Target tables must already exist"; confirmado ao criar a tabela antes do primeiro job).

A ideia de desfazer exclusões pelo X e a de ver, na aba Destination Servers, os pipelines e jobs que usam cada servidor estão em [q-analytics/ideias-de-melhoria.md](../q-analytics/ideias-de-melhoria.md) e não entraram na lista de perguntas.

## Sugestões de preparação (inferido, não combinado)

- No shadowing com Sam, priorizar o que só ele sabe: regras de negócio por trás dos relatórios e o histórico de decisões de modelagem. A base de negócio já está em [negocio/dominio-concessionaria.md](../negocio/dominio-concessionaria.md); no próximo encontro, aprofundar o mapa de tabelas do DMS e o catálogo de relatórios existentes.
- Reler o [glossário](../glossario.md) antes de abrir os repositórios e a ferramenta de ingestão (agente, container, pgAdmin 4, MSSQL, destination server, Query Executor, WO/RO, F&I) e, para o Superset, os termos novos (média ponderada, MTD, native filter, view).
- Anotar cada passo dado e as telas ao criar o pipeline pessoal, para transformar em um passo a passo documentado (começou em [q-analytics/pratica-ingestao-dev.md](../q-analytics/pratica-ingestao-dev.md)).
- Perguntar ao Sam se há um padrão visual para os dashboards do Q Analytics antes de investir mais na aparência do dashboard de teste (inferido).

## Lacunas e dúvidas

- Acessos e ambientes que o Eduardo já tem, em 2026-10-28: ferramenta de ingestão de produção (sem admin) e dev (com admin, segundo ele); pgAdmin 4 web e Postgres de teste; Superset Dev (admin). Falta registrar o acesso ao repositório, confirmar a conexão somente leitura do Postgres de produção e obter o acesso ao DMS de teste (nunca registrar os valores de acesso aqui).
- Prazo e prioridades do epic do Michael.
- Expectativa do Rick para o fim das primeiras duas semanas.
- O Sam continua sendo a referência dos relatórios depois que passar ao suporte? O shadowing foi combinado para quando exatamente?
