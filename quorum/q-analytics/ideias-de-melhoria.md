---
titulo: Ideias de melhoria para o Q Analytics
resumo: Backlog informal de ideias que o Eduardo pode propor para melhorar o Q Analytics e a ferramenta de ingestão, com contexto e situação de cada uma.
atualizado_em: 2026-10-28
fontes: [Eduardo, 2026-10-27 e 2026-10-28; ligação com Todd, 2026-10-27 (../reunioes/2026-10-27-todd-ligacao-ingestao.md)]
status: rascunho
---

# Ideias de melhoria para o Q Analytics

Lista informal, não é backlog oficial da Quorum. Cada ideia tem de onde veio e o que ainda falta confirmar. Antes de propor algo ao time, confirmar com o Todd que a ideia faz sentido e ainda não existe.

| Ideia | Origem | Situação |
|---|---|---|
| **Desfazer a exclusão de jobs pelo X** na ferramenta de ingestão | O Todd avisou que clicar no X pode apagar jobs, e que os pipelines atuais não têm backup (ligação de 2026-10-27). O Eduardo acredita que hoje não existe como desfazer (não confirmado) e vê aqui uma chance de agregar valor | Ideia. Falta confirmar com o Todd se existe algum jeito de recuperar |
| **Ver, na aba "Destination Servers", os pipelines e jobs ativos que usam cada servidor de destino** | Eduardo, 2026-10-28, ao explorar a versão dev da ferramenta de ingestão e tentar descobrir em qual servidor de destino os jobs gravam | Ideia. Falta confirmar com o Todd se essa informação já existe em outra tela |

## Ideia 1: desfazer a exclusão de jobs

- **Problema:** um clique no X apaga o job e, segundo o Todd, os pipelines incrementais e históricos atuais não têm backup. É um risco para quem está aprendendo e para a operação.
- **Caminhos possíveis (todos `inferido`, nada combinado):** pedir confirmação antes de excluir; uma "lixeira" que guarda o job apagado por um tempo; tirar um snapshot automático (a ferramenta já tem integração com GitHub para snapshots, ver [pipeline-ingestao.md](pipeline-ingestao.md)) antes de cada exclusão.
- **Antes de propor:** perguntar ao Todd se já existe recuperação (por exemplo, restaurar do Git) e se há interesse. Fazer isso depois de ter praticado na ferramenta, para falar com base na experiência.

## Ideia 2: ver quem usa cada servidor de destino

- **O que é:** na tela de **Destination Servers** da ferramenta de ingestão (lista dos servidores onde os dados são gravados), acrescentar uma opção para ver **os pipelines e jobs ativos que usam aquele servidor como destino**.
- **Problema:** a lista mostra os servidores de destino, mas, segundo o Eduardo, não mostra quem grava em cada um. Para saber se um servidor está em uso, é preciso olhar pipeline por pipeline e job por job `(inferido, a confirmar)`. Isso dificulta responder perguntas simples, como "onde os jobs deste cliente gravam?" ou "existe alguma coisa ativa gravando neste banco?".
- **Por que ajuda:** deixa claro o impacto antes de mudar ou remover um servidor de destino, evita confundir servidores de teste com os realmente usados e apoia quem está aprendendo a ferramenta. Combina com o cuidado da ideia 1: quanto mais visível o que depende de quê, menor o risco de apagar algo sem querer.
- **Em palavras simples:** é como a lista de contatos do celular mostrar, em cada contato, as conversas e os compromissos ligados a ele, em vez de obrigar a procurar um por um.
- **Antes de propor:** perguntar ao Todd se essa visão já existe em outra tela (por exemplo, dentro do pipeline ou do job) e se faz sentido para a ferramenta.

## Lacunas e dúvidas

- A ferramenta de ingestão já tem confirmação ou recuperação para exclusões? (o Eduardo acredita que não)
- A relação entre servidores de destino e jobs já aparece em alguma tela da ferramenta de ingestão?
- Quem decide e prioriza melhorias na ferramenta (Todd, Rick)?
