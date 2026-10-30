---
title: Quorum documentation guide
summary: Rules for organizing, formatting and maintaining this documentation. Read it before creating or changing any file.
updated_at: 2026-10-30
sources: [Eduardo's decision in the 2026-10-26 conversation, Eduardo's decisions of 2026-10-27 (glossary, "In plain words", never record secrets, focus on the essentials), structure expanded on 2026-10-29 (business, customers), single glossary by Eduardo's decision on 2026-10-29, move to the Git repository and translation to English by Eduardo's decision on 2026-10-30]
status: confirmed
---

# Quorum documentation guide

## 1. Purpose

This documentation maps Quorum as a company: products, business, customers, people, technology and, in particular, **Q Analytics**. It exists so that any agent, in any conversation, can get up to speed on a subject by reading a few files, and so that Eduardo (who is onboarding) has an organized memory of what he learns.

The documentation is **living**: the current structure is a first version and will change. Changing the structure is expected and allowed, following section 7.

## 2. Where the documentation lives

- The documentation lives **only** in the Git repository `quorumeduardogimenes/quorum-overview` (GitHub), cloned on Eduardo's computer at `C:\Users\edugm\Repositories\quorum-overview`. It is no longer stored in the Claude project (moved on 2026-10-30).
- Files live at the **repository root** (there is no `quorum/` folder). Older material may mention paths starting with `quorum/`; drop that prefix.
- Agents edit the files in the local folder. **Eduardo makes the commits and the push**; agents do not.
- Agents can create and overwrite files, but may not be able to delete, rename or move them. When that is needed, tell Eduardo exactly which files or folders he must remove by hand.

## 3. Principles

1. **Language:** all documentation is written in **English**. Eduardo communicates with Claude in **Portuguese**, and answers to him are always in Portuguese; only the files are in English. Raw meeting notes keep their original language when copied verbatim.
2. **One fact, one place.** Each piece of information has a single home. Other files point to it with a relative link instead of copying the text.
3. **Always cite the source.** Every file lists its sources (meeting, date, person who said it). Without a source, the fact must be marked as an inference.
4. **Separate fact from inference and from open questions.** A fact stated by someone: write it normally. An inference by whoever documented it: prefix it with `(inferred)`. What is still unknown goes into the "Gaps and open questions" section and is never filled in with assumptions.
5. **No empty files.** A file is created only when there is real content for it. The "planned" folders in section 4 do not exist until there is something to put in them.
6. **Small and focused.** One file covers one subject. If it grows beyond about 200 lines or mixes subjects, split it (section 8).
7. **Information about people:** record only what is relevant to the work (role, area, time zone, subjects they help with). No sensitive personal data.
8. **Understandable for someone who is learning.** Eduardo is learning technical concepts. Every technical file has a short **"In plain words"** section (an analogy, before the details), and **every new concept or term, technical or business, goes into the [glossary](glossary.md)**, which is a single document.
9. **Never record secrets.** Passwords, tokens, keys and server IP addresses do not go into the documentation or the notes, even if they appear in shared material (for example, sample passwords in a guide). Record only that they exist and through which channel they are delivered. This matters even more now that the documentation is in a Git repository.
10. **Focus on the essentials.** The goal is to understand the process end to end. Typos, variable names and small inconsistencies in materials are recorded only if they change the understanding; otherwise they are left out.

## 4. Structure

All paths are relative to the repository root. Current structure:

