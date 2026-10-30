---
titulo: Glossário (técnico e de negócio)
resumo: Conceitos técnicos e termos do negócio de concessionárias que aparecem nas reuniões, explicados em português simples, separando a explicação geral do que a Quorum faz.
atualizado_em: 2026-10-29
fontes: [reunião com Todd, 2026-10-26 (reunioes/2026-10-26-todd-tour-tecnico.md), ligação com Todd, 2026-10-27 (reunioes/2026-10-27-todd-ligacao-ingestao.md), documento Word do Todd sobre o agente (q-analytics/agente-de-ingestao.md), deck "Analytics Platform" do Todd (2026-10-27), reunião com Rick, 2026-10-26 (reunioes/2026-10-26-rick-kickoff.md), conversa com Sam, 2026-10-29 (reunioes/2026-10-29-sam-regras-de-negocio.md), Eduardo (Postgres de teste, orientação do Todd, exploração da versão dev da ferramenta de ingestão, README e prints da ferramenta, 2026-10-28; primeiro job na ingestão dev e dashboard de teste no Superset Dev, 2026-10-29: q-analytics/pratica-ingestao-dev.md e q-analytics/superset-e-seguranca.md), conhecimento técnico geral de quem documentou]
status: rascunho
---

# Glossário (técnico e de negócio)

Este é o **glossário único** da documentação: reúne os termos técnicos (dados, infraestrutura) e os termos do negócio de concessionárias e do DMS. (O antigo `glossario-negocio.md` foi incorporado aqui em 2026-10-29.)

Como ler cada verbete:

- **Geral** é explicação de conhecimento geral (não veio de reunião, vale para qualquer empresa).
- **Na Quorum** é o que foi dito por alguém da empresa, com a fonte indicada. Se algo for suposição, vem marcado com `(inferido)`.
- O termo fica em inglês, como é falado nas reuniões, com a pronúncia ou tradução quando ajuda.

Ordem alfabética. Novos termos entram aqui sempre que surgirem em reuniões (ver [00-guia-da-documentacao.md](00-guia-da-documentacao.md)). Contexto do negócio em [negocio/dominio-concessionaria.md](negocio/dominio-concessionaria.md).

## Agente (agent)

- **Geral:** programa instalado perto de uma fonte de dados que executa tarefas pedidas por um servidor central e devolve o resultado.
- **Na Quorum:** o programa que a equipe de infraestrutura instala nos servidores das concessionárias para a ferramenta de ingestão; roda como serviço do Windows, liga para o servidor central e recebe os jobs (Todd, ligação de 2026-10-27; Word do Todd). Na ferramenta, o agente se liga a um "cliente" pelo `client_id`. Ver [q-analytics/agente-de-ingestao.md](q-analytics/agente-de-ingestao.md).

## API

- **Geral:** forma de um sistema pedir dados a outro por uma interface combinada, em vez de acessar o banco diretamente.
- **Na Quorum:** uma das fontes que o ETL suporta, além de SQL e endpoints sob medida (Todd). Cada cliente de API é configurado conforme o sistema (Zoho, Powerlane e Veeva diferem). Ver [q-analytics/pipeline-ingestao.md](q-analytics/pipeline-ingestao.md).

## API fan-out

- **Geral:** pegar uma lista de itens e fazer a mesma chamada para cada um ("abrir em leque").
- **Na Quorum:** o ETL consulta o Postgres para obter a lista de concessionárias e repete a ingestão de API para cada uma (Todd).

## Babelfish

- **Geral:** recurso que faz um banco Postgres "entender" o protocolo e o dialeto T-SQL do SQL Server, para que aplicações feitas para SQL Server conversem com o Postgres sem reescrita total. Costuma ser usado em migrações de SQL Server para Postgres. Nesse recurso, um schema do SQL Server costuma aparecer no Postgres como um schema de nome composto: o nome do database do SQL Server, um sublinhado e o nome do schema (por exemplo, `<banco>_dbo`). (Definição geral de quem documentou.)
- **Na Quorum:** existe um database chamado `babelfish_db` na instância de teste do Postgres. O Todd disse só que os outros databases dali (fora o `datamigration`) ele usa para testar a migração de MS SQL para Postgres; a relação do nome com o recurso Babelfish não foi confirmada `(inferido)`. Na ferramenta de ingestão dev, o destination server Postgres do cliente DM Colo aponta para o `babelfish_db`, e um job grava ali numa tabela com schema nesse formato `(inferido)`. Ver [q-analytics/ambiente-de-teste-postgres.md](q-analytics/ambiente-de-teste-postgres.md) e [q-analytics/pipeline-ingestao.md](q-analytics/pipeline-ingestao.md).

## BDC

- **Geral:** em concessionárias, costuma ser "Business Development Center", o setor que atende contatos e leads (inferido).
- **Na Quorum:** o Todd citou "Carbon60 (BDC data, New Brunswick)" como exemplo de fonte. Significado exato a confirmar. O site público também lista "BDC" como área de solução. Os jobs de produção do DealerMine tratam de contatos e agendamentos da central de agendamento, que parece ser esse setor `(inferido)`.

## Benchmarking (comparação entre clientes)

