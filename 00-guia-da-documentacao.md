---
titulo: Guia da documentação Quorum
resumo: Regras de organização, formato e manutenção desta documentação. Leia antes de criar ou alterar qualquer arquivo.
atualizado_em: 2026-10-29
fontes: [decisão do Eduardo na conversa de 2026-10-26, decisões do Eduardo de 2026-10-27 (glossário, "Em palavras simples", nunca gravar segredos, foco no essencial), estrutura ampliada em 2026-10-29 (negócio, clientes), glossário único por decisão do Eduardo em 2026-10-29]
status: confirmado
---

# Guia da documentação Quorum

## 1. Propósito

Esta documentação mapeia a Quorum como empresa: produtos, negócio, clientes, pessoas, tecnologia e, em especial, o **Q Analytics**. Ela existe para que qualquer agente, em qualquer conversa deste projeto, consiga se contextualizar sobre um assunto lendo poucos arquivos, e para que o Eduardo (que está em onboarding) tenha uma memória organizada do que aprende.

A documentação é **viva**: a estrutura atual é uma primeira versão e vai mudar. Mudar a estrutura é esperado e permitido, seguindo a seção 7.

## 2. Princípios

1. **Idioma:** tudo em português do Brasil. Termos técnicos, nomes de produtos e de pessoas ficam no original (Superset, Power BI, row-level security etc.). Notas brutas de reunião podem ficar no idioma original.
2. **Um fato, um lugar.** Cada informação tem uma casa só. Os outros arquivos apontam para ela com link relativo em vez de copiar o texto.
3. **Sempre citar a fonte.** Todo arquivo lista suas fontes (reunião, data, pessoa que informou). Sem fonte, o fato deve estar marcado como inferência.
4. **Separar fato de inferência e de dúvida.** Fato dito por alguém: escrever normalmente. Inferência de quem documentou: prefixar com `(inferido)`. O que falta saber vai na seção "Lacunas e dúvidas", nunca é preenchido com suposição.
5. **Não criar arquivo vazio.** Só se cria um arquivo quando há conteúdo real para ele. Pastas "previstas" na seção 3 não existem até haver o que colocar.
6. **Pequeno e focado.** Um arquivo cobre um assunto. Se passar de cerca de 200 linhas ou misturar assuntos, divida (seção 7).
7. **Informação sobre pessoas:** registrar somente o que é relevante para o trabalho (papel, área, fuso, assuntos em que ajuda). Nada de dados pessoais sensíveis.
8. **Compreensível para quem está aprendendo.** O Eduardo está aprendendo conceitos técnicos em inglês. Todo arquivo técnico tem uma seção curta **"Em palavras simples"** (analogia, antes dos detalhes), e **todos os conceitos e termos novos, técnicos ou de negócio, ficam no [glossário](glossario.md)**, que é um documento único.
9. **Nunca gravar segredos.** Senhas, tokens, chaves e endereços IP de servidores não entram na documentação nem nas notas, mesmo que apareçam em materiais compartilhados (por exemplo, senhas de exemplo de um guia). Registrar apenas que existem e por qual canal são entregues.
10. **Foco no essencial.** O objetivo é entender o processo de ponta a ponta. Erros de digitação, nomes de variáveis e pequenas inconsistências em materiais só são registrados se mudarem o entendimento; caso contrário, ficam de fora.

## 3. Estrutura

Todos os caminhos começam com `quorum/`. Estrutura atual:

```
quorum/
├── 00-guia-da-documentacao.md   # este arquivo: regras
├── 00-indice.md                 # mapa de leitura + registro de mudanças
├── glossario.md                 # glossário único: conceitos técnicos e termos de negócio (WO/RO, F&I, flat rate etc.)
├── empresa/
│   ├── visao-geral.md           # o que o site público e o Sam dizem sobre a empresa (inclui história informal)
│   ├── modo-de-trabalho.md      # horários, fusos, cadência de reuniões, ferramentas
│   └── cultura-e-expectativas.md # cultura de trabalho na visão do Sam (escopo, estimativas, honestidade)
├── negocio/
│   └── dominio-concessionaria.md # departamentos, estoques, receita, jornada do cliente, termos
├── clientes/
│   └── concessionarias.md       # quantidade, um servidor por cliente, mesmo esquema, grupos
├── pessoas/
│   └── diretorio.md             # quem é quem
├── produtos/
│   ├── marcas.md                # marcas, produtos e status no Q Analytics
│   └── quorum-dms.md            # o DMS: contabilidade como centro, módulos, banco Dealer, teste
├── q-analytics/
│   ├── visao-geral.md           # o que é, para quem, como é acessado, uso interno
│   ├── arquitetura.md           # fluxo de dados de ponta a ponta, hospedagem, ambientes, deck do Todd
│   ├── pipeline-ingestao.md     # ETL próprio (clientes, pipelines, jobs, destination servers)
│   ├── agente-de-ingestao.md    # ingestão de ponta a ponta: agente/cliente, implantação, ambientes de teste, jobs, cuidados, guia Word
│   ├── pratica-ingestao-dev.md  # diário da prática do Eduardo na ingestão dev: acessos, objetos criados, consultas, próximos passos
│   ├── ambiente-de-teste-postgres.md # Postgres de teste ("test db"): pgAdmin web, databases, orientação do Todd
│   ├── postgres-e-resumos.md    # Postgres, réplica, pg_cron, tabelas de resumo
│   ├── superset-e-seguranca.md  # Superset, RLS, Descope, quem vê o quê, customizações, MCP
│   ├── migracao-powerbi-superset.md # inclui a evolução Flex Reports > Power BI > Superset
│   └── ideias-de-melhoria.md    # backlog informal de ideias do Eduardo
├── reunioes/
│   ├── 2026-10-26-rick-kickoff.md
│   ├── 2026-10-26-todd-tour-tecnico.md
│   ├── 2026-10-27-todd-ligacao-ingestao.md
│   └── 2026-10-29-sam-regras-de-negocio.md
└── onboarding/
    ├── primeiras-semanas.md     # cronograma, ações e situação das perguntas de follow-up
    └── perguntas-para-o-todd.md # cópia das perguntas (em inglês) do doc vivo do Eduardo
```

