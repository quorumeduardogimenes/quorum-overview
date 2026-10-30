---
titulo: Quorum DMS: o que é e como os dados se organizam
resumo: O DMS como software de contabilidade que recebe os dados de todas as áreas da concessionária, seus módulos, a área de CRM, o banco "Dealer" e a existência de um ambiente de teste.
atualizado_em: 2026-10-29
fontes: [conversa com Sam, 2026-10-29 (../reunioes/2026-10-29-sam-regras-de-negocio.md)]
status: rascunho
---

# Quorum DMS: o que é e como os dados se organizam

DMS é a sigla de Dealer Management System (sistema de gestão de concessionária). Visão pública do produto em [marcas.md](marcas.md); as telas e o acesso serão documentados depois, a partir de prints do Eduardo.

## Em palavras simples

O Sam descreveu o DMS como, no fundo, um **software de contabilidade**. Serviço, vendas, peças e funilaria têm cada um suas tabelas, mas **todos escrevem no mesmo livro-razão** (a contabilidade). Por isso o DMS é a "espinha" do negócio da concessionária: para entender de onde vêm os números, é preciso entender como cada área alimenta a contabilidade.

## Como os dados se organizam (Sam)

- Cada área (serviço, vendas, peças e funilaria) tem **seu próprio conjunto de tabelas**, e todas gravam na **contabilidade** (esquema compartilhado de livro-razão).
- **Categorias contábeis:** vendas, custo, despesas, outras receitas, ativos e passivos.
- **Lançamentos (journal entries) e conciliações** são feitos dentro do próprio DMS.
- **Banco "Dealer":** sempre presente em todo servidor, é o banco de entrada. Grupos de concessionárias têm bancos adicionais por loja (ver [clientes/concessionarias.md](../clientes/concessionarias.md)).

## Módulos (visto na demonstração do Sam)

Na faixa de botões (ribbon) do DMS aparecem: **serviço, peças, veículos, contabilidade e CRM**. Na área de **CRM**: prospects (potenciais clientes), agendamentos de venda e agendamentos de serviço. O **DealerMine** integra com o DMS enviando agendamentos e prospects.

## Produtos ligados ao DMS

O **Communicator** é o aplicativo de mensagens do DMS, o **AutoVance** funciona com o DMS ou com outros, e o **PowerLane** é produto da marca. Detalhes em [marcas.md](marcas.md).

## Ambiente de teste

Existe um **servidor de teste do DMS** (identificado pelo Sam como ZZZ509), alcançado pelo "QIS Connector" / colocation. O Sam disse que é **seguro explorar** e que o Eduardo **não deve se conectar a ambientes de clientes com nome**. O pedido de acesso deve ser feito ao Todd ou ao help desk, e o Sam vai ajudar. Passos e telas serão documentados quando o Eduardo enviar os prints. (Nenhum endereço ou credencial é registrado aqui.)

## Lacunas e dúvidas

- O que é o "QIS Connector" e qual a relação com o colocation ("Colo")? É o mesmo "DM Colo" da ferramenta de ingestão?
- Em que tecnologia (SQL Server? outra) rodam os bancos do DMS? A ferramenta de ingestão usa SQL Server e change tracking (ver [q-analytics/pipeline-ingestao.md](../q-analytics/pipeline-ingestao.md)).
- Quais são os nomes das tabelas e esquemas de cada área e da contabilidade (mapa de dados)?
- Como o acesso ao DMS de teste é feito na prática (documentar com os prints)?
