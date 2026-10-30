---
titulo: Agente de ingestão (cliente), implantação, ambientes de teste e jobs
resumo: Como funciona a ingestão de ponta a ponta na prática: o programa que fica na concessionária (agente/cliente), como ele é implantado e se registra no servidor, ambientes de teste, como jobs e clientes de API são configurados, cuidados ao mexer na ferramenta e o roteiro de prática do Eduardo.
atualizado_em: 2026-10-29
fontes: [ligação com Todd, 2026-10-27 (../reunioes/2026-10-27-todd-ligacao-ingestao.md), mensagem do Todd no Teams, 2026-10-27, antes da ligação, colada pelo Eduardo (versões dev), documento Word "QAnalytics Data Ingestion Agent 2.docx" (guia de implantação do agente), enviado pelo Eduardo em 2026-10-27 e resumido aqui, Eduardo (aviso do Todd sobre o X; esclarecimentos de 2026-10-27; conexão ao Postgres de teste, orientação do Todd, exploração da versão dev, README e prints da ferramenta, 2026-10-28; primeiro job de ponta a ponta na dev, 2026-10-29), reunião com Todd, 2026-10-26 (../reunioes/2026-10-26-todd-tour-tecnico.md)]
status: rascunho
---

# Agente de ingestão (cliente), implantação, ambientes de teste e jobs

Este arquivo detalha uma parte da [ferramenta de ingestão](pipeline-ingestao.md): o programa instalado perto do banco de origem, como ele é posto no ar, onde se pode testar e o que o Eduardo deve (e não deve) fazer na ferramenta. Os campos de um job, o agendamento e os snapshots estão em [pipeline-ingestao.md](pipeline-ingestao.md). Ideias de melhoria ficam em [ideias-de-melhoria.md](ideias-de-melhoria.md). O Postgres de teste (databases, conexão e orientação do Todd) está em [ambiente-de-teste-postgres.md](ambiente-de-teste-postgres.md), e o diário da prática do Eduardo (objetos criados, consultas, próximos passos) em [pratica-ingestao-dev.md](pratica-ingestao-dev.md).

## Em palavras simples

Em cada concessionária fica instalado um programa, o **agente**. Na primeira vez ele se registra no servidor central com um **token provisório de uso único**, gerado no painel, e recebe um **token definitivo**, guardado no cofre de credenciais do Windows. Dali em diante ele liga sozinho para a central, fica na linha e espera ordens: "leia estas tabelas e mande 500 linhas por mensagem". A central também lhe entrega a senha do banco, então ninguém precisa deixá-la escrita num arquivo. Quem configura **o que** ele faz (os jobs) é a equipe de dados, direto no painel central; quem **instala** o agente é a equipe de infraestrutura.

## A ingestão de ponta a ponta (visão resumida)

Montada a partir da ligação com o Todd, do Word e das reuniões anteriores. Cada etapa tem a fonte indicada.

| Etapa | O que acontece | Quem | Fonte |
|---|---|---|---|
| 1. Acesso do cliente | A concessionária (cliente) fornece um usuário e senha do ambiente dela | Cliente | Todd (ligação) |
| 2. Implantar o agente | O agente é instalado como serviço do Windows e se registra no servidor com um token de uso único | Equipe de infraestrutura | Todd; Word |
| 3. Conectar ao banco | Quando o agente aparece na ferramenta, configura-se a conexão com o banco da concessionária | Equipe de dados | Todd |
| 4. Atribuir pipelines | Pipelines são atribuídos ao agente; cada pipeline tem jobs | Equipe de dados | Todd |
| 5. Executar | O servidor manda os jobs, por agendamento, por cliente, por pipeline, por job ou manualmente; o agente lê o banco local e envia as linhas em lotes de 500 por padrão | Servidor e agente | Deck; Word |
| 6. Transformar e gravar | Os dados são mapeados, padronizados e gravados no Postgres (upsert, cargas em stage), em esquemas por produto | Ferramenta | Deck; Todd (2026-10-26) |
| 7. Resumir | Rotinas noturnas do pg_cron agregam dados brutos em tabelas de resumo | Postgres | Todd (2026-10-26); ver [postgres-e-resumos.md](postgres-e-resumos.md) |
| 8. Consumir | O Superset consulta a réplica de leitura e monta dashboards, com RLS por concessionária | Superset | Todd; deck; ver [superset-e-seguranca.md](superset-e-seguranca.md) |