- **Geral:** comparar o desempenho de uma empresa com o de outras do mesmo grupo ou segmento.
- **Na Quorum:** o lado interno do Q Analytics (equivalente ao Q Insights) permite comparar clientes, por exemplo dizer a uma concessionária GM de uma província qual é a média de veículos vendidos no mês (Sam). Se o painel já existe no Superset é dúvida. Ver [q-analytics/visao-geral.md](q-analytics/visao-geral.md).

## Body shop (funilaria)

- **Geral:** oficina de reparo de carroceria e pintura.
- **Na Quorum:** departamento que nem toda concessionária tem; tem seu próprio conjunto de tabelas no DMS, que também grava na contabilidade (Sam).

## Bootstrap token (token de enrollment)

- **Geral:** credencial temporária, de uso único, usada só para registrar um programa pela primeira vez; depois ela é trocada por uma credencial permanente.
- **Na Quorum:** gerado no painel do servidor, no diálogo "Deploy" do cliente, e colado no `config.json` do agente; no primeiro start, o agente o troca por um token permanente e apaga o de bootstrap (Word do Todd). Ver [q-analytics/agente-de-ingestao.md](q-analytics/agente-de-ingestao.md).

## Cache e Redis

- **Geral:** cache é guardar uma resposta já calculada para não recalcular. Redis é um banco em memória muito usado para isso.
- **Na Quorum:** o Superset usa Redis para cache junto com o carregamento preguiçoso das páginas dos dashboards (Todd). Ver [q-analytics/superset-e-seguranca.md](q-analytics/superset-e-seguranca.md).

## Change tracking

- **Geral:** recurso do SQL Server que registra o que mudou em cada tabela, para sincronizar só as diferenças. Quem lê precisa de uma permissão específica (`VIEW CHANGE TRACKING`).
- **Na Quorum:** o ETL o usa; o Todd diz que é o método de sincronização recomendado pela Microsoft. Antes não havia sincronizações incrementais (Todd). O guia do agente exige essa permissão na conta de banco do agente (Word do Todd).

## Cliente, pipeline e job (no ETL)

- **Na Quorum:** modelo de orquestração do ETL: **clientes** > **pipelines** > **jobs** (Todd). Um job roda uma vez para todos os clientes associados. O deck acrescenta os modos de execução (agendado, por cliente, por pipeline, por job, manual/recuperação). Na ligação de 2026-10-27: o programa instalado na concessionária é o **agente** (ligado ao registro do cliente pelo `client_id`); depois de implantado o cliente, a configuração se concentra nos jobs; e num cliente de API (Zoho, Powerlane) um cliente tem vários jobs com chamadas diferentes. Segundo o README da ferramenta, os pipelines são entidades globais, atribuídas aos clientes no painel do cliente, e um job com Client ID em branco roda em todos os clientes do pipeline. Na versão dev (Eduardo, 2026-10-28), os jobs que não estão em nenhum pipeline aparecem agrupados como **Unassigned**. Na prática de 2026-10-29, o Eduardo criou um job dentro do pipeline pessoal dele e o job rodou sem erros. Detalhes em [q-analytics/pipeline-ingestao.md](q-analytics/pipeline-ingestao.md) e [q-analytics/agente-de-ingestao.md](q-analytics/agente-de-ingestao.md). Atenção: "cliente" na ferramenta é o registro do agente; para o negócio, o cliente é a concessionária (ver [clientes/concessionarias.md](clientes/concessionarias.md)).

## Colocation (co-lo)

- **Geral:** a empresa tem seus próprios servidores dentro de um data center de terceiros, que fornece espaço, energia, refrigeração e rede.
- **Na Quorum:** o Q Analytics roda autohospedado no data center de colocation em Calgary (Todd). O nome "DM Colo" do cliente do ambiente de migração de dados provavelmente se refere a isso `(inferido)`. O Sam também citou "Colo" ao falar do acesso ao servidor de teste do DMS (ver "QIS Connector").

## Config snapshot (snapshot de configuração)

- **Geral:** cópia salva da configuração de um sistema num dado momento, que permite voltar atrás ou levar a configuração de um ambiente para outro.
- **Na Quorum:** a ferramenta de ingestão guarda snapshots da configuração (pipelines, atribuições de pipeline a cliente e jobs; não credenciais nem as marcas de "last run") e permite exportar, aplicar e restaurar via GitHub (README). Aplicar um snapshot sobrescreve a configuração atual. Se é o caminho oficial de promoção do teste ao produção é dúvida (Rodada 3, 1). Ver [q-analytics/pipeline-ingestao.md](q-analytics/pipeline-ingestao.md).

## Conflict handling / upsert