```
quorum-overview/
├── README.md                    # entry point of the repository
├── 00-documentation-guide.md    # this file: rules
├── 00-index.md                  # reading map + change log
├── glossary.md                  # single glossary: technical concepts and business terms (WO/RO, F&I, flat rate etc.)
├── company/
│   ├── overview.md              # what the public website and Sam say about the company (includes informal history)
│   ├── ways-of-working.md       # hours, time zones, meeting cadence, tools
│   └── culture-and-expectations.md # work culture as Sam sees it (scope, estimates, honesty)
├── business/
│   └── dealership-domain.md     # departments, inventories, revenue, customer journey, terms
├── customers/
│   └── dealerships.md           # how many, one server per customer, same schema, groups
├── people/
│   └── directory.md             # who is who
├── products/
│   ├── brands.md                # brands, products and status in Q Analytics
│   └── quorum-dms.md            # the DMS: accounting at the center, modules, Dealer database, test environment
├── q-analytics/
│   ├── overview.md              # what it is, who it is for, how it is accessed, internal use
│   ├── architecture.md          # end-to-end data flow, hosting, environments, Todd's deck
│   ├── ingestion-pipeline.md    # in-house ETL (clients, pipelines, jobs, destination servers)
│   ├── ingestion-agent.md       # end-to-end ingestion: agent/client, deployment, test environments, jobs, precautions, Word guide
│   ├── dev-ingestion-practice.md # log of Eduardo's practice on the dev ingestion tool: access, objects created, queries, next steps
│   ├── postgres-test-environment.md # test Postgres ("test db"): pgAdmin web, databases, Todd's guidance
│   ├── postgres-and-summaries.md # Postgres, replica, pg_cron, summary tables
│   ├── superset-and-security.md # Superset, RLS, Descope, who sees what, customizations, MCP
│   ├── powerbi-to-superset-migration.md # includes the Flex Reports > Power BI > Superset evolution
│   └── improvement-ideas.md     # Eduardo's informal backlog of ideas
├── meetings/
│   ├── 2026-10-26-rick-kickoff.md
│   ├── 2026-10-26-todd-technical-tour.md
│   ├── 2026-10-27-todd-ingestion-call.md
│   └── 2026-10-29-sam-business-rules.md
└── onboarding/
    ├── first-weeks.md           # schedule, actions and status of follow-up questions
    └── questions-for-todd.md    # the open questions for Todd (in English)
```

Planned folders and files, to be created only when there is content:

- `company/`: organization, relationship with Valsoft and leadership (from the website: Our Story, Our Team).
- `customers/`: files per segment or per important customer and how they consume reports (today only `dealerships.md`).
- `business/`: one file per subject when `dealership-domain.md` grows (for example, accounting or service rules).
- `products/<brand>.md`: one file per brand when there is architecture detail (today only Quorum DMS; DealerMine, Autovance and Accessible Accessories are missing).
- `q-analytics/`: report catalog.
- `technology/`: infrastructure beyond Q Analytics (AWS etc.), databases, repositories.
- `decisions/`: relevant technical and business decisions, one per file, when they come up.

## 5. Standard file format

Every file starts with this (YAML) header:

```
---
title: ...
summary: one sentence saying what the file is about
updated_at: YYYY-MM-DD
sources: [meeting X on YYYY-MM-DD, so-and-so said Y]
status: draft | confirmed | outdated
---
```

Recommended body: short context, "In plain words" (technical files), the subject's content, and at the end the **Gaps and open questions** section (a list of what still needs confirming and with whom). Links between files use paths relative to the file (e.g. `../q-analytics/overview.md`).

`status`: `draft` = from a single source or still incomplete; `confirmed` = validated by more than one source or by the owner; `outdated` = known to be superseded, awaiting review.