Pastas e arquivos previstos, a criar só quando houver conteúdo:

- `empresa/`: organização, relação com a Valsoft e liderança (do site: Our Story, Our Team).
- `clientes/`: arquivos por segmento ou por cliente importante e como consomem relatórios (hoje só `concessionarias.md`).
- `negocio/`: um arquivo por assunto quando `dominio-concessionaria.md` crescer (por exemplo, regras de contabilidade ou de serviço).
- `produtos/<marca>.md`: um arquivo por marca quando houver detalhe de arquitetura (hoje só Quorum DMS; faltam DealerMine, Autovance, Accessible Accessories).
- `q-analytics/`: catálogo de relatórios.
- `tecnologia/`: infraestrutura além do Q Analytics (AWS etc.), bancos de dados, repositórios.
- `decisoes/`: decisões técnicas e de negócio relevantes, uma por arquivo, quando surgirem.

## 4. Formato padrão de arquivo

Todo arquivo começa com este cabeçalho (YAML):

```
---
titulo: ...
resumo: uma frase dizendo do que o arquivo trata
atualizado_em: AAAA-MM-DD
fontes: [reunião X de AAAA-MM-DD, fulano disse Y]
status: rascunho | confirmado | desatualizado
---
```

Corpo recomendado: contexto curto, "Em palavras simples" (arquivos técnicos), conteúdo do assunto, e ao final a seção **Lacunas e dúvidas** (lista do que falta confirmar e com quem). Links entre arquivos usam caminho relativo ao `quorum/` (ex.: `q-analytics/visao-geral.md`).

`status`: `rascunho` = vindo de uma única fonte ou ainda incompleto; `confirmado` = validado por mais de uma fonte ou pelo responsável; `desatualizado` = sabidamente superado, aguardando revisão.

Fontes externas (site, documentos, imagens): citar a URL ou a origem e a data da consulta. Informação pública não substitui o que foi dito por pessoas da empresa; quando divergirem, registrar a divergência em "Lacunas e dúvidas". Imagens, diagramas, apresentações e documentos anexados (como o deck e o Word do Todd) não ficam arquivados no projeto: transcreva ou resuma o conteúdo em texto no arquivo de assunto e diga isso no próprio arquivo.

**Cópias de documentos vivos.** Alguns documentos vivem fora do projeto (por exemplo, um Claude Doc do Eduardo). Quando outros agentes precisarem vê-los, grave uma cópia em texto aqui, deixe claro no arquivo que é um snapshot, cite o link e a revisão, e avise que o original é a fonte.

**Glossário (`glossario.md`).** Glossário único, um verbete por conceito, em ordem alfabética, cobrindo termos técnicos e de negócio. Cada verbete separa **Geral** (explicação de conhecimento geral, que não veio de reunião) de **Na Quorum** (o que foi dito por alguém, com a fonte). Nunca misturar os dois no mesmo parágrafo, para que explicação geral não seja confundida com fato da empresa.

## 5. Convenção de nomes

- Minúsculas, palavras separadas por hífen, sem acentos nem espaços (`migracao-powerbi-superset.md`).
- Reuniões: `reunioes/AAAA-MM-DD-assunto-ou-pessoa.md`.
- Decisões: `decisoes/AAAA-MM-DD-titulo-curto.md`.
- Sempre informar o **caminho completo com `quorum/`** ao gravar. Um nome sem barra cai na pasta `claude/` do projeto e fica fora desta árvore.

## 6. Fluxo para processar uma nova reunião