- **Geral:** "upsert" = update + insert. Se o registro já existe, atualiza; se não existe, insere. Evita duplicar dados quando a mesma carga roda de novo. Para saber se o registro já existe, é preciso uma **chave** (uma PRIMARY KEY ou UNIQUE na tabela de destino).
- **Na Quorum:** o ETL tem tratamento de conflito com lógica de upsert (Todd; deck, slide 6 "Persist"). No job, a chave é a **conflict column** e a ação em conflito é `nothing` (ignora) ou `update` (atualiza); a coluna precisa coincidir com a PRIMARY KEY ou UNIQUE do destino (README). **Confirmado na prática (Eduardo, 2026-10-29):** duas execuções do mesmo job mantiveram o mesmo número de linhas no destino (sem duplicar), e, ao acrescentar uma coluna no `SELECT` e rodar de novo, a coluna nova foi preenchida nas linhas existentes pela ação `update`. Ver [q-analytics/pratica-ingestao-dev.md](q-analytics/pratica-ingestao-dev.md).

## Container

- **Geral:** pacote leve que leva um programa e tudo o que ele precisa para rodar, isolado do resto do servidor (Docker é o exemplo mais conhecido).
- **Na Quorum:** o cliente do servidor de teste da ferramenta de ingestão roda num container no mesmo servidor (Todd, ligação de 2026-10-27).

## Cookie cutter (esquema padrão)

- **Geral:** expressão em inglês para algo feito com o mesmo molde, idêntico em todas as cópias.
- **Na Quorum:** analogia do Sam: todas as concessionárias têm o **mesmo esquema** de banco (mesmas tabelas e campos), mas cada uma com seus próprios dados e seu próprio servidor. Ver [clientes/concessionarias.md](clientes/concessionarias.md).

## Database (banco de dados) x schema

- **Geral:** um servidor Postgres pode conter vários **databases**, isolados entre si, e cada database contém vários **schemas** (pastas de tabelas). Todo Postgres novo já vem com um database chamado `postgres`, usado como padrão para administração e conexão inicial. Um schema novo nasce vazio, e o schema `public` é o padrão compartilhado.
- **Na Quorum:** a instância de teste do Postgres tem três databases: `babelfish_db`, `datamigration` e `postgres`. O `datamigration` é do ambiente de teste, onde o Eduardo trabalha, e o Todd sugeriu que ele crie o próprio schema ali (Todd, via Eduardo, 2026-10-28). O Eduardo criou o schema `test_edu` no mesmo dia. Ver [q-analytics/ambiente-de-teste-postgres.md](q-analytics/ambiente-de-teste-postgres.md).

## Dataset, métrica, chart e dashboard (no Superset)

- **Geral:** no Apache Superset, o **dataset** é a tabela (ou consulta) que o Superset enxerga; a **métrica** é uma medida reutilizável (por exemplo, soma de vendas), que pode ser uma expressão SQL própria (Custom SQL); o **chart** é um gráfico individual (o **Big Number** mostra um número grande, com subtítulo e formato configuráveis); o **dashboard** reúne vários charts e filtros numa página. Depois de alterar a tabela de origem (por exemplo, acrescentar uma coluna), é preciso sincronizar as colunas do dataset ("Sync columns from source"). Para copiar um dashboard existente usa-se "Save as".
- **Na Quorum:** o deck descreve a cadeia Postgres > datasets > métricas > charts > dashboards (slide 9). Na prática de 2026-10-29, o Eduardo montou no Superset Dev um dataset sobre `test_edu.dt_contact_daily`, 10 charts e um dashboard com dois filtros (versão 1), com nomes em inglês e prefixo "DM Colo - ". Ver [q-analytics/superset-e-seguranca.md](q-analytics/superset-e-seguranca.md).

## Dealer (banco "Dealer")

- **Na Quorum:** banco de dados presente em todo servidor de cliente, usado como banco de entrada; grupos de concessionárias têm bancos adicionais por loja, com o nome da loja (Sam). Ver [clientes/concessionarias.md](clientes/concessionarias.md).

## Dealer group (grupo de concessionárias)

- **Geral:** empresa que controla várias concessionárias.
- **Na Quorum:** clientes de grande porte, com várias lojas e marcas, também chamados de enterprise groups (Sam). O site tem uma área de contas empresariais (liderança em [pessoas/diretorio.md](pessoas/diretorio.md)).

## Descope (SSO e OAuth)

- **Geral:** Descope é um serviço de autenticação de terceiros (provedor de identidade) que oferece login, SSO e OAuth. (Definição geral; o deck da Quorum só o nomeia.)
- **Na Quorum:** o Descope, por OAuth, faz o SSO do Q Analytics e também autentica o acesso por MCP (deck, slide 13). Só o Q Analytics o usa hoje; a intenção é estender a todos os produtos (Todd). **Correção:** o Granola escreveu "dScope" na nota da reunião; o deck do Todd confirma a grafia **Descope**. Ver [q-analytics/superset-e-seguranca.md](q-analytics/superset-e-seguranca.md).

## Destination server

- **Geral:** em ferramentas de ingestão, é o cadastro de um banco onde os dados podem ser gravados, com o tipo do banco (por exemplo, SQL Server ou PostgreSQL), o servidor e o database. Um job escolhe em quais destination servers grava.
- **Na Quorum:** a ferramenta de ingestão tem uma aba **Destination Servers**. Na versão dev havia três antes de 2026-10-28 (dois de SQL Server e um de PostgreSQL apontando para o `babelfish_db`), e o Eduardo criou um quarto, o DMColoPGTestEdu, para o `datamigration` (Eduardo, 2026-10-28). Um job pode gravar em mais de um (até 8, cada um com tabela e colunas de override). O formulário avisa que as tabelas de destino já devem existir. Ver [q-analytics/pipeline-ingestao.md](q-analytics/pipeline-ingestao.md).