External sources (website, documents, images): cite the URL or origin and the date of access. Public information does not replace what people at the company said; when they diverge, record the divergence under "Gaps and open questions". Images, diagrams, presentations and attached documents (like Todd's deck and Word document) are not stored in the repository: transcribe or summarize their content as text in the subject file and say so in the file itself.

**Copies of living documents.** Some documents live outside the repository (for example, one of Eduardo's Claude Docs). When other agents need to see them, save a text copy here, make clear in the file that it is a snapshot, cite the link and the revision, and note that the original is the source.

**Glossary (`glossary.md`).** A single glossary, one entry per concept, in alphabetical order, covering technical and business terms. Each entry separates **General** (general-knowledge explanation that did not come from a meeting) from **At Quorum** (what someone said, with the source). Never mix the two in the same paragraph, so that a general explanation is not mistaken for a company fact.

## 6. Naming convention

- Lowercase English words separated by hyphens, no accents or spaces (`powerbi-to-superset-migration.md`).
- Meetings: `meetings/YYYY-MM-DD-subject-or-person.md`.
- Decisions: `decisions/YYYY-MM-DD-short-title.md`.

## 7. Workflow for processing a new meeting

1. Save the meeting note in `meetings/` with the raw text Eduardo provided, unedited. It is the primary source and **is not rewritten later**; corrections are only appended in a "Later corrections" section.
2. Extract the facts and **promote** them to the subject files (products, q-analytics, people etc.), updating existing files before creating new ones.
3. Add to `glossary.md` **all** new words and concepts that came up (technical and business).
4. In the meeting note, keep the "Where each fact was documented" table pointing to the files touched.
5. Record questions and pending items in the "Gaps and open questions" section of the corresponding subject file; actions and follow-up questions go to `onboarding/first-weeks.md` while onboarding lasts; improvement ideas go to `q-analytics/improvement-ideas.md`.
6. Update `00-index.md`: rows for new files and an entry in the change log.
7. Tell Eduardo, briefly and in Portuguese, what was created/changed and what remained as an open question, and list the files so he can commit them.

Proper names and acronyms in notes produced by automatic transcription (like Granola) may be wrong (examples already seen: DealerMind for DealerMine, dScope for Descope). When you receive supporting material (decks, diagrams, documents), check the names against it and record the correction under "Later corrections". If a meeting's date is not in the notes, record the assumed date as `(inferred)` and ask Eduardo to confirm.

In conversations that mix personal introductions with technical content (like the 2026-10-29 one with Sam), separate in the subject files: company facts, illustrative analogies and proportions (not measured data), and personal stories or opinions (the person's view, not company policy). The raw note keeps everything; the subject files only take what helps the work (principle 7).

## 8. How to change and evolve

**Changing an existing file**

1. Read the current version in the repository folder right before editing (Eduardo or another conversation may have changed it).
2. Rewrite the **whole** file.
3. Update `updated_at` and `sources`, and adjust `status` if needed.
4. Record the change in the change log of `00-index.md`.

**Conflicting sources:** the most recent source wins, but the old information does not disappear silently. Record the change in the file itself (e.g. "Before (2026-10-26): X. Updated on 2026-11-03 by Y: Z"). If the error is in a meeting note, do not rewrite the note: append it under "Later corrections". On **technical** questions, Todd is the reference (Eduardo, 2026-10-27); approximate numbers from other people yield to his.

**Evolving the structure**

- *Split:* when creating the new files, leave a link to them in the original and reduce it to what remains.
- *Merge:* move the content to the target file and remove the old one only after fixing all links.
- *Rename/move:* write to the new path, fix links and the index, and then ask Eduardo to remove the old path.
- *In any case:* update the tree in section 4 of this guide and `00-index.md`, and note the reason in the change log.
- Changes to **the rules** of this guide must be agreed with Eduardo first.

**Removing content:** deletion is irreversible (until it is committed, it cannot be recovered from Git either). Only delete files when Eduardo asks or as part of a rename/merge. Superseded information is marked `status: outdated` instead of being deleted.

## 9. For agents consuming the documentation

1. Always start with `00-index.md` and read only the files relevant to the task.
2. For Q Analytics tasks, the suggested order is `q-analytics/overview.md`, then the specific files (`architecture.md` first), then `products/brands.md` if the task involves a brand or product. To understand the business behind the reports, read `business/dealership-domain.md` and `products/quorum-dms.md`. For unknown terms, check `glossary.md`.
3. Treat files with `status: draft` as likely, not guaranteed, information, and check the "Gaps and open questions" section.
4. If you find something wrong or missing, tell Eduardo and propose the correction following section 8, instead of silently changing information you cannot validate.

## Gaps and open questions

- Whether the documentation should also be shared with the team (repository visibility, other contributors) has not been decided.