Depois de implantado o cliente, o trabalho do dia a dia é **nos jobs**, não no cliente.

**Percorrido na prática em 2026-10-29 (só em teste):** as etapas 5, 6 e 8 foram feitas de ponta a ponta pelo Eduardo na dev, com um job pessoal: origem SQL Server de teste do DealerMine (agregada no `SELECT`) > tabela `test_edu.dt_contact_daily` no Postgres de teste > dataset, charts e dashboard no Superset Dev. As etapas 1 a 4 já estavam prontas no ambiente (o cliente DM Colo já estava conectado). Ver [pratica-ingestao-dev.md](pratica-ingestao-dev.md) e [superset-e-seguranca.md](superset-e-seguranca.md).

## Cliente e agente: o mesmo programa?

As fontes usam as duas palavras:

- O Todd (ligação) diz que a equipe de infraestrutura "implanta o **agente**" e que, quando ele "aparece na ferramenta", os pipelines são atribuídos a ele; também fala em **clientes** em produção que "conectam de volta" ao servidor.
- O Word chama o programa de "client agent" e diz que o `client_id` identifica "esta instância do agente" e "deve casar com o **registro do cliente** no servidor".

Leitura que cabe nas duas fontes: **cada concessionária ou ambiente tem um registro de cliente no servidor; o programa instalado nela é o agente; os dois se ligam pelo `client_id`**. Isso conversa com a hierarquia clientes > pipelines > jobs de [pipeline-ingestao.md](pipeline-ingestao.md).

## Como um cliente é implantado (Todd, ligação)

Na seção "Client Deployment Process" das notas do Granola, o Todd explica que a equipe **não mexe diretamente nos servidores das concessionárias ou de produção**. O processo, em quatro passos:

1. O cliente (a concessionária) fornece um **usuário e senha do seu ambiente**.
2. A **equipe de infraestrutura implanta o agente**.
3. Quando o agente **aparece na ferramenta**, a equipe configura a **conexão com o banco de dados**.
4. Os **pipelines são atribuídos ao agente**.

O Granola não diz de quem é o usuário e senha do passo 1 (conta do banco ou acesso ao servidor). O Word indica que o agente precisa de uma conta de banco com permissões de leitura, o que sugere ser essa conta `(inferido)`.

## Guia técnico de implantação (documento Word do Todd)

O Word é um guia para implantar o agente num **servidor Windows como serviço do Windows**. Resumo do conteúdo (o arquivo não fica arquivado no projeto; as senhas de exemplo do Word foram propositalmente omitidas; pequenas variações de nome no Word são erros de digitação, segundo o Eduardo):

**Pré-requisitos**

- Um executável já compilado do agente.
- Direitos de administrador no servidor de destino.
- Acesso de rede ao endpoint WebSocket do servidor QAnalytics (formato `wss://.../worker`).
- Um **token de enrollment (bootstrap)** gerado no painel do servidor, para a primeira configuração.

**Pasta e configuração**

- Pasta dedicada, por exemplo `C:\Program Files\QAnalyticsDI\`, com o executável e um `config.json`. Quando roda como serviço, o executável procura o `config.json` na mesma pasta.
- Campos do `config.json`:

| Campo | Para que serve |
|---|---|
| `batch_size` | Quantas linhas vão em cada mensagem WebSocket. 500 é o padrão recomendado |
| `client_id` | Identificador único da instância do agente; deve casar com o registro do cliente no servidor |
| `ws_worker_url` | URL WebSocket do servidor (use `wss://`, com TLS) |
| `worker_enroll_bootstrap_token` | Token de uso único do painel, para o primeiro enrollment; é apagado automaticamente depois |

**Como o agente se registra (bootstrap enrollment)**