## DMS (Dealer Management System)

- **Geral:** sistema que a concessionária usa para operar vendas, serviço, peças e contabilidade.
- **Na Quorum:** o Quorum DMS é, na descrição do Sam, um software de contabilidade que recebe os dados de todas as áreas num livro-razão compartilhado. Ver [produtos/quorum-dms.md](produtos/quorum-dms.md).

## DMS agnostic

- **Na Quorum:** o Sam disse que o AutoVance é "DMS agnostic": funciona com o Quorum DMS ou com DMS de concorrentes. Ver [produtos/marcas.md](produtos/marcas.md).

## ETL

- **Geral:** Extract, Transform, Load: extrair dados da origem, transformá-los (limpar, ajustar) e carregá-los no destino.
- **Na Quorum:** feito por uma ferramenta própria, escrita em Go (Todd; Rick). Ver [q-analytics/pipeline-ingestao.md](q-analytics/pipeline-ingestao.md).

## F&I (Finance and Insurance)

- **Geral:** escritório de negócios da concessionária que cuida de financiamento, seguros e papelada na venda de um veículo.
- **Na Quorum:** etapa da jornada do cliente, depois do agendamento de venda (Sam). O Autovance Menu apresenta o menu de F&I (ver [produtos/marcas.md](produtos/marcas.md)).

## Firewall e whitelist

- **Geral:** firewall é a barreira de rede que controla quem entra e sai. Whitelist é a lista de endereços liberados.
- **Na Quorum:** o novo ETL evita ter de liberar endereços no firewall das concessionárias (algo que antes era um risco de segurança com o agente do Synapse, segundo o Granola) porque o cliente conecta de volta, de dentro para fora, via WebSockets (Todd). O guia do agente confirma que só é preciso acesso de saída às portas 443 (`wss`) ou 80 (`ws`).

## Fixed ops e variable ops

- **Geral:** *fixed operations* são serviço e peças (receita recorrente); *variable operations* são a venda de veículos (varia mais).
- **Na Quorum:** o site usa esses termos para descrever o que o DMS cobre ([produtos/marcas.md](produtos/marcas.md)).

## Flat rate x preço por hora

- **Geral:** no *flat rate*, o serviço tem um tempo-padrão e preço fixo, mesmo que o mecânico demore mais ou menos; no preço por hora, cobra-se o tempo gasto.
- **Na Quorum:** o Sam citou os dois modelos, com exemplo de US$ 200/hora.

## Flex Reports

- **Na Quorum:** relatórios customizados em Excel que antecederam o Power BI na história dos relatórios da Quorum (Sam). Ver [q-analytics/migracao-powerbi-superset.md](q-analytics/migracao-powerbi-superset.md).

## Git e GitHub

- **Geral:** Git guarda o histórico de versões de arquivos; GitHub hospeda repositórios Git.
- **Na Quorum:** os stored procedures são versionados no Git e podem ser recriados a partir do repositório; o ETL usa GitHub para snapshots e backup/restauração de jobs e pipelines; as mudanças em jobs e clientes são gerenciadas via Git; existe uma organização da Quorum no GitHub (Todd). Segundo o Todd (via Eduardo, 2026-10-29), as consultas e procedimentos dos jobs de produção do DealerMine também estão no GitHub.

## Handlebars

- **Geral:** linguagem de templates que monta texto/HTML a partir de dados. No Superset, o chart Handlebars recebe o resultado de uma consulta (por exemplo, no modo "Raw", linhas sem agregação) e o desenha com um template HTML/CSS escrito pelo autor do chart; por isso o Superset aplica uma sanitização que pode remover parte do HTML/CSS.
- **Na Quorum:** os gráficos de medidor (gauge) foram construídos em código Handlebars no Superset (Todd). O Eduardo planeja usar o Handlebars em 2026-10-30 para trocar os Big Numbers do dashboard de teste por cartões de KPI com comparação com o mês anterior (ver "MTD / comparação de mesmo período" e [q-analytics/superset-e-seguranca.md](q-analytics/superset-e-seguranca.md)). O efeito da sanitização na versão da Quorum é dúvida.

## Help desk

- **Geral:** equipe de suporte interno que recebe pedidos de acesso e de ajuda.
- **Na Quorum:** o Sam indicou que o acesso ao ambiente de teste do DMS pode ser pedido ao Todd ou ao help desk. Quem atende o help desk e como é acionado são dúvidas (ver [empresa/modo-de-trabalho.md](empresa/modo-de-trabalho.md)).

## information_schema

- **Geral:** conjunto de visões que todo banco SQL padrão mantém com o "catálogo" do próprio banco: quais schemas, tabelas e colunas existem. Serve para consultar a estrutura com `SELECT`, sem alterar nada (por exemplo, `information_schema.schemata` lista os schemas e `information_schema.tables` lista as tabelas). No SQL Server e no Postgres o nome é o mesmo.
- **Na Quorum:** usado pelo Eduardo em 2026-10-28 para listar os schemas do `datamigration` antes de criar o dele; no SQL Server de origem serve para listar tabelas e colunas pelo Query Executor (2026-10-29: lista de cerca de 1790 tabelas do banco de teste do DealerMine). Ver [q-analytics/pratica-ingestao-dev.md](q-analytics/pratica-ingestao-dev.md).

