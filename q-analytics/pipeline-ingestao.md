---
titulo: Pipeline de ingestão de dados (ETL próprio)
resumo: Plataforma feita em casa que leva dados das fontes para o Postgres: configuração de clientes, agendamento de pipelines de extração e transformação, jobs (campos, formulário, agendamento, upsert, snapshots), destination servers, padrões dos jobs de produção do DealerMine, modos de execução, recursos e exemplos de fontes.
atualizado_em: 2026-10-29
fontes: [reunião com Todd, 2026-10-26 (../reunioes/2026-10-26-todd-tour-tecnico.md), ligação com Todd, 2026-10-27 (../reunioes/2026-10-27-todd-ligacao-ingestao.md), documento Word do Todd sobre o agente (resumido em agente-de-ingestao.md), deck "Analytics Platform" do Todd (slides 5, 6, 7 e 13, enviado em 2026-10-27), Eduardo (esclarecimentos, 2026-10-27; observações na versão dev da ferramenta, prints e texto do README do repositório da ferramenta, colados por ele em 2026-10-28; formulário de job, código de 5 jobs agendados do DealerMine de produção e primeiro job de teste rodado com sucesso, 2026-10-29), reunião com Rick, 2026-10-26 (linguagem Go) (../reunioes/2026-10-26-rick-kickoff.md)]
status: rascunho
---

# Pipeline de ingestão de dados (ETL próprio)

## Em palavras simples

É uma ferramenta que leva dados das fontes (bancos das concessionárias e APIs) para o Postgres, com painel de controle. Em cada concessionária fica um pequeno programa (o **cliente**, também chamado **agente**) que liga para o servidor central e fica na linha. Pela plataforma central a equipe configura os clientes e agenda os **pipelines** de extração e transformação; o servidor manda as tarefas ("busque as vendas novas"), o cliente executa no banco local, os dados são ajustados no caminho (campos, datas, regras) e gravados no destino. As tarefas podem ter horário e se repetir sozinhas, mas também podem ser rodadas à mão.

Na reunião, o Todd mostrou o Q Analytics e, por trás dele, esta ferramenta de ingestão: uma plataforma que permite **configurar os clientes, agendar pipelines de extração e transformação de dados** e outros recursos (Eduardo, 2026-10-27).

**Para ver o fluxo de ponta a ponta, o passo a passo de implantação, os ambientes de teste e os cuidados (o X pode apagar jobs), leia [agente-de-ingestao.md](agente-de-ingestao.md). O diário da prática do Eduardo está em [pratica-ingestao-dev.md](pratica-ingestao-dev.md).**

## Por que foi feito em casa

Nenhuma ferramenta pronta atendeu aos requisitos de **segurança e flexibilidade** (Todd). O Granola registra também que o novo ETL **evita a necessidade de liberar (whitelist) endereços no firewall**, algo que antes era um risco de segurança com o agente do Synapse (o Eduardo disse que a estrutura antiga não é o foco).

## Como conecta

- **Clientes** são instalados **por concessionária ou por ambiente** e **conectam de volta ao servidor central via WebSockets** (Todd).
- Como é o cliente que faz a conexão de saída, **não é preciso liberar a entrada no firewall** da concessionária. O guia do agente confirma que ele só precisa de acesso de saída à porta 443 (`wss`) ou 80 (`ws`).
- O primeiro registro do agente usa um **token de uso único** gerado no painel; depois o servidor reconhece o agente por um token permanente guardado no cofre de credenciais do Windows. Passo a passo em [agente-de-ingestao.md](agente-de-ingestao.md).
- A linguagem da ferramenta é **Go** (Rick; confirmado pelo repositório, que o Eduardo descreveu como servidor + cliente + painel em React).

## Conectividade (deck, slide 5)

- **Bancos atrás de firewall:** a ingestão segura alcança bancos operacionais que não estão expostos à internet pública.
- **Múltiplos endpoints de API:** uma fonte pode combinar vários endpoints para coletar entidades, transações e dados de referência relacionados.
- **Endpoints configurados sob medida:** caminhos, parâmetros e mapeamentos configuráveis para integrações específicas de produto ou de cliente.