1. No painel do servidor, abre-se o diálogo **Deploy** do cliente e copia-se o token de bootstrap, que é colado no `config.json`.
2. No primeiro start, o agente faz um POST em `/api/worker-enroll` com esse token.
3. Recebe um **token permanente por cliente**.
4. Guarda o token no **Windows Credential Manager** (cofre de credenciais do Windows).
5. **Apaga** o token de bootstrap do `config.json` e reconecta com o token novo.

Reinícios seguintes pulam o enrollment e autenticam com a credencial guardada. Segundo o Word, nada fica em texto puro no disco.

**Serviço do Windows**

- O próprio executável tem os comandos `install`, `start`, `stop`, `restart` e `uninstall`.
- Por padrão roda como `LocalSystem`; em produção recomenda-se uma **conta de serviço dedicada**. Essa conta precisa de: direito "Log on as a service"; leitura **e modificação** na pasta de instalação (o agente reescreve o `config.json` após o enrollment); acesso ao Credential Manager; e acesso de rede ao banco de origem e ao servidor QAnalytics.
- Logs vão para o **Windows Event Log** (Application).

**Conta de banco no SQL Server**

O agente precisa de uma conta (login e usuário) no banco de origem com, no mínimo:

- `SELECT` no schema `dbo` (ler os dados);
- `VIEW CHANGE TRACKING` no schema `dbo` (acompanhar mudanças incrementais).

Se houver dados em outros schemas, repete-se a concessão para eles. No modo "worker" (o normal), **o servidor envia as credenciais do banco ao agente de forma segura**, sem guardá-las no `config.json`; no modo **standalone** (alternativo), usam-se variáveis de ambiente.

**Solução de problemas (do Word)**

| Sintoma | O que verificar |
|---|---|
| Serviço não inicia | `config.json` existe na pasta do executável e é JSON válido |
| Enrollment falha | Token de bootstrap expirou; gerar outro no painel |
| Conecta e cai logo | Rede até `ws_worker_url`: porta 443 (`wss`) ou 80 (`ws`) acessível a partir do servidor |
| Acesso negado | A conta de serviço tem leitura/modificação na pasta de instalação |
| Token não guardado | A conta de serviço acessa o Credential Manager |

**Segurança que o guia deixa clara:** o agente só precisa de acesso **de saída** à porta 443 (ou 80) do servidor, o que confirma por que não é preciso liberar entrada no firewall da concessionária (ver [pipeline-ingestao.md](pipeline-ingestao.md)).

## Ambientes de teste (Todd)

**Versões dev (mensagem do Todd no Teams, antes da ligação):** existe sim ambiente de desenvolvimento. O Todd mantém **versões dev do app rodando em um container na rede interna**, onde testa as mudanças. Ou seja, nem tudo é revisado e implementado direto em produção (o Eduardo havia perguntado isso por achar que era assim). Duas ressalvas dele:

- Para alguns jobs, como **cada cliente só se registra em um servidor**, é preciso **implantar clientes novos** para testar.
- Para **experimentar**, é **melhor usar a versão dev**, mas ela fica **atrás do firewall** (acesso só pela rede interna).

**Na ligação (Granola):**

- **Servidor de teste:** tem **um único cliente**, rodando **num container no mesmo servidor**. Provavelmente é a mesma versão dev descrita acima `(inferido)`.
- **Por que é difícil testar:** os clientes de produção só conectam de volta a **um servidor**; para testar, é preciso **recriar o ambiente inteiro**.
- **Isolamento:** recursos de segurança impedem que um cliente de outro ambiente conecte (exemplo: não dá para acessar de fora um cliente de um servidor do DealerMine ou de uma concessionária).
- **Ambiente de migração de dados (data migration environment):** tem um cliente chamado **"DM Colo"**, que conecta a um **banco de teste do DealerMine em MSSQL** com **dados reais de clientes exportados**. É o "sandbox" que o Todd indicou ao Eduardo.
- **Destino de teste:** o Todd ia enviar credenciais do **Postgres** e o acesso ao servidor de teste; dá para conectar pelo **pgAdmin 4**. **Atualização (2026-10-28):** o Eduardo já acessa a instância de teste pelo pgAdmin 4 web (conexão `test db`), onde aparecem os databases `babelfish_db`, `datamigration` e `postgres`. O `datamigration` é o do ambiente de teste; os outros dois o Todd usa para testar a migração de MS SQL para Postgres, e ele sugeriu que o Eduardo crie o próprio schema e pipeline de jobs. Detalhes em [ambiente-de-teste-postgres.md](ambiente-de-teste-postgres.md).
- **Destino SQL Server:** disponível; se o Eduardo precisar, o Todd fornece uma tabela.