1. Gravar a nota da reunião em `reunioes/` com o texto bruto fornecido pelo Eduardo, sem editar. É a fonte primária e **não é reescrita depois**; só se acrescentam correções em uma seção "Correções posteriores".
2. Extrair os fatos e **promovê-los** para os arquivos de assunto (produtos, q-analytics, pessoas etc.), atualizando arquivos existentes antes de criar novos.
3. Acrescentar ao `glossario.md` **todas** as palavras e conceitos novos que apareceram (técnicos e de negócio).
4. Na nota da reunião, manter a tabela "Onde cada fato foi documentado" apontando para os arquivos tocados.
5. Registrar dúvidas e pendências na seção "Lacunas e dúvidas" do arquivo de assunto correspondente; ações e perguntas de follow-up vão em `onboarding/primeiras-semanas.md` enquanto o onboarding durar; ideias de melhoria vão em `q-analytics/ideias-de-melhoria.md`.
6. Atualizar `00-indice.md`: linhas de novos arquivos e uma entrada no registro de mudanças.
7. Dizer ao Eduardo, de forma curta, o que foi criado/alterado e o que ficou como dúvida.

Nomes próprios e siglas em notas geradas por transcrição automática (como o Granola) podem vir errados (exemplos já vistos: DealerMind por DealerMine, dScope por Descope). Ao receber material de apoio (decks, diagramas, documentos), confira os nomes contra ele e registre a correção em "Correções posteriores". Se a data de uma reunião não consta nas notas, registre a data assumida como `(inferido)` e peça confirmação ao Eduardo.

Em conversas que misturam apresentação pessoal e conteúdo técnico (como a de 2026-10-29 com o Sam), separe nos arquivos de assunto: fatos da empresa, analogias e proporções ilustrativas (não são dados medidos) e relatos pessoais ou opiniões (visão da pessoa, não política da empresa). A nota bruta guarda tudo; os arquivos de assunto só levam o que ajuda o trabalho (princípio 7).

## 7. Como alterar e evoluir

**Alterar um arquivo existente**

1. Ler a versão atual do projeto imediatamente antes de editar (outra conversa pode ter mudado).
2. Regravar o arquivo **completo** (a ferramenta substitui o conteúdo inteiro; não há edição parcial).
3. Atualizar `atualizado_em` e `fontes`, e ajustar `status` se for o caso.
4. Registrar a mudança no registro de mudanças do `00-indice.md`.

**Conflito entre fontes:** a fonte mais recente prevalece, mas a informação antiga não some em silêncio. Registre a mudança no próprio arquivo (ex.: "Antes (2026-10-26): X. Atualizado em 2026-11-03 por Y: Z"). Se o erro está numa nota de reunião, não reescreva a nota: acrescente em "Correções posteriores". Em questões **técnicas**, a referência é o Todd (Eduardo, 2026-10-27); números aproximados de outras pessoas cedem aos dele.

**Evoluir a estrutura**

- *Dividir:* ao criar os novos arquivos, deixe no original um link para eles e reduza-o ao que sobrou.
- *Mesclar:* leve o conteúdo para o arquivo de destino e remova o antigo só depois de corrigir todos os links.
- *Renomear/mover:* grave no novo caminho, corrija links e índice, e então remova o caminho antigo.
- *Em qualquer caso:* atualizar a árvore da seção 3 deste guia e o `00-indice.md`, e anotar o motivo no registro de mudanças.
- Mudanças que alteram **as regras** deste guia devem ser combinadas com o Eduardo antes.

**Remover conteúdo:** exclusão é irreversível. Só apague arquivos quando o Eduardo pedir ou quando for parte de renomear/mesclar. Informação superada é marcada `status: desatualizado` em vez de apagada.

## 8. Para agentes que consomem a documentação

1. Comece sempre por `00-indice.md` e leia apenas os arquivos relevantes para a tarefa.
2. Para tarefas sobre Q Analytics, a ordem sugerida é `q-analytics/visao-geral.md`, depois os arquivos específicos (`arquitetura.md` primeiro), depois `produtos/marcas.md` se a tarefa envolver uma marca ou produto. Para entender o negócio por trás dos relatórios, leia `negocio/dominio-concessionaria.md` e `produtos/quorum-dms.md`. Para termos desconhecidos, consulte `glossario.md`.
3. Trate arquivos com `status: rascunho` como informação provável, não garantida, e confira a seção "Lacunas e dúvidas".
4. Se encontrar algo errado ou faltando, avise o Eduardo e proponha a correção seguindo a seção 7, em vez de alterar em silêncio informação que não tem como validar.

## Lacunas e dúvidas

- Definir se a documentação também será espelhada em repositório GitHub ou pasta local (hoje vive somente neste projeto). O Eduardo vai ganhar acesso à organização GitHub da Quorum, o que pode viabilizar isso.