## Transformação guiada por configuração (deck, slide 6)

Quatro passos para converter dados variados em estruturas analíticas consistentes:

1. **Map:** variáveis de API e colunas de origem para campos do warehouse.
2. **Standardize:** definir datas, tipos de dados, valores padrão e normalização.
3. **Manipulate:** aplicar funções reutilizáveis em nível de campo e de registro.
4. **Persist:** inserir registros novos ou fazer upsert dos existentes, com resolução de conflito.

## Modelo de orquestração: clientes > pipelines > jobs

| Nível | O que é (segundo as notas, o deck, o Word e o README) |
|---|---|
| Cliente | O registro, na ferramenta, de uma concessionária ou ambiente; o programa instalado nela é o **agente**, ligado ao registro pelo `client_id`. Depois de implantado, a configuração se concentra nos jobs. Para APIs, um cliente é a "porta de entrada" configurada conforme o sistema (Zoho, Powerlane etc.) |
| Pipeline | Sequência definida de jobs relacionados. **Pipelines são entidades globais** e são **atribuídos aos clientes** no painel do cliente (README; o Word diz que são atribuídos ao agente na implantação) |
| Job | A unidade que roda: uma tarefa de extração, transformação ou carga. Um job roda **uma vez para todos os clientes associados** (Todd). Num cliente de API, vários jobs podem chamar APIs diferentes |

Há pipelines **incrementais** (só o que mudou) e **históricos** (carga do passado) (Todd; ver glossário). Os já existentes não têm backup e são trabalho em andamento.

**Modos de execução (deck, slide 7):**

| Modo | O que faz |
|---|---|
| Scheduled | Execução recorrente por horário |
| Client | Roda todo o trabalho configurado para um cliente |
| Pipeline | Roda uma sequência definida de jobs relacionados |
| Job | Roda uma única tarefa |
| Manual / recovery | Execução dirigida, para validação ou reinício |

O deck diz que um framework comum de execução melhora a repetibilidade, a visibilidade operacional e o controle de recuperação.

Escala: **256 bancos de concessionárias** gerenciados (Todd, que é a referência técnica; o "cerca de 250" do Rick é aproximação) e **108 jobs** no total, entre incrementais e de produção (ligação de 2026-10-27). Um print da ferramenta mostrou um total de **1776 jobs**; a diferença não foi explicada (dúvida, ver lacunas).

Tipos de fonte suportados: **SQL, API e endpoints configurados sob medida**.

## Como a ferramenta funciona (README, resumido em 2026-10-28)

O Eduardo colou o README do repositório da ferramenta e prints das telas, só para contextualização. O texto bruto e o repositório não ficam arquivados aqui; o que segue é resumo. **A tela da versão dev é mais nova que o README** (por exemplo, o README não descreve a escolha de destination server por job).

**Tipos de job:** `db` (lê de um banco) e `api` (chama uma API).

**Campos de um job de banco:**

| Campo | Para que serve |
|---|---|
| Client ID | Em qual cliente o job roda. **Em branco = todos os clientes do pipeline** |
| Pipeline | A qual pipeline o job pertence |
| Database config (escopo e ids) | Qual configuração de banco do cliente usar |
| Query de origem | O `SELECT` que roda na fonte |
| Table | Tabela de destino (aceita schema.tabela) |
| Conflict column(s) e conflict action | Chave do upsert e o que fazer no conflito: `nothing` (ignora) ou `update` (atualiza). **A chave precisa coincidir com uma PRIMARY KEY ou UNIQUE da tabela de destino** |
| Now columns | Colunas preenchidas com a data e hora da carga |
| Variáveis `{{chave}}` | Valores que entram na query |
| Last run variable | Marca do ponto da última carga (ver abaixo) |
| Enabled | Liga e desliga o job |
| Schedule | Quando roda (ver abaixo) |

