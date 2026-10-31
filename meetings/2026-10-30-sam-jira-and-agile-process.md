---
title: D&R conversation with Sam about Jira and the agile process, 2026-10-30
summary: Note (Granola) from Eduardo's second conversation with Sam, in which they covered team ownership, the state of the Jira board and backlog, the lack of Scrum ceremonies and QA, AI tooling in the workflow, security hygiene and Eduardo's onboarding.
updated_at: 2026-10-30
sources: [Granola summary of the meeting "D&R" with Sam (Sam Landrie), 2026-10-30 at 10:00 BRT, read through the Granola connector on 2026-10-30 (https://notes.granola.ai/d/b50c4632-d011-459c-aee2-4b448186dc0a; Granola summary, not the full transcript); context reported by Eduardo]
status: confirmed
---

# D&R conversation with Sam, 2026-10-30

- **Date:** 2026-10-30 (Friday), 10:00 BRT. The Granola note was last updated at 11:08 BRT.
- **Participants:** Eduardo and Sam (Sam Landrie).
- **Title in Granola:** "D&R". What the abbreviation stands for is not stated in the note.
- **Context (Eduardo):** second conversation with Sam, this time about Jira and how the agile process works for the technology teams. Eduardo's reading: it is **quite disorganized for now; nothing is perfect, but it is running**.
- **This note is a primary source:** the raw text below (Granola, in English) is not rewritten. Corrections go in "Later corrections".

## How to read these notes

- The Granola text is an **AI summary**, not a transcript. Statements about the team and the process are **Sam's view** as of 2026-10-30, not official Quorum policy.
- The note starts with a section called "Morning Catch-up", which records a personal conversation between the two that is not about work. It was **left out on purpose** (principle 7 of the [documentation guide](../documentation-guide.md): no sensitive personal data). It remains in Granola.
- The section "Eduardo's Onboarding and Observations" mixes what Sam said with what Eduardo said about himself; the subject files separate them.

## Where each fact was documented

| Topic | File |
|---|---|
| Who owns what (product, dev, infrastructure, product support), gray areas, roles nobody holds (Scrum Master, product owner, QA), sprint 116, stale backlog, no ceremonies, Jira PBI Creator skill and Estabot, QA and acceptance gaps, Eduardo's Jira access and approach | [company/agile-process-and-jira.md](../company/agile-process-and-jira.md) |
| Shared Power BI service account, employee names shown in Superset, no written rules on secrets, SOC 2 goal, secret scanning | [company/governance-and-security-practices.md](../company/governance-and-security-practices.md) |
| Todd, Darren, Patrick, Rick and Sam in their roles for this process | [people/directory.md](../people/directory.md) |
| Jira as a tool, link to the process file | [company/ways-of-working.md](../company/ways-of-working.md) |
| Meeting row, Jira access, next steps | [onboarding/first-weeks.md](../onboarding/first-weeks.md) |
| New terms (Sprint, Backlog, PBI, Refinement, Product owner, Scrum Master, Ready for Acceptance, Estabot, Jira PBI Creator, Skill, AI Olympics, Service account, SOC 2, Secrets) | [glossary.md](../glossary.md) |

## Original notes (in English, unedited, except the personal section omitted above)

Team Structure and Ownership

- Product (Rick) drives dev; dev leans on infrastructure
  - Todd: internal infrastructure (data, cybersecurity)
  - Darren: customer infrastructure (servers, hosting)
  - Patrick: external product owner who sends over occasional requests
- Gray areas of ownership between product, dev, and infrastructure are a known, ongoing issue
  - No stable process; requests sometimes bypass product and go directly to dev
  - Product support should funnel feature requests and bugs through product, but often doesn't

Jira Board and Backlog State

- Currently on sprint 116; much of the product backlog is stale or obsolete
  - Items like ACA pipeline credentials and Synapse references are deprecated
  - Backlog needs human attention and cleanup, but isn't getting it
- No dedicated Scrum Master or product owner currently
  - Sam has been functioning as product owner, Scrum Master, dev, and QA combined
  - Logs time against a standing refinement issue (can run ~2 hrs/day)
- Formal Scrum ceremonies (sprint planning, retros, dailies, refinement) no longer happen
  - Meetings are ad hoc, triggered by blockers or update requests
  - Sam escalates to Rick when stuck rather than sitting idle

Claude and AI Tooling in the Workflow

- Sam runs Claude via Visual Basic; connected to GitHub, local repo clones, Jira, and SQL servers
- Uses a Jira PBI Creator skill (built by Rick) to auto-refine backlog items
  - After refinement, Estabot label is applied automatically for story point estimation
- Story point estimation is a known friction point
  - Points measure complexity, not time, but stakeholders conflate the two
- Team ran an "AI Olympics" initiative to explore AI use cases
- MCP connectors used for Jira and other integrations; credentials stored outside Claude

QA and Testing Gaps

- No dedicated QA resource; developers test their own work
  - Dev environment is customer-inaccessible, but changes go straight to prod once the dev is satisfied
  - No second set of eyes on changes before release
- Acceptance flow is broken: items sit in "Ready for Acceptance" with no notification until moved to "Done"
- Loss of QA resource likely due to resource constraints

Security and Repository Hygiene

- Shared Power BI service account in use: auditable by IP, but a single kill-switch risk
- Superset previously showed real employee names (Sam, Todd) as report creators to customers
  - Now masked as "Quorum Analytics" but the gap highlights a broader governance issue
- No firm stance or written rules on secrets, credentials, or repo hygiene
- SOC 2 compliance is a goal; repo scanning for secrets/credentials not yet in place
  - Sam's view: an automated Claude skill should scan repos for exposed secrets as a baseline
  - READMEs and .gitignore files also need ongoing maintenance

Eduardo's Onboarding and Observations

- Rick has granted Jira access; no cards assigned yet (one appeared during the call)
- Eduardo explored Superset and the Data Ingestion 2 pipeline; has early notes and improvement ideas
- Shared philosophy: flag issues, document them, suggest improvements without forcing change
  - Example from prior role: automated Dundas BI deploy pipeline via REST APIs, cutting deploy time from ~1 hour to ~3 minutes
- Sam's stance: fully open to new ideas; worst reason to keep doing something is "it's how we've always done it"
- Sam encouraged Eduardo to document observations in Jira issues at minimum

Next Steps

- **Review Eduardo's early notes and improvement ideas** (Sam, Eduardo)

  Scheduled for an hour after this call.

## Later corrections

- **2026-10-30, to be confirmed:** "Sam runs Claude via Visual Basic" is probably a transcription error for **Visual Studio Code** `(inferred)`; Sam needs to confirm which tool he uses.
- **2026-10-30, to be confirmed:** the name "Patrick" and the description "external product owner" come from the automatic summary; see "Gaps and open questions" in [company/agile-process-and-jira.md](../company/agile-process-and-jira.md).
- **2026-10-30, check against the documentation:** "Data Ingestion 2 pipeline" is not a name used in the existing files; Eduardo's own pipeline is documented as "DealerMine Test Edu" in [q-analytics/dev-ingestion-practice.md](../q-analytics/dev-ingestion-practice.md). It is not known which pipeline the note refers to.
