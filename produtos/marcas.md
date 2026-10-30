---
titulo: Marcas, produtos e cobertura no Q Analytics
resumo: Estrutura Valsoft > Quorum > 4 marcas > produtos, com o que cada produto faz (site público e Sam), a situação de cada um no Q Analytics segundo o diagrama do Rick, exemplos de fontes ingeridas e a infraestrutura conhecida.
atualizado_em: 2026-10-29
fontes: [diagrama do Rick, imagem enviada pelo Eduardo em 2026-10-27 (transcrita aqui), reunião com Rick 2026-10-26 (../reunioes/2026-10-26-rick-kickoff.md), reunião com Todd 2026-10-26 (../reunioes/2026-10-26-todd-tour-tecnico.md), ligação com Todd 2026-10-27 (../reunioes/2026-10-27-todd-ligacao-ingestao.md), conversa com Sam 2026-10-29 (../reunioes/2026-10-29-sam-regras-de-negocio.md), site público consultado em 2026-10-27 (páginas https://quoruminfotech.com/brands/, /dms/, /crm/, /sales-retailing/ e /service-retailing/)]
status: confirmado
---

# Marcas, produtos e cobertura no Q Analytics

## Estrutura (diagrama do Rick)

```
Valsoft
└── Quorum
    ├── Quorum DMS
    ├── DealerMine
    ├── Autovance
    └── Accessible Accessories
```

A Quorum é uma empresa do grupo **Valsoft** e tem **quatro marcas**: Quorum DMS, DealerMine, Autovance e Accessible Accessories. Cada marca agrupa produtos. O **PowerLane é um produto da marca Quorum DMS**, não uma marca.

(A imagem do diagrama foi compartilhada na conversa e não está arquivada no projeto; a transcrição abaixo é a referência.)

## Legenda de cobertura no Q Analytics

| Cor | Texto no diagrama | Leitura |
|---|---|---|
| Roxo | "Data Flowing in QAnalytics" | Dados já fluindo no Q Analytics |
| Amarelo | "Early Progress into QAnalytics" | Progresso inicial (nas notas da reunião: ETL em estágio inicial) |
| Vermelho | "Little or no data in QAnalytics" | Pouco ou nenhum dado (nas notas da reunião: relatórios limitados) |

## Produtos: o que fazem e situação no Q Analytics

A coluna "O que faz" vem do **site público** (consultado em 2026-10-27), não de alguém da Quorum; o que o Sam acrescentou está na seção seguinte. O site não usa os mesmos agrupamentos do diagrama; onde o site não nomeia o produto, está escrito "não consta no site".

| Marca | Produto | Cor | O que faz (site) |
|---|---|---|---|
| Quorum DMS | Quorum DMS | Roxo | Sistema central da concessionária: operações de **serviço e peças** (fixed ops), **vendas e leads** (variable ops) e **contabilidade** |
| Quorum DMS | PowerLane | Roxo | Modernizar a "service lane" (recepção da oficina): check-in/check-out pelo celular, inspeções digitais com mídia e comunicação em tempo real. O recurso **Declined Services** (serviços recusados, para recotar) faz parte dele |
| Quorum DMS | Communicator | Roxo | Módulo "Communications" do DMS: mensagens em tempo real entre cliente e equipe, marketing digital e campanhas automáticas |
| Quorum DMS | Sales Planner | Roxo | Não consta no site |
| Quorum DMS | Forecast Tool | Vermelho | Não consta no site |
| DealerMine | DealerMine Service CRM | Amarelo | CRM de serviço: aumentar contatos, agendamentos e receita de serviço, combinando automação e contato pessoal (inclui o "AI Marketing", com mensagens por SMS e voicemail) |
| DealerMine | DealerMine Sales CRM | Amarelo | CRM de vendas: acompanha cada cliente no funil, captura leads com follow-ups automáticos e dá acesso pelo celular |
| DealerMine | MORI | Amarelo | Não consta no site |
| Autovance | Autovance Desk | Amarelo | "Desking": estruturar o negócio (taxas, valores residuais, descontos) com desking móvel e planilhas claras para o cliente |
| Autovance | Autovance Menu | Amarelo | Menu de F&I (financiamento e seguros) apresentado em toda venda, para o cliente escolher |
| Autovance | Autovance MyDeal | Amarelo | Varejo digital: o cliente monta o próprio negócio online, ligado à transação na loja |
| Accessible Accessories | Accessible Accessories | Vermelho | Venda de acessórios (catálogo de montadoras e aftermarket) online e na loja; o recurso **Tire Boost** (cotação de pneus no negócio) faz parte dele |

Resumo: 12 produtos, sendo 4 roxos (todos da marca Quorum DMS), 6 amarelos (DealerMine e Autovance) e 2 vermelhos (Forecast Tool e Accessible Accessories).

**Outras ofertas no site que não aparecem como produto no diagrama:** o **Analytics** do DMS é o **Q Analytics** (a página do DMS o cita pelo nome) e reúne a atividade das lojas em relatórios em tempo real, inclusive para grupos de várias concessionárias; o **Online Scheduling** (cliente agenda serviço de qualquer dispositivo) aparece sob a marca DealerMine; o **BDC Services** (central de relacionamento com clientes terceirizada, focada em receita de serviço) também aparece sob a DealerMine; o **VINN Auto**, listado como marca, é um marketplace online que liga compradores a concessionárias e gera leads.

Descrição geral da empresa: [empresa/visao-geral.md](../empresa/visao-geral.md). Detalhe do Quorum DMS: [produtos/quorum-dms.md](quorum-dms.md).

## O que o Sam disse sobre os produtos (2026-10-29)

Visão de quem trabalha com os dados, em conversa informal (ver [reunioes/2026-10-29-sam-regras-de-negocio.md](../reunioes/2026-10-29-sam-regras-de-negocio.md)):

- A suíte tem **Quorum DMS, AutoVance (adquirido), DealerMine (CRM) e Accessible Accessories**. Os produtos podem ser **vendidos separados ou integrados**.
- O **AutoVance é "DMS agnostic"**: funciona com o Quorum DMS ou com DMS de concorrentes. É a ferramenta de estruturação de negócio (cenários à vista, financiado e leasing).
- O **DealerMine** integra com o DMS enviando agendamentos e prospects de clientes.
- O **Communicator** é o aplicativo de mensagens ligado ao DMS: mensagens internas da equipe e mensagens de saída a clientes, com suporte a mensagens automáticas.
- O **Lead Generator** acompanha leads e o retorno de marketing (por exemplo, gasto com anúncio de rádio x veículos vendidos); o Sam disse que **pode ainda estar ativo**. Ele não aparece no diagrama nem no site.
- O **Sales Planner** é "trabalho em andamento" (work in progress).
- O **PowerLane** foi só mencionado, sem detalhes.

## Exemplos de fontes ingeridas (reuniões com Todd)

O Todd citou, como **exemplos** de fontes da ferramenta de ingestão: **QDMS, AutoVance, Communicator, DealerMine, Powerlane, Zoho CRM, Zoho Desk e Carbon60** (ver [q-analytics/pipeline-ingestao.md](../q-analytics/pipeline-ingestao.md)). Isso é compatível com o diagrama: os produtos roxos e amarelos já têm alguma ingestão. QDMS provavelmente é o Quorum DMS (inferido). Zoho CRM, Zoho Desk e Carbon60 não aparecem no diagrama de produtos. Sales Planner (roxo) não foi citado, mas a lista é de exemplos.

Detalhes da ligação de 2026-10-27 (ver [q-analytics/agente-de-ingestao.md](../q-analytics/agente-de-ingestao.md)):

- **Zoho (CRM e Desk) é interno:** é usado pela própria Quorum, não é uma fonte de dados para clientes. Vários jobs podem rodar contra um único cliente Zoho.
- **Powerlane** (produto do Quorum DMS) é ingerido por **API**: um cliente, vários jobs com chamadas diferentes.
- **DealerMine:** existe um banco de teste **MSSQL** do DealerMine, com dados reais de clientes exportados, usado no ambiente de migração de dados. Isso indica que o DealerMine tem uma base em SQL Server `(inferido)`; a nota não diz isso diretamente.
- O **Veeva** foi citado como mais um cliente de API com configuração própria; o Eduardo decidiu que não é prioridade agora.

## Infraestrutura e bancos

- **AutoVance e Accessible Accessories** rodam em **AWS** (Rick, 2026-10-26).
- Os bancos de dados incluem **Postgres, MySQL e T-SQL**, sem informação de qual marca/produto usa qual (Rick).
- Infraestrutura de Quorum DMS e DealerMine: não informada.
- O próprio **Q Analytics** roda autohospedado em colocation em Calgary (Todd); ver [q-analytics/arquitetura.md](../q-analytics/arquitetura.md).

## Objetivo

Expandir o [Q Analytics](../q-analytics/visao-geral.md) para todos os produtos, não só o Quorum DMS (explicação do Rick ao Eduardo, que o diagrama ilustra). Na prática, é levar os produtos amarelos e vermelhos ao roxo.

## Lacunas e dúvidas

- Qual marca/produto usa qual banco (Postgres, MySQL, T-SQL) e onde roda cada um?
- Há prioridade definida entre os produtos amarelos e vermelhos para a expansão?
- **MORI, Sales Planner e Forecast Tool** não aparecem no site: são produtos internos, mais novos ou de nome interno? O que cada um faz? O Sam diz que o Sales Planner é trabalho em andamento, mas o diagrama o mostra em roxo (dados fluindo): o produto ainda está em desenvolvimento enquanto os dados já chegam?
- **Lead Generator:** de qual marca é, e ainda está ativo? Não aparece no diagrama.
- O Granola diz que o AutoVance foi "adquirido do Rick". Quem é esse Rick (o CTO? outro? erro de transcrição) e de quem a Quorum adquiriu o AutoVance?
- QDMS é o Quorum DMS? E o que é o Carbon60 em relação aos produtos?
- O **VINN Auto**, marketplace listado no site como marca, não aparece no diagrama. Está fora do escopo do Q Analytics, é de outro grupo ou foi descontinuado? (O site vinnauto.com não foi consultado.)
- O Online Scheduling e o BDC Services (site, sob a DealerMine) fazem parte do produto "DealerMine Service CRM" do diagrama?
- Quando houver detalhe de arquitetura de outra marca, criar `produtos/<marca>.md` (já existe [quorum-dms.md](quorum-dms.md)) e deixar aqui só o resumo (ver guia).