**Last run variable (carga incremental):** guarda o ponto até onde o job já leu (por exemplo, uma data) e o entrega à query na rodada seguinte. Tem valor inicial e três formas de ser atualizada: por timestamp, por expressão sobre o destino ou por script na origem.

**Agendamento:** manual, uma data/hora, a cada N minutos ou horas, a cada N dias com horário, ou em dias da semana com horário. **Sem agendamento, o job roda quando o cliente conecta.** Na tela existe a opção "Manual only".

**Gravação:** com chave de conflito, usa **tabela intermediária (staging) e merge**; colunas `GENERATED` do destino são ignoradas. O README também traz a tabela de **mapeamento de tipos** SQL Server para Postgres (consultar no README quando um tipo falhar).

**Execução manual:** botão Run (▶) no job, com diálogo de confirmação; o job passa por Queued, Running e depois mostra o resultado. Há também **Run All**, que roda tudo e deve ser evitado.

**Observabilidade:** Runtime Logs, Data Transfer History, Active Jobs e **Query Executor** (consulta ao vivo a um banco de origem de um cliente). Há também **Runtime Capacity** (limites de execução simultânea; não mexer).

**Snapshots de configuração e GitHub:** é possível guardar snapshots da configuração (pipelines, atribuições de pipeline a cliente e jobs; **não** inclui credenciais nem as marcas de "last run") e exportar, aplicar ou restaurar via GitHub. Aplicar um snapshot sobrescreve a configuração: não usar sem alinhar.

**Acesso e segurança:** login local ou OIDC; tokens de enrollment (diálogo Deploy) e credenciais de worker.

**Destination servers** são uma aba própria (ver abaixo).

## Formulário de job na versão dev (observado em 2026-10-29)

O Eduardo abriu o formulário de job (título "Edit Job", usado também para criar) e mostrou a parte de cima. O que há nela:

| Seção ou campo | O que faz |
|---|---|
| Job Type | Escolha do tipo, por exemplo "Database (SQL)" |
| Show Code | Botão no topo; abre o código do job (não foi explorado) |
| SQL Query (obrigatório) | A consulta de origem. O exemplo em cinza é `SELECT TOP 10000 * FROM MyTable` |
| Target Table (obrigatório) | Nome da tabela de destino. O exemplo em cinza é `my_table` |
| Destination Servers | Sem seleção, grava no PostgreSQL padrão. Aceita **até 8 servidores**, cada um com **a sua tabela e as suas colunas de override**. O formulário avisa: **"Target tables must already exist"** (a tabela de destino precisa existir antes) |
| Destination Table Override | Nome da tabela naquele destino; o Eduardo usa o formato `schema.tabela` |
| Destination Column Overrides | Pares coluna de origem e coluna de destino; colunas sem par mantêm o nome; **as chaves de conflito seguem esses overrides**. Para jobs de API, usam-se os nomes do mapa de campos da API, não os caminhos da API |
| Add Destination e Remove Destination | Acrescenta ou tira um destino do job |

Os campos de cliente, pipeline, conflito, agendamento, variáveis e last run ficam mais abaixo no formulário e **ainda não foram registrados em detalhe**. Abrir sempre pelo **+ Add Job** do pipeline do Eduardo, nunca pelo lápis de um job existente.

**Confirmado na prática (2026-10-29), com o primeiro job do Eduardo:**

- **Destino com override `schema.tabela`:** o job grava, pelo destination server do Eduardo, na tabela `dt_contact_daily` do schema `test_edu`, e rodou **sem erros** (o formato `schema.tabela` funciona no override).
- **Tabela criada antes:** a tabela de destino foi criada manualmente no Postgres antes do job, como o formulário exige, com uma PRIMARY KEY igual às colunas de conflito do job.
- **Upsert:** duas execuções do mesmo job mantiveram o mesmo total de linhas (675) e, depois de acrescentar uma coluna no `SELECT` (e no destino, com `ALTER TABLE ... ADD COLUMN`), uma nova execução a preencheu pela ação `update`. A tabela de destino precisa ganhar a coluna antes de o job passar a enviá-la.
- **Execução:** manual, pelo botão Run (▶) do job, com agendamento "Manual only".
- **Atribuição do pipeline ao cliente:** o job rodou, mas o Eduardo não registrou se foi preciso atribuir o pipeline ao cliente DM Colo no painel do cliente (dúvida mantida).

