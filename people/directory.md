---
title: People directory
summary: Who is who at Quorum, with role, topics they help with and time zone when known.
updated_at: 2026-10-29
sources: [meeting with Rick, 2026-10-26 (../meetings/2026-10-26-rick-kickoff.md), meeting with Todd, 2026-10-26 (../meetings/2026-10-26-todd-technical-tour.md), call with Todd, 2026-10-27 (../meetings/2026-10-27-todd-ingestion-call.md), conversation with Sam, 2026-10-29 (../meetings/2026-10-29-sam-business-rules.md), information from Eduardo, public website https://quoruminfotech.com/about-us/ (consulted on 2026-10-27)]
status: draft
---

# People directory

Format: one line per person. Record only what matters for the work. When someone accumulates a lot of context, create `people/<name>.md` and leave the summary line here with a link.

About time zones: the team is in Canada and the vast majority is in MT (Mountain Time), 3h behind Brazil today (see [company/ways-of-working.md](../company/ways-of-working.md), including the change expected after 2026-11-01). Where the table says "MT (inferred)", it is an assumption from "the vast majority is in MT", with no individual confirmation.

| Name | Role | Ask about | Time zone | Source |
|---|---|---|---|---|
| Rick | Quorum's CTO | Technology overview, priorities; leads Eduardo's onboarding and will do the Wednesday check-in | MT (Canada) | Meeting 2026-10-26; Eduardo |
| Todd | IT Manager / Infrastructure Manager; about 20 years at Quorum; technical reference for Q Analytics | Q Analytics architecture, ingestion tool (agent deployment, test environments, jobs), Postgres, Superset, infrastructure (Calgary colocation), GitHub and access | MT (Canada) | Eduardo (role, tenure, time zone); meetings 2026-10-26 and 2026-10-27 |
| Andrea (Noseworthy) | Director, Human Resources | HR matters (to be detailed as Eduardo interacts) | MT (inferred) | Eduardo (role); website (surname and role match) |
| Sam | **Data and reporting team lead** (Eduardo, 2026-10-29); built over 70% of the original Power BI reports over 10 years | Dealership business rules, how Quorum DMS works, existing reports and history; helps get access to the DMS test server | MT (inferred) | Meeting 2026-10-26; conversation 2026-10-29; Eduardo |
| Michael | Owner of the epic of new report stories; role not provided | Priorities and scope of the new reports | MT (inferred) | Meeting 2026-10-26 |
| Justin | Mentioned by Sam as co-builder of the ingestion tool, with Todd; no role | To be confirmed (the name may be a transcription error) | n/k | Conversation with Sam, 2026-10-29 (Granola) |
| Terry | Executive ("C-suite") mentioned by Sam as an example of someone who sees the data of all customers | Not a day-to-day contact | n/k | Conversation with Sam, 2026-10-29 (Granola) |
| Eduardo | Works on Q Analytics reports (exact title to be confirmed); based in Brazil | n/a | Brazil (BRT, UTC-3) | Eduardo |

## Details per person

- **Todd:** about 20 years at the company and with great technology knowledge ("the brain behind it", according to Eduardo); when in technical doubt, he is the reference (for example, the number of 256 databases prevails over Rick's approximate figure). Built the ingestion tool (Go, according to Rick) and handled about 70% of the build of the migration to Superset/Postgres. Gave Eduardo the technical tour on 2026-10-26 (see [meetings/2026-10-26-todd-technical-tour.md](../meetings/2026-10-26-todd-technical-tour.md)). Will add Eduardo to Quorum's GitHub organization, point him to the QAnalytics repositories and READMEs, and generate a Superset README if documentation is lacking. On 2026-10-27 he sent the access Eduardo had asked for, warned him to be careful with the X in the ingestion tool (it can delete jobs) and explained by call the test environment and client deployment (see [meetings/2026-10-27-todd-ingestion-call.md](../meetings/2026-10-27-todd-ingestion-call.md)). He was to send Postgres credentials and the test server's IP. Whoever deploys the agents at the dealerships is the **infrastructure team** (Todd is its manager); Todd shared the agent's Word guide.
- **Sam:** on 2026-10-29 had a conversation of about 2 hours with Eduardo (dealership business, DMS, history of the reports, work culture; see [meetings/2026-10-29-sam-business-rules.md](../meetings/2026-10-29-sam-business-rules.md)), partly to get to know each other. He was to help Eduardo with access to the DMS test server and is available to talk again the same day; shadowing on the existing reports (3 to 4 hours) remains agreed for this week. According to Rick (2026-10-26), he moves to the support team in the week of 2026-11-02; this still needs to be reconciled with the role of data and reporting team lead. His analogies and opinions are in [company/culture-and-expectations.md](../company/culture-and-expectations.md) as a personal view.
- **Rick:** will send the diagram and the meeting notes and will schedule a 30-minute check-in on Wednesday.

## Leadership listed on the public website

The website ("About us" page, team section, consulted on 2026-10-27) lists the people below. It is public information and does **not** say who is who in Eduardo's day-to-day; the matches with the people in the table above are only hypotheses.

| Name on the website | Title on the website |
|---|---|
| Terrance Trinh | Managing Director |
| Rick Johnston | Chief Product Officer |
| Jane Webb | VP, Sales & Marketing |
| Bruce Atkinson | VP, Operations |
| Michael Cox | VP, Technology |
| Yossi Ronnen | VP, Software Development (mentioned only in the biography) |
| Mark Cashin | Senior Director, Finance |
| Jennifer Phillips | Director, Enterprise Accounts |
| Katie Gallant | Senior Manager, Regional Accounts |
| John Hynes | Director, Product Support |
| Kelly Cribb | Director, BDC Operations |
| Darren Hawthorne | Director, Product Infrastructure |
| Andrea Noseworthy | Director, Human Resources |
| Garry Grattly | Manager, Implementations & Training |

Remarks: Eduardo's **Rick** is CTO, whereas the website lists "Rick Johnston, Chief Product Officer"; **Todd** and **Sam** do not appear in the list (they are not leadership); "Michael Cox, VP, Technology" may be the Michael of the reports epic `(inferred)`; the "Terry" mentioned by Sam may be "Terrance Trinh, Managing Director" `(inferred)`.

## Gaps and open questions

- Is the website's "Rick Johnston, Chief Product Officer" Eduardo's CTO Rick (different title on the website)? Granola also says AutoVance was "acquired from Rick" (see [products/brands.md](../products/brands.md)).
- Is "Michael Cox, VP, Technology" the Michael who owns the reports epic? Who replaces Sam as the reference for the reports after he moves to support, and what is his role in support?
- Who is Justin (co-builder of the ingestion tool, according to Sam)? Is he still at the company? Is it the right name?
- Who is Terry: the website's Terrance Trinh?
- Actual time zone of Andrea, Sam and Michael (currently inferred as MT).
- Other key people to map (product, support, leaders of each brand) as they come up in meetings.
- Who is Eduardo's direct manager (Rick or someone else)?
- Who else is on the infrastructure team that deploys the agents? Is the website's "Darren Hawthorne, Director, Product Infrastructure" related to it? And who staffs the help desk mentioned by Sam for requesting access?