## Jira e story points

- **Geral:** Jira é uma ferramenta de gestão de trabalho; story points são uma medida relativa do esforço de uma story (tarefa).
- **Na Quorum:** o Sam disse que, quando o escopo muda, a preferência é documentar e atualizar os story points no Jira. Ver [empresa/cultura-e-expectativas.md](empresa/cultura-e-expectativas.md).

## Last run variable (`last_run_var`)

- **Geral:** valor que o job guarda ao terminar (por exemplo, a data mais recente que leu) e usa na rodada seguinte para ler só o que é novo. É a forma simples de carga incremental.
- **Na Quorum:** campo do job na ferramenta de ingestão, com valor inicial e três formas de atualização: por timestamp, por expressão sobre o destino ou por script na origem (README). Não entra nos snapshots. Ver [q-analytics/pipeline-ingestao.md](q-analytics/pipeline-ingestao.md).

## Lazy loading

- **Geral:** carregar só o que o usuário vai ver agora, e o resto sob demanda.
- **Na Quorum:** carregamento preguiçoso por página de dashboard no Superset, com cache em Redis (Todd).

## Livro-razão, lançamentos e conciliação (ledger, journal entries, reconciliation)

- **Geral:** o livro-razão registra todos os movimentos financeiros; um lançamento é um registro nele; a conciliação confere se os registros batem com a realidade (por exemplo, o banco).
- **Na Quorum:** feitos dentro do DMS, que reúne as categorias vendas, custo, despesas, outras receitas e ativos/passivos (Sam).

## MCP (Model Context Protocol)

- **Geral:** padrão que permite a um assistente de IA, como o Claude, usar ferramentas externas (consultar um banco, criar gráficos).
- **Na Quorum:** o Superset tem suporte a MCP, com autenticação pelo Descope (deck); o Todd mostrou a integração com o Claude Desktop, para criar gráficos, auditar SQL e validar dados. Fluxo recomendado: construir em Dev, conferir, exportar para produção.

## Média ponderada

- **Geral:** média em que cada valor conta com um peso. Para agrupar médias de vários dias, não se faz a média das médias: soma-se `valor × peso` e divide-se pela soma dos pesos. O peso precisa ter **o mesmo universo do valor**: uma média calculada só sobre as linhas que têm certa data deve ser ponderada pela contagem dessas linhas, não pela contagem de outro grupo. Contagens distintas (`COUNT(DISTINCT ...)`) também não podem ser somadas entre dias.
- **Na Quorum:** erro e correção do primeiro dashboard do Eduardo (2026-10-29): a média de dias até o agendamento dava menos de 1 dia porque o peso era a contagem de contatos com agendamento (`with_appointment`) e a média só considerava as linhas com data de agendamento válida. A correção foi guardar no job a coluna `appt_date_count` e usar a métrica `SUM(avg_days_to_appt * appt_date_count) / NULLIF(SUM(appt_date_count), 0)`; os valores passaram a ficar entre 1 e 21 dias. Ver [q-analytics/pratica-ingestao-dev.md](q-analytics/pratica-ingestao-dev.md) e [q-analytics/superset-e-seguranca.md](q-analytics/superset-e-seguranca.md).

## MSSQL (Microsoft SQL Server)

- **Geral:** abreviação de Microsoft SQL Server, banco relacional da Microsoft (o dialeto é o T-SQL).
- **Na Quorum:** o banco de teste do DealerMine no ambiente de migração de dados é MSSQL, com dados reais de clientes exportados (Todd, ligação de 2026-10-27). O SQL Server também aparece como banco de destino já disponível na ingestão.

## MTD / comparação de mesmo período

- **Geral:** MTD (*month to date*) é o acumulado do mês até hoje. Na comparação de **mesmo período**, o mês atual parcial é comparado com o mês anterior **até o mesmo dia**, para não comparar meio mês com um mês inteiro. A variação em contagens e médias se expressa em **percentual**; para um indicador que já é percentual (por exemplo, a participação de um tipo de contato), usam-se **pontos percentuais** (diferença entre os dois percentuais).
- **Na Quorum:** regra adotada pelo Eduardo para o dashboard v2 de teste (aceita em 2026-10-29, a construir em 2026-10-30): o corte é o último dia com dados no mês atual, e o mês anterior é cortado no mesmo dia. Ver [q-analytics/superset-e-seguranca.md](q-analytics/superset-e-seguranca.md).

## Native filter (Superset)

- **Geral:** filtro de dashboard do Superset, criado na tela do dashboard e ligado a uma coluna de um ou mais datasets. Os tipos usados na prática foram **Value** (lista de valores de uma coluna) e **Time range** (intervalo de datas). Ao ligar o filtro de tempo, o Superset pode avisar sobre colunas temporais, mas o filtro funcionou.
- **Na Quorum:** o dashboard v1 do Eduardo (2026-10-29) tem dois native filters: tipo de contato e período. Na v2, o filtro de período padrão passa a ser o mês atual. Ver [q-analytics/superset-e-seguranca.md](q-analytics/superset-e-seguranca.md).