O passo a passo, o `SELECT` e a tabela estão em [pratica-ingestao-dev.md](pratica-ingestao-dev.md).

## Padrões nos jobs de produção do DealerMine (observados em 2026-10-29)

O Eduardo colou o código de 5 jobs agendados do pipeline DealerMine de produção, só para entender os padrões. O código **não fica arquivado** (traz nomes de bancos e de servidores internos); segundo o Todd, via Eduardo, todas essas consultas e procedimentos estão no GitHub. Resumo:

| Job (resumo) | O que faz |
|---|---|
| 1 | Contagens do dia de alvos de agendamento por tipo de chamada (confirmação, CSI, recalls, manutenção etc.), loja e filial, em vários bancos de concessionárias |
| 2 | Relatório mensal da central de agendamento (ligações, tempo, agendamentos, meta), com tabelas temporárias. Tinha datas e um banco fixados no texto, parece uma cópia de trabalho `(inferido)` |
| 3 | Contatos por mês, loja e filial para um banco (usa a variável `{{dbname}}`) |
| 4 | O mesmo que o 3, para vários bancos; limita a 100 mil linhas por banco |
| 5 | Contatos por dia, tipo, motivo, agenda e sentido; **incremental**: relê a partir de um dia antes da última execução (`{{last_run}}`) |

**Padrões que se repetem:**

1. **Agrega na origem** (`GROUP BY` com contagens) e carrega só o resultado; não copia tabelas inteiras.
2. **Vários bancos de uma vez:** os jobs 1, 4 e 5 montam uma consulta com `UNION ALL`, percorrendo a lista de bancos das concessionárias e chamando cada um por linked server, a partir de um banco central que tem as tabelas de ligações (`twilio_*`) e o cadastro de bancos. Isso não existe no banco de teste do DM Colo.
3. **Variáveis do job:** `{{datestart}}`, `{{dateend}}`, `{{dbname}}` e `{{last_run}}`. O nome `last_run` ser a Last run variable do formulário é `(inferido)`.
4. **Incremental com sobreposição:** o job 5 volta um dia antes da última execução, e o upsert evita duplicar.
5. **Chave composta:** as colunas de agrupamento (banco, loja, filial, data, tipo) formam a chave do upsert `(inferido)`. Nulos e vazios são trocados por textos como `(blank)` e `unknown` antes de gravar.
6. **Saída pronta para o Postgres:** colunas em minúsculas e snake_case, mais `created_at` e `updated_at` no próprio `SELECT`.
7. **Assunto:** a central de agendamento (BDC) das concessionárias: contatos por tipo (agendar, confirmar, recall, manutenção, CSI, venda e outros) por loja e filial. A hierarquia nos dados é banco, loja (`storeno`) e filial (`branchno`).
8. Um job consulta uma tabela de usuários cujo nome sugere senhas, só para filtrar por tipo de usuário; não ler esses dados.

O primeiro job do Eduardo aplicou os padrões 1, 5 e 6 (agregar na origem, normalizar nulos para chave composta e saída em snake_case), em escala de teste, sobre uma única tabela de contatos (ver [pratica-ingestao-dev.md](pratica-ingestao-dev.md)).

## Destination servers e anatomia de um job (observado na versão dev, 2026-10-28)

O que o Eduardo viu ao explorar a versão dev da ferramenta (relato dele; não é documentação oficial da ferramenta):

**Destination servers.** A aba **Destination Servers** lista os servidores onde os jobs podem gravar. Cada um tem um tipo (SQL Server ou PostgreSQL), um servidor e um database; as credenciais ficam cifradas. Antes da prática do Eduardo havia três na dev:

