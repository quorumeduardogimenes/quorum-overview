---
titulo: Postgres de teste ("test db"): conexão pelo pgAdmin web, databases e orientação do Todd
resumo: Como o Eduardo acessa o Postgres de teste pelo pgAdmin 4 web, os três databases (babelfish_db, datamigration, postgres), o que o Todd disse sobre cada um, o estado do datamigration, quem grava onde e o schema próprio do Eduardo, com a primeira tabela.
atualizado_em: 2026-10-29
fontes: [Eduardo, 2026-10-28 e 2026-10-29 (conexão criada com ajuda de outro agente deste projeto, databases vistos, consultas de leitura no datamigration, schema e primeira tabela criados, observações na ferramenta de ingestão dev, orientação do Todd repassada), Todd (orientação sobre os databases, repassada pelo Eduardo), ligação com Todd, 2026-10-27 (../reunioes/2026-10-27-todd-ligacao-ingestao.md), acessos ao pgAdmin web enviados pelo Todd, 2026-10-27 e 2026-10-28]
status: rascunho
---

# Postgres de teste ("test db"): conexão, databases e orientação do Todd

Este arquivo detalha o **destino de teste** citado em [agente-de-ingestao.md](agente-de-ingestao.md): o Postgres onde o Eduardo pratica. Para o Postgres de produção (primário, réplica, resumos), ver [postgres-e-resumos.md](postgres-e-resumos.md). O diário do que o Eduardo foi fazendo está em [pratica-ingestao-dev.md](pratica-ingestao-dev.md).

## Em palavras simples

Pense numa oficina compartilhada com três salas. Uma delas (`datamigration`) é a sala do ambiente de teste da migração de dados, onde o Eduardo pode trabalhar. As outras duas o Todd usa para os próprios experimentos de migrar dados do SQL Server para o Postgres. Para não esbarrar no trabalho do Todd, o Eduardo montou a própria bancada dentro da sala dele: um **schema** (pasta de tabelas) chamado `test_edu` e, na ferramenta de ingestão, um **pipeline** só seu.

## Como o Eduardo acessa

- **Ferramenta:** o **pgAdmin 4 web**, hospedado pela Quorum. Ele tem **login próprio** (usuário e senha do pgAdmin) e exige **configurar MFA no primeiro login**. Esse login não é o usuário do Postgres.
- **Conexão registrada:** o Eduardo cadastrou a instância de teste do Postgres no pgAdmin web com o nome **`test db`**, com ajuda de outro agente deste projeto (2026-10-28).
- **Endereço do pgAdmin:** a primeira URL enviada pelo Todd estava errada (erro de digitação dele); ele mandou a correta em 2026-10-28. O endereço, o host e as senhas **não ficam nesta documentação** (ver guia, regra 9): estão na mensagem do Todd.
- **Como as conexões chegaram:** como o Todd não passou host nem porta, a leitura do Eduardo é de que a conexão se faz a partir do próprio pgAdmin web, onde os servidores já aparecem cadastrados `(inferido)`.
- **Alcance da conta:** o nome do usuário de conexão lembra o do database `babelfish_db` `(inferido)`. Pode ser uma conta compartilhada com os testes do Todd e ter alcance além do `datamigration` (não foi testado). O cuidado de não mexer nos outros databases vale de qualquer forma.

## Os três databases

Ao abrir `test db`, aparecem três databases:

| Database | O que o Todd disse | Para o Eduardo |
|---|---|---|
| `datamigration` | "Está no ambiente de teste" | É onde o Eduardo trabalha |
| `babelfish_db` | Um dos "outros databases", que o Todd usa para testar a migração de MS SQL para Postgres `(inferido: por eliminação, a partir da fala dele)`. Na ferramenta de ingestão dev, o destination server Postgres do cliente DM Colo aponta para ele (observado pelo Eduardo) | Não mexer; é o espaço de testes do Todd |
| `postgres` | Idem acima `(inferido)` | Não mexer. Também é o database padrão que todo Postgres cria (ver glossário) |

O Todd não explicou o que é o `babelfish_db`; o nome lembra o recurso Babelfish do Postgres para falar com bancos SQL Server (ver [glossario.md](../glossario.md)), e a forma do schema usado no destino Postgres do DM Colo (prefixo do banco de origem seguido de `_dbo`) é compatível com o jeito como o Babelfish representa os schemas do SQL Server `(inferido, conhecimento geral, não confirmado na Quorum)`.

## Orientação do Todd (original em inglês e tradução)

