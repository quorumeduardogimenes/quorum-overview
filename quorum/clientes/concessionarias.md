---
titulo: Clientes: as concessionárias
resumo: Quantas concessionárias são clientes, como cada uma tem seu servidor e seus dados, e o que muda entre lojas isoladas e grupos.
atualizado_em: 2026-10-29
fontes: [conversa com Sam, 2026-10-29 (../reunioes/2026-10-29-sam-regras-de-negocio.md), reunião com Rick, 2026-10-26, reunião com Todd, 2026-10-26]
status: rascunho
---

# Clientes: as concessionárias

## Em palavras simples

Pense em uma receita de bolo ("cookie cutter", como o Sam disse): todas as concessionárias usam o **mesmo molde** de banco de dados (mesmas tabelas e campos), mas cada uma tem o **seu próprio servidor** com os **seus próprios dados**. Por isso um relatório escrito uma vez serve para todas, e a ingestão precisa ir buscar os dados em cada servidor separadamente.

## O que sabemos

- **Número de clientes:** o Sam disse cerca de **240** concessionárias, espalhadas pela **América do Norte**, cada uma em seu **próprio servidor**.
- **Mesmo esquema** em todos os servidores, com dados diferentes em cada um.
- **Porte:** de lojas únicas a **grupos de concessionárias** (enterprise groups) com várias unidades e marcas.
- **Banco "Dealer":** existe em todo servidor como banco de entrada. Os grupos têm **bancos adicionais, um por loja**, com o nome da loja.
- **Consumo dos dados:** cada concessionária vê só os seus dados nos dashboards (ver [q-analytics/superset-e-seguranca.md](../q-analytics/superset-e-seguranca.md)).

## Números que não batem entre fontes

| Fonte | Número | Observação |
|---|---|---|
| Sam (2026-10-29) | ~240 clientes | Aproximado |
| Rick (2026-10-26) | ~250 concessionárias | Aproximado |
| Todd (2026-10-26) | 256 bancos de concessionárias | Referência técnica |

Pelo guia, o número do Todd prevalece em questões técnicas. É um número de **bancos**, não de clientes. `(inferido)` A diferença pode vir de grupos com vários bancos (um por loja), mas isso precisa ser confirmado.

## Lacunas e dúvidas

- Quantos dos ~240 clientes são lojas únicas e quantos são grupos? Quantos bancos tem o maior grupo?
- Os 256 bancos do Todd são os bancos "Dealer" mais os bancos por loja dos grupos?
- Todos os clientes usam o Quorum DMS? Há clientes só de outros produtos (por exemplo, AutoVance sozinho)?
- Como são identificados clientes e lojas nos dados (código de concessionária, nome)?