| Destination server | Tipo | Database de destino |
|---|---|---|
| DMColoBabelfish | SQL Server | Um database SQL Server de teste do Todd |
| DMColoMSSQL | SQL Server | O mesmo database SQL Server de teste |
| DMColoPG | PostgreSQL | `babelfish_db` (inferido: o mesmo do Postgres de teste visto no pgAdmin) |

O Eduardo criou depois o **DMColoPGTestEdu**, PostgreSQL apontando para o `datamigration` (ver [pratica-ingestao-dev.md](pratica-ingestao-dev.md)). Como o Eduardo observou, a aba não mostra quais pipelines e jobs usam cada servidor (ideia de melhoria em [ideias-de-melhoria.md](ideias-de-melhoria.md)).

**Pipelines do cliente DM Colo na dev:**

- **DealerMine Historical** e **DealerMine Incremental:** testes do Todd com o SQL Server, segundo o Eduardo. O Eduardo não os detalhou.
- **Unassigned:** jobs que não pertencem a nenhum pipeline. Havia dois rodando.
- **DealerMine Test Edu:** criado pelo Eduardo; recebeu o primeiro job em 2026-10-29, que rodou sem erros.

**Anatomia de um job**, a partir de um job Unassigned que roda para todos os clientes:

| Campo | O que o job tinha |
|---|---|
| Query de origem | Um `SELECT` sem filtro (lê a tabela inteira) de colunas de ordem de serviço (número, data, código da operação, causa, reclamação, correção, loja, filial e outras) da tabela `dbo.CCCMaster` do banco de teste do DealerMine |
| Tabela alvo | `dbo.CCCMaster` |
| Destination servers | Dois: o SQL Server (DMColoMSSQL) e o Postgres (DMColoPG) |
| Override da tabela de destino | No Postgres, a tabela grava com outro nome: schema com o prefixo do banco de origem seguido de `_dbo`, tabela `cccmaster` em minúsculas |
| Modo de gravação | Upsert |
| Outras configurações | Existem, mas o Eduardo não as detalhou |

O outro job Unassigned parecia ter os destinos desconfigurados; o Eduardo não o considera importante e ele não deve ser tocado.

**Leituras `(inferido)`:**

- O job existe para gravar os mesmos dados no SQL Server e no Postgres (via Babelfish) e comparar, no teste do Todd sobre usar o SQL Server como destino (deck, slide 13).
- O formato do schema do override segue o jeito como o Babelfish representa os schemas do SQL Server no Postgres (conhecimento geral).
- "CCC" são as três colunas de texto: Complaint, Cause, Correction (reclamação, causa e correção) de uma ordem de serviço. Os campos de texto livre podem conter dados de clientes; por isso não se copiam linhas dos dados de teste.

## Recursos principais

| Recurso | O que faz | Nota |
|---|---|---|
| Tratamento de conflito (upsert) | Se o registro já existe, atualiza; se não, insere | Ver glossário. Chave = coluna de conflito do job (README). **Confirmado na prática em 2026-10-29:** duas execuções mantiveram 675 linhas e uma coluna acrescentada depois foi preenchida pela ação `update` |
| Cargas em stage (staged loads) | Dados chegam primeiro a uma tabela intermediária e depois são mesclados no destino | README; há também um esquema de staging no Postgres |
| Override da tabela de destino | Muda o nome (e o schema) com que a tabela chega a um destination server | Visto num job existente, 2026-10-28; **o formato `schema.tabela` funcionou no job do Eduardo (2026-10-29)**; ver glossário |
| Change tracking | Detecta o que mudou na origem; é o método de sincronização recomendado pela Microsoft | Confirmado para **SQL Server**: a conta do agente precisa da permissão `VIEW CHANGE TRACKING` (guia do agente). Para as outras fontes, o README só mostra a variável de última execução (`last_run_var`) |
| Agendamento | Manual, uma vez, por intervalo, diário ou por dias da semana | Todd; README |
| API fan-out | Consulta o Postgres para obter a lista de concessionárias e repete a ingestão para cada uma | |
| Integração com GitHub | Snapshots e backup/restauração de jobs e pipelines. Na ligação de 2026-10-27: mudanças gerenciadas via Git em todos os jobs e clientes | README: snapshots de configuração com exportar, aplicar e restaurar. Os pipelines incrementais e históricos atuais não têm backup (ver lacunas) |
| Query Executor | Permite consultar ao vivo os bancos de origem para auditoria | README e tela da dev |
| Lotes (batch) | O agente envia por padrão 500 linhas por mensagem WebSocket | Guia do agente |