> "The datamigration database is in the test environment. The other databases there I am using to test migrating from MS sql to postgres. I suggest creating your own schema and job pipeline to separate my work from yours."

Em português: o database `datamigration` está no ambiente de teste. Os outros databases ali o Todd está usando para testar a migração de MS SQL para Postgres. Ele sugere que o Eduardo crie o **próprio schema e o próprio pipeline de jobs**, para separar o trabalho de um do do outro.

Outras mensagens do Todd no mesmo dia, segundo o Eduardo: ele tornou o Eduardo admin do Superset Dev e passou as credenciais e o acesso de admin para que o Eduardo crie na ferramenta de ingestão o destination server do `datamigration`. Depois o Eduardo confirmou que o Todd **já criou, no Superset Dev, a conexão com o `datamigration`**.

## Estado do `datamigration`

Em 2026-10-28, consultas só de leitura, rodadas pelo Eduardo conectado ao `datamigration` (lista em [pratica-ingestao-dev.md](pratica-ingestao-dev.md)):

- O usuário e o database estavam corretos.
- **Nenhum schema além dos quatro padrão do Postgres:** `information_schema`, `pg_catalog`, `pg_toast` e `public`. Ou seja, o database estava vazio de schemas de trabalho.
- O usuário **tem permissão de criar schemas** no database.
- O Eduardo criou o schema **`test_edu`**. Nada é criado em `public`, que é o schema padrão de todos.

Em 2026-10-29, o Eduardo criou em `test_edu` a tabela **`dt_contact_daily`** (contatos por dia, tipo e sentido, vinda do `DTContactHistory` do DealerMine de teste), com chave primária composta de 5 colunas. É o destino do primeiro job dele (detalhes em [pratica-ingestao-dev.md](pratica-ingestao-dev.md)).

## Quem grava onde (observado na ferramenta de ingestão dev)

- Os destination servers que já existiam apontam para o `babelfish_db` (um Postgres) e para um banco SQL Server; detalhes em [pipeline-ingestao.md](pipeline-ingestao.md).
- Segundo o Eduardo, **nenhum pipeline ou job ativo gravava no `datamigration`** antes da prática dele. Portanto a hipótese de que o cliente DM Colo gravava no `datamigration` **não se confirmou**: o DM Colo gravava no `babelfish_db` e no SQL Server (testes do Todd).
- O `datamigration` passou a ser o destino da prática do Eduardo, por meio de um destination server novo, criado por ele (DMColoPGTestEdu). Em 2026-10-29, o primeiro job dele passou a gravar em `test_edu.dt_contact_daily`.

## O que isso significa na prática

1. **Trabalhar só no `datamigration`**, no schema `test_edu`. Os outros dois databases são do Todd.
2. **Pipeline e jobs próprios** na ferramenta de ingestão, sem alterar os existentes, reforçando a recomendação da ligação de 2026-10-27 (ver [agente-de-ingestao.md](agente-de-ingestao.md)).
3. **Tabelas de destino criadas antes**, no pgAdmin: o formulário do job avisa que as tabelas de destino precisam já existir.
4. **Manter os cuidados de sempre:** o X da ferramenta pode apagar jobs, e credenciais nunca entram na documentação.
5. **Dados reais:** o banco de origem de teste tem dados reais exportados; não copiar linhas para conversas nem documentos.

## Lacunas e dúvidas

- Resolvidas em 2026-10-28: o Eduardo consegue criar schema no `datamigration` (sim); há convenção de nomes? (não foi informada; o Eduardo adotou `test_edu`).
- Resolvida em 2026-10-29: a tabela precisa existir antes do job (o formulário diz que sim). O campo de override aceita o nome com schema (`schema.tabela`) segundo o README; o job do Eduardo usa esse formato.
- O `datamigration` é o mesmo que o ambiente de migração de dados e a dev da ferramenta de ingestão? O Todd só disse que o `datamigration` está no ambiente de teste.
- A pergunta do Eduardo ao Todd sobre "test db" e "data migration db" serem a mesma coisa recebeu só a resposta acima; a relação com o cliente DM Colo hoje é: não grava no `datamigration` (a não ser pelo job do Eduardo).
- O que é o `babelfish_db` e qual é o objetivo da migração de MS SQL para Postgres que o Todd está testando? Tem relação com o SQL Server como destino da ingestão ou com a migração de dados do DealerMine?
- Existe também uma conexão somente leitura para o Postgres de produção (usuário somente leitura que o Todd enviou) e ela já foi cadastrada no pgAdmin web?