## OAuth

- **Geral:** protocolo padrão para que um sistema confirme quem é o usuário (ou dê acesso a um app) sem compartilhar a senha; é a base de muitos logins "entrar com...".
- **Na Quorum:** o Descope usa OAuth para o SSO do Q Analytics (deck, slide 13).

## Override da tabela de destino

- **Geral:** configuração de um job que troca o nome (e, se o destino aceitar, o schema) com que a tabela é gravada, em vez de usar o mesmo nome da origem.
- **Na Quorum:** visto num job existente da dev: a tabela de origem `dbo.CCCMaster` é gravada no destino Postgres com outro schema (prefixo do banco de origem seguido de `_dbo`) e o nome em minúsculas (Eduardo, 2026-10-28). O campo Table do job também aceita schema.tabela (README). **Confirmado na prática (2026-10-29):** o job do Eduardo grava no destination server dele com override no formato `schema.tabela` (schema `test_edu`) e rodou sem erros.

## Over-the-counter (venda de balcão)

- **Geral:** venda direta ao cliente no balcão.
- **Na Quorum:** venda de peças ao cliente final, em contraste com a venda interna de peças ao serviço, que tem volume muito maior (Sam).

## pg_cron

- **Geral:** extensão do Postgres que agenda tarefas dentro do próprio banco, como um cron.
- **Na Quorum:** jobs pg_cron rodam toda noite e agregam os dados brutos em tabelas de resumo (Todd). Ver [q-analytics/postgres-e-resumos.md](q-analytics/postgres-e-resumos.md).

## pgAdmin 4

- **Geral:** aplicativo gráfico gratuito para conectar a um banco Postgres, navegar pelas tabelas e rodar consultas. Existe como programa instalado no computador e também em versão **web**, hospedada num servidor, com login e MFA próprios.
- **Na Quorum:** o Todd disse que o servidor de teste pode ser adicionado no pgAdmin 4 (ligação de 2026-10-27). Em 2026-10-28 ele enviou o acesso ao **pgAdmin 4 web** da Quorum (login próprio, com MFA no primeiro acesso) e o Eduardo cadastrou a instância de teste como `test db`. Ver [q-analytics/ambiente-de-teste-postgres.md](q-analytics/ambiente-de-teste-postgres.md).

## Pipelines incrementais e históricos

- **Geral:** um pipeline **incremental** carrega só o que mudou desde a última execução; um **histórico** carrega o passado de uma vez (carga inicial ou recarga).
- **Na Quorum:** foram citados na ligação de 2026-10-27, com a informação de que os existentes não têm backup e são trabalho em andamento. A definição geral acima é suposição do significado do termo `(inferido)`; ver [q-analytics/agente-de-ingestao.md](q-analytics/agente-de-ingestao.md). Na dev, o cliente DM Colo tem os pipelines DealerMine Historical e DealerMine Incremental, que o Eduardo descreve como testes do Todd com o SQL Server (Eduardo, 2026-10-28).

## Plugins (mensagens automáticas)

- **Na Quorum:** o Sam chamou de "plugins" as mensagens automáticas enviadas à equipe (por exemplo, pedido de peças) e a clientes (por exemplo, lembrete de garantia estendida); o Communicator as suporta. O significado técnico exato do termo está em aberto (ver [negocio/dominio-concessionaria.md](negocio/dominio-concessionaria.md)).

## Postgres: primário e réplica de leitura

- **Geral:** o primário aceita leitura e escrita; a réplica é uma cópia sincronizada, só leitura, que alivia o primário.
- **Na Quorum:** todos os relatórios consultam a réplica (Todd).

## Pré-agregação e tabelas de resumo (summary tables)

- **Geral:** calcular antes totais e somas e guardá-los numa tabela menor, para a consulta ser rápida.
- **Na Quorum:** existem porque algumas tabelas brutas chegam a ~425 GB; o exemplo foi 2 minutos contra 2 segundos de carregamento (Todd). Os jobs de produção do DealerMine também agregam na origem, com `GROUP BY`, e carregam só o resultado (ver [q-analytics/pipeline-ingestao.md](q-analytics/pipeline-ingestao.md)); o primeiro job do Eduardo segue o mesmo padrão.

## Q Analytics

- **Na Quorum:** ferramenta principal de relatórios da Quorum, sobre Superset + Postgres; voltada ao cliente (concessionária). No Power BI era um dos dois tenants, ao lado do Q Insights (Sam). Ver [q-analytics/visao-geral.md](q-analytics/visao-geral.md).

## Q Insights

- **Na Quorum:** tenant interno do Power BI, ao lado do Q Analytics (voltado ao cliente); permitia ver dados de todos os clientes. Com a migração, o Superset reúne os dois num servidor, um tenant e um login (Sam). Ver [q-analytics/visao-geral.md](q-analytics/visao-geral.md).

## QIS Connector

