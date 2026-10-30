---
titulo: Como funciona uma concessionária (regras de negócio)
resumo: Departamentos, estoques, modelo de receita, jornada do cliente, termos e modelos de preço de uma concessionária, como o Sam explicou ao Eduardo.
atualizado_em: 2026-10-29
fontes: [conversa com Sam, 2026-10-29 (../reunioes/2026-10-29-sam-regras-de-negocio.md)]
status: rascunho
---

# Como funciona uma concessionária (regras de negócio)

## Em palavras simples

O Sam usou uma analogia do corpo humano: **vendas** é uma mão, **serviço** é a outra mão, **peças** é o pé direito e a **contabilidade** é a coluna/cérebro, porque tudo o que acontece nas outras áreas acaba registrado nela. É uma analogia didática dele, não um termo da empresa. Para os relatórios, a ideia útil é: cada área gera seus próprios dados, e o dinheiro de todas aparece na contabilidade.

## Departamentos

| Departamento | Observação |
|---|---|
| Vendas (sales) | Vende veículos |
| Serviço (service) | Manutenção e reparos |
| Peças (parts) | Vende e fornece peças |
| Funilaria (body shop) | **Nem toda concessionária tem** |
| Contabilidade (accounting) | Recebe os lançamentos de todas as áreas |

## Dois estoques diferentes

- **Estoque de veículos:** o que os vendedores vendem.
- **Estoque de peças:** usado pelos departamentos de serviço e de peças.

## Modelo de receita

- A venda do veículo é a "ponta do iceberg": o Sam falou em **10%** da receita, contra **90%** vindos do serviço recorrente. São proporções **ilustrativas ditas por ele**, não um número medido.
- O dinheiro de verdade vem de **reter o cliente** para serviços: troca de óleo, pneus e manutenção.

## Jornada do cliente

Agendamento de venda, depois o **F&I / escritório de negócios** (financiamento e seguros, papelada), formulários, **instalação de acessórios** e, depois, as visitas de serviço.

## Termos e regras

| Tema | O que o Sam disse |
|---|---|
| Ordem de serviço | **Work order = repair order = WO = RO**. O DMS usa "work order"; o CRM usa "repair order" |
| Venda de peças | **Balcão** (over-the-counter, para clientes) x **interna** (para o serviço). A venda interna tem **volume muito maior** |
| Preço do serviço | **Por hora** (exemplo dado: US$ 200/hora) ou **flat rate** (preço fixo, independentemente do tempo gasto) |
| Mensagens automáticas | Chamadas de "plugins" pelo Sam: enviadas à equipe (por exemplo, pedido de peças) e a clientes (por exemplo, lembrete de garantia estendida) |

## Por que isso importa para os relatórios `(inferido)`

- O mesmo conceito tem nomes diferentes entre sistemas (WO no DMS, RO no CRM), então relatórios que cruzam as duas fontes precisam tratar isso.
- Como o serviço é a maior parte da receita, relatórios de serviço e retenção tendem a ser prioritários para as concessionárias.
- Termos e definições em [glossario.md](../glossario.md); como os dados ficam no sistema, em [produtos/quorum-dms.md](../produtos/quorum-dms.md).

## Lacunas e dúvidas

- Sempre que o Sam disser "plugins": o que é isso tecnicamente (regras de mensagem do Communicator? outro módulo)?
- Como o flat rate e o preço por hora aparecem nos dados (tabelas e campos) e nos relatórios existentes?
- Quais relatórios existentes seguem cada departamento? (Catálogo de relatórios ainda não mapeado.)
