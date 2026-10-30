---
title: Work culture and expectations (Sam's view)
summary: How Sam described the way of dealing with scope changes, estimates, mistakes and stakeholders; it is one person's view, not a formal policy.
updated_at: 2026-10-29
sources: [conversation with Sam, 2026-10-29 (../meetings/2026-10-29-sam-business-rules.md)]
status: draft
---

# Work culture and expectations (Sam's view)

Warning: this is what **Sam** said, in a conversation in which they were also getting to know each other. No formal policy was cited; treat it as the practice of the data and reporting team.

## Scope and estimates

- **Scope change (scope creep):** handled case by case, without a rigid formal process.
- Preference: **document the change and update the story points in Jira**, but "sort it out" without bureaucracy.
- Principle: **transparency above emotion**. Give honest estimates and **own the revisions**.
- **Expectations:** estimates will sometimes be wrong, too high or too low. **Communicating early** is the rule.

## People

- Treat **all stakeholders as customers**, regardless of title ("from the janitor to the CEO").
- **Honesty after a mistake** is what keeps people employed. Sam told of a mistake of his own on a live server (a bulk change to a system control table, fixed with a backup) to illustrate this.
- "**It's who you are, not what you know how to do**": character and soft skills weigh more than technical knowledge.

## Practical precaution drawn from the story `(inferred)`

On a live server, bulk changes to system control tables are dangerous; have a backup and check before changing. (Eduardo works in test environments; see [onboarding/first-weeks.md](../onboarding/first-weeks.md).)

## Gaps and open questions

- Is there any formal scope change or revision process (Michael, Rick) that applies to the reports epic?
- Who approves estimate revisions?