- **Na Quorum:** o Sam disse que o servidor de teste do DMS (ZZZ509) é acessado "via QIS Connector / Colo". O que é o conector e a relação com o colocation não foram explicados (a confirmar com o Sam ou o Todd). Ver [produtos/quorum-dms.md](produtos/quorum-dms.md).

## Query Executor

- **Geral:** tela de uma ferramenta que permite rodar uma consulta direto num banco, sem sair da ferramenta.
- **Na Quorum:** recurso da ferramenta de ingestão para consultar ao vivo o banco de origem de um cliente, para auditoria (Todd; README). Na prática do Eduardo, serve para explorar o SQL Server de teste do DealerMine só com `SELECT` limitado por `TOP`. Ver [q-analytics/pratica-ingestao-dev.md](q-analytics/pratica-ingestao-dev.md).

## Repair order (RO) e work order (WO)

- **Geral:** documento que registra um serviço de reparo feito num veículo.
- **Na Quorum:** os termos são equivalentes: o DMS diz "work order" e o CRM diz "repair order" (Sam).

## Ribbon (faixa de botões)

- **Geral:** a barra de botões agrupados por módulo no topo de um programa.
- **Na Quorum:** no Quorum DMS, o ribbon mostra os módulos serviço, peças, veículos, contabilidade e CRM (Sam, na demonstração). Ver [produtos/quorum-dms.md](produtos/quorum-dms.md).

## Row-level security (RLS)

- **Geral:** regra que filtra as linhas que cada usuário pode ver na mesma tabela.
- **Na Quorum:** limita cada concessionária aos próprios dados; o filtro vem como metadado da conta do usuário (Todd) e é tratado por um security manager customizado do Superset (deck). O Sam acrescenta que dentro da concessionária há acesso por papel e que a diretoria vê tudo. Ver [q-analytics/superset-e-seguranca.md](q-analytics/superset-e-seguranca.md).

## Sandbox (ambiente de teste)

- **Geral:** ambiente isolado onde se pode experimentar sem afetar o que está em produção.
- **Na Quorum:** o Todd indicou como sandbox do Eduardo o ambiente de migração de dados, com o cliente "DM Colo" ligado ao banco de teste MSSQL do DealerMine; a recomendação é criar pipeline e jobs pessoais, sem alterar os existentes (ligação de 2026-10-27). No Postgres de teste, o Todd sugeriu também um schema próprio no `datamigration` (2026-10-28), e o Eduardo criou o schema `test_edu` e o pipeline "DealerMine Test Edu". Em 2026-10-29, o primeiro job desse pipeline carregou a tabela `test_edu.dt_contact_daily` e o Eduardo montou um dashboard de teste no Superset Dev em cima dela. Para o DMS, existe um servidor de teste seguro para explorar (Sam). Ver [q-analytics/agente-de-ingestao.md](q-analytics/agente-de-ingestao.md), [q-analytics/ambiente-de-teste-postgres.md](q-analytics/ambiente-de-teste-postgres.md) e [q-analytics/pratica-ingestao-dev.md](q-analytics/pratica-ingestao-dev.md).

## Schedule "Manual only"

- **Na Quorum:** opção de agendamento de um job na ferramenta de ingestão: o job só roda quando alguém clica em Run (▶). É a escolha para os jobs de teste do Eduardo. Atenção: um job **sem nenhum agendamento** roda sempre que o cliente conecta (README). Ver [q-analytics/pipeline-ingestao.md](q-analytics/pipeline-ingestao.md).

## Schema (esquema)

- **Geral:** "pasta" dentro do banco que agrupa tabelas.
- **Na Quorum:** os dados caem em esquemas por produto, mais um esquema de staging (Todd). No Postgres de teste, o Eduardo tem o schema próprio `test_edu` (ver "Database x schema").

## Scope creep (expansão de escopo)

- **Geral:** crescimento gradual do que precisa ser entregue, sem ajuste de prazo ou esforço.
- **Na Quorum:** tratado caso a caso, sem processo formal rígido: documentar a mudança, atualizar os story points no Jira e resolver (Sam). Ver [empresa/cultura-e-expectativas.md](empresa/cultura-e-expectativas.md).

## Security manager (Superset)

- **Geral:** componente do Superset que decide quem pode ver o quê (dashboards, datasets, linhas). Pode ser substituído por uma versão customizada em código.
- **Na Quorum:** há um security manager customizado que usa os metadados do login para controlar o acesso a dashboards e a RLS (deck, slide 13).

## Serviço do Windows (Windows Service)

- **Geral:** programa que roda em segundo plano no Windows, inicia com a máquina e não precisa de ninguém logado.
- **Na Quorum:** o agente de ingestão é instalado como serviço (nome citado `QAnalyticsDIClient`), por padrão como `LocalSystem`, com recomendação de conta de serviço dedicada em produção; os logs vão para o Event Log do Windows (Word do Todd).

## Shadowing

- **Geral:** acompanhar uma pessoa experiente enquanto ela trabalha, para aprender observando.
- **Na Quorum:** o Rick combinou 3 a 4 horas de shadowing do Eduardo com o Sam nos relatórios existentes; o Sam disse que o Eduardo pode acompanhá-lo enquanto ele trabalha (ver [onboarding/primeiras-semanas.md](onboarding/primeiras-semanas.md)).

