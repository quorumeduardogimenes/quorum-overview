---
title: Governance and security practices (current state)
summary: What Sam said about shared accounts, names shown to customers, the absence of written rules on secrets and repositories, and the SOC 2 goal.
updated_at: 2026-10-30
sources: [conversation with Sam, 2026-10-30 (../meetings/2026-10-30-sam-jira-and-agile-process.md; Granola summary)]
status: draft
---

# Governance and security practices (current state)

A snapshot of **2026-10-30** from a single source (Sam, Granola summary). It is Sam's view, not a security audit. Superset security (RLS, Descope, who sees what) is in [../q-analytics/superset-and-security.md](../q-analytics/superset-and-security.md); the process side is in [agile-process-and-jira.md](agile-process-and-jira.md).

## In plain words

A company handles sensitive keys and shared logins. "Governance" is simply having clear, written answers to: who may use what, where secrets are kept, and how we would notice a mistake. At Quorum those answers mostly live in people's heads for now, and SOC 2 (an external security standard, see the [glossary](../glossary.md)) is a goal that will require writing them down.

## What Sam described

| Topic | What was said |
|---|---|
| Shared Power BI service account | One shared account is in use. It can be **audited by IP**, but it is a **single kill-switch risk**: if it is blocked or compromised, everything that depends on it stops. |
| Names shown in Superset | Superset **used to show real employee names** (Sam, Todd) as the creators of reports, **to customers**. They are now **masked as "Quorum Analytics"**. Sam sees this as a symptom of a broader governance gap. |
| Rules on secrets and repositories | There is **no firm stance and no written rules** on secrets, credentials or repository hygiene. |
| SOC 2 | **SOC 2 compliance is a goal.** **Scanning repositories for secrets and credentials is not yet in place.** |
| Sam's suggestion | An **automated Claude skill** that scans the repositories for exposed secrets, as a **baseline**. |
| Maintenance | **READMEs and `.gitignore` files** need ongoing maintenance. |
| Credentials in AI tooling | The **MCP connector credentials are stored outside Claude** (channel not stated; see [agile-process-and-jira.md](agile-process-and-jira.md)). |

## Relation to this documentation

This documentation repository follows its own rule: **never record secrets** (passwords, tokens, keys, server IP addresses), see principle 9 of the [documentation guide](../documentation-guide.md). The rule applies to this repository only and is not a Quorum policy.

## Improvement suggestions

Sam's suggestion of an automated secret-scanning skill (SEC-1, stated by Sam) and the suggestion of written rules with an owner (SEC-2, `(inferred)` by the documenting agent) are in the global [improvement-ideas.md](../improvement-ideas.md), section "Security and governance suggestions".

## Gaps and open questions

- Which Power BI service account is it, and is it still in use after the Superset migration?
- Is the "masked as Quorum Analytics" setting applied to every dashboard, and where is it configured?
- Who owns the written rules for secrets and repository hygiene, and what is the SOC 2 timeline?
- Is the secret-scanning idea already planned, or is it only Sam's suggestion?
- Where are the MCP connector credentials stored?