**O que o Eduardo viu na dev (2026-10-28):** o cliente **DM Colo aparece na versão dev e está conectado**, o que é um indício de que a dev e o ambiente de migração de dados são o mesmo ambiente `(inferido, a confirmar com o Todd)`. Os jobs existentes do DM Colo gravam em destinos de teste do Todd (um SQL Server e o `babelfish_db`), e não no `datamigration`; o Eduardo criou o próprio destination server (DMColoPGTestEdu) para o `datamigration`. Detalhes em [pipeline-ingestao.md](pipeline-ingestao.md) e [pratica-ingestao-dev.md](pratica-ingestao-dev.md).

## Jobs e configuração (Todd, ligação; README)

- Existem **108 jobs** (incrementais e de produção), que levaram cerca de **6 horas para criar** (o total de 108 foi confirmado pelo Eduardo; um print da ferramenta mostrou 1776, a explicar).
- Mudanças são **gerenciadas via Git** em todos os jobs e clientes. Segundo o README, a ferramenta tem **snapshots de configuração** (pipelines, atribuições a clientes e jobs) com exportar, aplicar e restaurar via GitHub.
- O banco é **copiado em backup**, mas **jobs podem ser adicionados ou apagados**. O Todd avisou (via Eduardo) que **clicar no X pode deletar jobs**.
- Os pipelines **incrementais e históricos já existentes não têm backup** e ainda são **trabalho em andamento**.
- O que o Todd recomenda ao Eduardo: **criar um pipeline e jobs pessoais novos, em vez de modificar os existentes.**

Em outras palavras: há Git e a integração com GitHub para snapshots, mas os pipelines atuais não têm backup. Pode ser que o recurso exista e ainda não esteja ligado para eles `(inferido)`; vale perguntar ao Todd depois, sem pressa. Por enquanto, a regra é não apagar nem alterar os existentes.

## Clientes de API e ferramentas internas (Todd, ligação)

- A integração da **ferramenta de API** ainda é **trabalho em andamento**.
- Cada **cliente de API** é configurado conforme o que aquele sistema exige: **Zoho, Veeva e Powerlane diferem entre si** (o Eduardo decidiu que o Veeva não é prioridade agora).
- **Zoho é interno** (CRM + Desk) e **não é uma fonte de dados para clientes**; é usado pela própria Quorum. Vários jobs podem rodar contra **um único cliente Zoho** já configurado.
- O mesmo vale para o **Powerlane**: **um cliente, vários jobs**, cada um com chamadas de API diferentes.
- Em palavras simples: configura-se a "porta de entrada" da API uma vez (o cliente) e cada job é uma chamada diferente passando por ela.

## Cuidados ao mexer na ferramenta

- **Clicar no X pode apagar jobs** (aviso do Todd). Antes de clicar em qualquer X, confirmar o que será removido e em qual ambiente.
- **Não modificar** pipelines e jobs existentes, nem na dev e mesmo sendo admin; criar os seus (ver roteiro abaixo).
- Evitar também **Run All**, **aplicar snapshot**, **Runtime Capacity**, apagar destination servers e a área de contas.
- **Client ID em branco** num job faz ele rodar em todos os clientes do pipeline; sempre preencher.
- Prática apenas no **ambiente de teste** (a versão dev, que o Todd diz ser a melhor para experimentar, ou o ambiente de migração de dados com o banco de teste), não em produção.
- No Postgres de teste, **trabalhar só no `datamigration`**, dentro de um **schema próprio**; os outros databases são do Todd (ver [ambiente-de-teste-postgres.md](ambiente-de-teste-postgres.md)).
- **Nunca escrever credenciais** (senhas, tokens, IPs de servidor) nesta documentação ou em notas; elas vão por canal seguro do Todd.
- O banco de origem de teste tem **dados reais de clientes**: não copiar nem registrar linhas, e evitar tabelas de dados pessoais (contatos, nomes, senhas).