## Exemplos de fontes ingeridas

O Todd citou como **exemplos** de fontes (não é uma lista fechada, segundo o Eduardo): **QDMS, AutoVance, Communicator, DealerMine, Powerlane, Zoho CRM, Zoho Desk e Carbon60 (dados de BDC, New Brunswick)**. A nota do Granola escreveu "DealerMind"; a correção para DealerMine foi confirmada pelo Eduardo.

Na ligação de 2026-10-27, o Todd acrescentou que o **Zoho (CRM e Desk) é interno**, usado pela própria Quorum, e não uma fonte de dados de clientes; e que o **Powerlane** é ingerido por **API** (um cliente, vários jobs). Cada cliente de API é configurado conforme o sistema (Zoho, Veeva e Powerlane diferem). Para testes existe um banco **MSSQL do DealerMine** com dados reais exportados.

Relação com a cobertura por produto (diagrama do Rick): [produtos/marcas.md](../produtos/marcas.md).

## Onde os dados pousam

No Postgres, em **esquemas separados por produto**, mais um **esquema de staging**. Ver [postgres-e-resumos.md](postgres-e-resumos.md). O deck informa que a ingestão está evoluindo, incluindo **Microsoft SQL Server como banco de destino**; na ligação, o Todd disse que o **destino SQL Server já está disponível** (ver [arquitetura.md](arquitetura.md)). Na versão dev, o destino de cada job é definido pelos destination servers (seção acima); no Postgres de teste, ver [ambiente-de-teste-postgres.md](ambiente-de-teste-postgres.md).

## Documentação e código

- O código e os READMEs da ferramenta estão nos repositórios do **QAnalytics no GitHub**. O Eduardo viu o README da ferramenta (colado por ele em 2026-10-28) e não pretende expor o repositório ao assistente.
- Há um **documento Word** do Todd sobre o processo do agente de ingestão, resumido em [agente-de-ingestao.md](agente-de-ingestao.md).
- Não há documentação conhecida das **tabelas do DealerMine**; o Eduardo perguntou ao Todd em 2026-10-29 (sem resposta até agora).

## Lacunas e dúvidas

As dúvidas mais importantes sobre o processo (passar do teste para produção, falhas, change tracking das outras fontes) estão em [agente-de-ingestao.md](agente-de-ingestao.md). Aqui ficam as demais:

- No deck, "Client" aparece como topo da hierarquia; tudo indica ser o registro do cliente ligado ao agente por `client_id`, a confirmar com o Todd.
- O que significa "Unassigned" (jobs sem pipeline) e como ele se relaciona com "roda para todos os clientes"? O README diz que Client ID em branco roda em todos os clientes do pipeline.
- O pipeline "DealerMine Test Edu" precisou ser atribuído ao cliente DM Colo no painel do cliente? O job rodou sem erros, mas o passo não foi registrado e o Eduardo não respondeu; continua em aberto.
- Por que um print mostra 1776 jobs se o Todd falou em 108?
- Segurança do canal WebSocket: o Word mostra `wss` (TLS) e token por cliente; faltam detalhes de rotação e revogação de tokens.
- Que funções reutilizáveis de transformação existem (deck, "Manipulate")? Os jobs de produção fazem a transformação dentro do `SELECT` (agregação, normalização de nulos, colunas de data e hora) e usam variáveis; não se viu um catálogo de funções.
- (baixa prioridade) O que é Carbon60 e como se relaciona com a Quorum? BDC pode significar "Business Development Center" (inferido; os jobs de produção tratam de contatos e agendamentos da central de agendamento).
