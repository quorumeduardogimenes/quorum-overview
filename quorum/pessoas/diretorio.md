---
titulo: Diretório de pessoas
resumo: Quem é quem na Quorum, com papel, assuntos em que ajuda e fuso quando conhecido.
atualizado_em: 2026-10-29
fontes: [reunião com Rick, 2026-10-26 (../reunioes/2026-10-26-rick-kickoff.md), reunião com Todd, 2026-10-26 (../reunioes/2026-10-26-todd-tour-tecnico.md), ligação com Todd, 2026-10-27 (../reunioes/2026-10-27-todd-ligacao-ingestao.md), conversa com Sam, 2026-10-29 (../reunioes/2026-10-29-sam-regras-de-negocio.md), informação do Eduardo, site público https://quoruminfotech.com/about-us/ (consultado em 2026-10-27)]
status: rascunho
---

# Diretório de pessoas

Formato: uma linha por pessoa. Registrar só o que importa para o trabalho. Quando alguém acumular muito contexto, criar `pessoas/<nome>.md` e deixar aqui a linha-resumo com link.

Sobre fusos: o time está no Canadá e a grande maioria no MT (Mountain Time), 3h atrás do Brasil hoje (ver [empresa/modo-de-trabalho.md](../empresa/modo-de-trabalho.md), inclusive a mudança esperada após 2026-11-01). Onde a tabela diz "MT (inferido)", é suposição a partir de "a grande maioria está em MT", sem confirmação individual.

| Nome | Papel | Perguntar sobre | Fuso | Fonte |
|---|---|---|---|---|
| Rick | CTO da Quorum | Visão geral de tecnologia, prioridades; é quem conduz o onboarding do Eduardo e fará o check-in de quarta | MT (Canadá) | Reunião 2026-10-26; Eduardo |
| Todd | IT Manager / Infrastructure Manager; cerca de 20 anos na Quorum; referência técnica do Q Analytics | Arquitetura do Q Analytics, ferramenta de ingestão (implantação de agentes, ambientes de teste, jobs), Postgres, Superset, infraestrutura (colocation em Calgary), GitHub e acessos | MT (Canadá) | Eduardo (cargo, tempo de casa, fuso); reuniões 2026-10-26 e 2026-10-27 |
| Andrea (Noseworthy) | Diretora de RH (Director, Human Resources) | Assuntos de RH (a detalhar conforme o Eduardo interagir) | MT (inferido) | Eduardo (cargo); site (sobrenome e cargo conferem) |
| Sam | **Team lead de dados e reporting** (Eduardo, 2026-10-29); construiu mais de 70% dos relatórios Power BI originais ao longo de 10 anos | Regras de negócio das concessionárias, funcionamento do Quorum DMS, relatórios existentes e histórico; ajuda a obter acesso ao servidor de teste do DMS | MT (inferido) | Reunião 2026-10-26; conversa 2026-10-29; Eduardo |
| Michael | Dono do epic de novas stories de relatórios; cargo não informado | Prioridades e escopo dos novos relatórios | MT (inferido) | Reunião 2026-10-26 |
| Justin | Citado pelo Sam como co-construtor da ferramenta de ingestão, com o Todd; sem cargo | A confirmar (o nome pode ser erro de transcrição) | n/i | Conversa com Sam, 2026-10-29 (Granola) |
| Terry | Executivo ("C-suite") citado pelo Sam como exemplo de quem vê os dados de todos os clientes | Não é contato do dia a dia | n/i | Conversa com Sam, 2026-10-29 (Granola) |
| Eduardo | Trabalha com relatórios do Q Analytics (título exato a confirmar); baseado no Brasil | n/a | Brasil (BRT, UTC-3) | Eduardo |

## Detalhes por pessoa

- **Todd:** cerca de 20 anos na empresa e com grande conhecimento de tecnologia ("o cérebro por trás", segundo o Eduardo); na dúvida técnica, ele é a referência (por exemplo, o número de 256 bancos prevalece sobre o aproximado do Rick). Construiu a ferramenta de ingestão (Go, segundo o Rick) e cuidou de cerca de 70% da construção da migração para Superset/Postgres. Deu ao Eduardo, em 2026-10-26, o tour técnico (ver [reunioes/2026-10-26-todd-tour-tecnico.md](../reunioes/2026-10-26-todd-tour-tecnico.md)). Vai adicionar o Eduardo à organização GitHub da Quorum, apontar os repositórios e READMEs do QAnalytics e gerar um README do Superset se faltar documentação. Em 2026-10-27 enviou os acessos pedidos pelo Eduardo, avisou para ter cuidado com o X da ferramenta de ingestão (pode apagar jobs) e explicou por ligação o ambiente de teste e a implantação de clientes (ver [reunioes/2026-10-27-todd-ligacao-ingestao.md](../reunioes/2026-10-27-todd-ligacao-ingestao.md)). Ficou de enviar credenciais do Postgres e o IP do servidor de teste. Quem implanta os agentes nas concessionárias é a **equipe de infraestrutura** (Todd é o gerente dela); o Todd compartilhou o guia Word do agente.
- **Sam:** em 2026-10-29 teve uma conversa de cerca de 2 horas com o Eduardo (negócio das concessionárias, DMS, história dos relatórios, cultura de trabalho; ver [reunioes/2026-10-29-sam-regras-de-negocio.md](../reunioes/2026-10-29-sam-regras-de-negocio.md)), em parte para se conhecerem. Ficou de ajudar o Eduardo com o acesso ao servidor de teste do DMS e está disponível para voltar a conversar no mesmo dia; o shadowing nos relatórios existentes (3 a 4 horas) segue combinado para esta semana. Pelo Rick (2026-10-26), ele passa para o time de suporte na semana de 2026-11-02; falta conciliar isso com o cargo de team lead de dados e reporting. As analogias e opiniões dele estão em [empresa/cultura-e-expectativas.md](../empresa/cultura-e-expectativas.md) como visão pessoal.
- **Rick:** enviará o diagrama e as notas da reunião e agendará check-in de 30 min na quarta.

## Liderança listada no site público

O site (página "Sobre nós", seção da equipe, consultado em 2026-10-27) lista as pessoas abaixo. É informação pública e **não** diz quem é quem no dia a dia do Eduardo; as correspondências com as pessoas da tabela acima são apenas hipóteses.

| Nome no site | Cargo no site |
|---|---|
| Terrance Trinh | Managing Director |
| Rick Johnston | Chief Product Officer |
| Jane Webb | VP, Sales & Marketing |
| Bruce Atkinson | VP, Operations |
| Michael Cox | VP, Technology |
| Yossi Ronnen | VP, Software Development (citado só na biografia) |
| Mark Cashin | Senior Director, Finance |
| Jennifer Phillips | Director, Enterprise Accounts |
| Katie Gallant | Senior Manager, Regional Accounts |
| John Hynes | Director, Product Support |
| Kelly Cribb | Director, BDC Operations |
| Darren Hawthorne | Director, Product Infrastructure |
| Andrea Noseworthy | Director, Human Resources |
| Garry Grattly | Manager, Implementations & Training |

Observações: o **Rick** do Eduardo é CTO, enquanto o site lista "Rick Johnston, Chief Product Officer"; **Todd** e **Sam** não aparecem na lista (não são da liderança); "Michael Cox, VP, Technology" pode ser o Michael do epic de relatórios `(inferido)`; o "Terry" citado pelo Sam pode ser o "Terrance Trinh, Managing Director" `(inferido)`.

## Lacunas e dúvidas

- O "Rick Johnston, Chief Product Officer" do site é o Rick CTO do Eduardo (cargo diferente no site)? O Granola também diz que o AutoVance foi "adquirido do Rick" (ver [produtos/marcas.md](../produtos/marcas.md)).
- O "Michael Cox, VP, Technology" é o Michael dono do epic de relatórios? Quem substitui o Sam como referência dos relatórios depois que ele for para o suporte, e qual o papel dele no suporte?
- Quem é o Justin (co-construtor da ferramenta de ingestão, segundo o Sam)? Ainda está na empresa? É o nome certo?
- Quem é o Terry: o Terrance Trinh do site?
- Fuso real de Andrea, Sam e Michael (hoje inferido como MT).
- Outras pessoas-chave a mapear (produto, suporte, líderes de cada marca) conforme surgirem em reuniões.
- Quem é o gestor direto do Eduardo (Rick ou outro)?
- Quem mais compõe a equipe de infraestrutura que implanta os agentes? O "Darren Hawthorne, Director, Product Infrastructure" do site tem relação com ela? E quem atende o help desk citado pelo Sam para pedir acessos?