## Roteiro de prática do Eduardo (combinado na ligação)

Situação em 2026-10-29; o detalhe passo a passo está em [pratica-ingestao-dev.md](pratica-ingestao-dev.md).

1. Explorar a ferramenta e o repositório primeiro; só depois compilar as perguntas restantes e enviar ao Todd pelo Microsoft Teams. **Em andamento:** a ferramenta dev foi explorada e o README lido; a lista de perguntas foi podada; em 2026-10-29 o Eduardo enviou ao Todd a primeira mensagem (documentação das tabelas do DealerMine), ainda sem resposta.
2. Receber do Todd as credenciais do Postgres de teste e conectar pelo pgAdmin 4. **Feito em 2026-10-28** (conexão `test db` no pgAdmin web).
3. Criar um **schema próprio** no database `datamigration`, um **destination server** para ele e um **pipeline pessoal** com **jobs** no ambiente de migração de dados, usando o banco de teste do DealerMine (MSSQL) para praticar consultas SQL. **Feito:** schema `test_edu`, destination server DMColoPGTestEdu e pipeline "DealerMine Test Edu" (2026-10-28); **primeiro job concluído em 2026-10-29** (contatos por dia, tipo e sentido, agregados na origem; tabela `test_edu.dt_contact_daily`; rodou sem erros e o upsert foi confirmado). **Próximos:** novos jobs, incluindo carga incremental com a Last run variable (ver [pratica-ingestao-dev.md](pratica-ingestao-dev.md)).
4. Usar o Word do Todd como referência do processo do agente.
5. Fechar o ciclo no Superset Dev, com dataset, charts e dashboard sobre a tabela carregada. **Versão 1 feita em 2026-10-29; versão 2 (cartões Handlebars com comparação com o mês anterior) planejada para 2026-10-30** (ver [superset-e-seguranca.md](superset-e-seguranca.md)).

## Lacunas e dúvidas

- **Fluxo na prática:** um job foi criado e rodado de ponta a ponta com sucesso (2026-10-29). Falta explorar o que ainda não foi usado: carga incremental com a Last run variable, agendamento por horário, vários destinos num job e jobs de API.
- **Acesso ao dev:** a versão dev fica atrás do firewall. Como o Eduardo (no Brasil) a acessa? **Parcial:** ele já acessa; o meio não foi registrado. Falta confirmar se o ambiente de migração de dados é a própria versão dev (o DM Colo aparecer na dev é um indício).
- **Do teste para produção:** os snapshots de configuração com GitHub parecem ser o caminho `(inferido)`; falta confirmar que é o processo oficial, já que os clientes só se registram em um servidor, e quem revisa.
- **Passo 1 da implantação:** o "usuário e senha do ambiente" é conta do banco ou acesso ao servidor da concessionária? Quem na concessionária fornece?
- **Cliente x agente:** confirmar que o "Client" do topo do deck (clientes > pipelines > jobs) é o registro do cliente no servidor, e como se associa um cliente a um job. O job do Eduardo rodou sem erros, mas o passo de atribuir o pipeline ao cliente DM Colo não foi registrado.
- **Falhas de job:** a ferramenta tem Runtime Logs, Data Transfer History e o botão Run para rodar de novo. Falta saber de alertas e de reprocessar uma janela de tempo.
- **Change tracking:** o SQL Server usa `VIEW CHANGE TRACKING`; o README só mostra a variável de última execução para as outras fontes (API, Postgres, MySQL). Confirmar.
- **API:** o que falta na "ferramenta de API" (trabalho em andamento) e quais chamadas estão hoje configuradas para o Powerlane?
- **Backup:** qual banco é copiado e por que os pipelines atuais não têm backup apesar da integração com GitHub (baixa prioridade).
- **Repositório:** o Eduardo viu o README da ferramenta, mas não pretende expor o repositório ao assistente; qual repositório guarda o agente e o servidor continua sem registro.