## SSO (single sign-on)

- **Geral:** entrar uma vez e ser reconhecido por vários sistemas.
- **Na Quorum:** feito pelo Descope no Q Analytics (Todd, deck).

## Staging e staged loads

- **Geral:** área intermediária onde os dados chegam e são validados antes de irem ao destino final.
- **Na Quorum:** o ETL faz cargas em stage: com chave de conflito, os dados vão para uma tabela intermediária e são mesclados no destino (README); há também um esquema de staging no Postgres (Todd).

## Stored procedure

- **Geral:** bloco de código SQL guardado e executado dentro do banco.
- **Na Quorum:** todos são versionados no Git e podem ser apagados e recriados a partir do repositório (Todd).

## Synapse, SQL Server e Power BI Fabric

- **Geral:** Azure Synapse é o serviço de data warehouse/analytics da Microsoft; SQL Server é o banco relacional da Microsoft; Power BI é a ferramenta de BI da Microsoft e o Fabric é a plataforma que a integra.
- **Na Quorum:** era a stack anterior (Synapse + data warehouse SQL Server + Power BI Fabric), cara de operar e manter (Todd). Ver [q-analytics/migracao-powerbi-superset.md](q-analytics/migracao-powerbi-superset.md). O deck diz que o SQL Server está sendo adicionado como banco de **destino** da nova ingestão, e o Todd disse na ligação de 2026-10-27 que ele já está disponível.

## System Control table

- **Na Quorum:** tabela do DMS que o Sam citou ao contar um erro de alteração em massa em servidor ao vivo, corrigido com backup. O papel exato da tabela não foi explicado.

## T-SQL

- **Geral:** dialeto de SQL do SQL Server.
- **Na Quorum:** aparece entre os bancos usados pelos produtos, ao lado de Postgres e MySQL (Rick).

## Tenant (multi-tenant)

- **Geral:** "tenant" é um cliente isolado dentro de um sistema compartilhado. Multi-tenant: vários clientes usam a mesma plataforma, cada um vendo só os seus dados.
- **Na Quorum:** o deck usa "tenant / client context" como serviço compartilhado: resolve a qual cliente (concessionária) o usuário pertence, e daí vem o escopo dos dados. No Power BI havia dois tenants (Q Analytics e Q Insights), hoje unificados no Superset (Sam).

## Vehicle inventory x parts inventory (estoque de veículos x de peças)

- **Na Quorum:** dois estoques distintos: o de veículos é o que a equipe de vendas vende; o de peças é usado por serviço e peças (Sam).

## View (visão)

- **Geral:** consulta SQL guardada no banco com um nome, que se usa como se fosse uma tabela. Não guarda dados próprios: roda a consulta a cada uso. Serve para esconder uma lógica complicada (por exemplo, funções de janela como `LAG`, que busca o valor da linha anterior) atrás de um nome simples, que o Superset lê como um dataset.
- **Na Quorum:** o Eduardo planeja criar, em 2026-10-30, a view `test_edu.v_contact_kpi_monthly` (uma linha por mês, com o valor do mês atual e do anterior para cada KPI e a variação já calculada) para alimentar o dashboard v2. Só criar objetos novos e pessoais, nunca alterar os existentes. Ver [q-analytics/superset-e-seguranca.md](q-analytics/superset-e-seguranca.md).

## Warehouse (data warehouse)

- **Geral:** banco central organizado para análise e relatórios, alimentado por vários sistemas.
- **Na Quorum:** o Postgres desempenha esse papel; a tabela de segurança das concessionárias é sincronizada para o warehouse (Todd).

## Watermark

- **Geral:** marca do ponto até onde os dados já foram processados, para a próxima rodada começar dali.
- **Na Quorum:** uma tabela de watermark do ETL guarda a última execução; os dados de serviço têm um watermark próprio para recuperar de atualizações que falharam (Todd). Ver também "Last run variable".

## WebSockets

- **Geral:** conexão que fica aberta nos dois sentidos entre cliente e servidor, em vez de pedidos isolados.
- **Na Quorum:** os clientes do ETL ligam para o servidor central por WebSockets, então não é preciso liberar o firewall da concessionária (Todd). O agente usa `wss` (WebSocket com TLS) e envia 500 linhas por mensagem por padrão (Word do Todd).

## Windows Credential Manager

- **Geral:** cofre de credenciais do próprio Windows, onde programas guardam senhas e tokens de forma protegida.
- **Na Quorum:** o agente guarda ali seu token permanente após o enrollment, em vez de deixar em texto no disco (Word do Todd).

## Zoho CRM e Zoho Desk

- **Geral:** produtos da Zoho; CRM gerencia relacionamento com clientes e Desk é um sistema de suporte/atendimento.
- **Na Quorum:** citados pelo Todd como exemplos de fontes do ETL; na ligação de 2026-10-27 ele esclareceu que o Zoho é **interno** (CRM + Desk), não uma fonte de dados voltada a clientes, e que vários jobs rodam contra um único cliente Zoho configurado.
