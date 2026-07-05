# Module 17 — Internal Audit: Disbursement & Vendor-Payment Fraud-Risk Analytics

> **The engagement:** [brief.md](brief.md) — a real fraud-risk audit mandate on real state-disbursement data. How you deliver it is yours to decide.

## Motivation

Part 3 is not another set of methods to learn. It is four real engagements where you apply everything from Parts 1 and 2 the way you will at work: someone hands you a mandate and a dataset, and you are expected to scope the problem, choose your own tools, direct the agent, and defend the result. **No one writes your agent prompts for you here.** Deciding what to ask the agent — and knowing when its confident answer is wrong — is the job now.

This first engagement is an internal-audit classic: sift a year of one agency's vendor payments for genuine fraud-risk exceptions, and separate them from the statistical noise a naive analysis drowns in. The money is real public spending. The judgment — which anomalies are actually audit-relevant — *is* the deliverable.

## Goals

By the end of this engagement you will have:

- Scoped a fraud-risk audit from a two-line mandate and a raw payments file — choosing which analytics apply and which don't, without a prescribed recipe.
- Directed a coding agent to build the analysis: writing your own briefs, reading its diffs, and correcting what it gets wrong.
- Separated genuine audit exceptions from statistical artifacts of how public money is administered.
- Produced an internal-audit findings memo a Chief Audit Executive could take to an audit committee, backed by a reproducible repository you can defend line by line.

## What you deliver

An internal-audit findings memo / workpaper to the CAE and the reproducible repo behind it. Full spec in the [engagement brief](brief.md); assessment in [Assessment across Part 3](../README.md#assessment-across-part-3).

## Going Deeper *(optional)*

*The launch briefing frames the engagement and the professional standards it is judged against. Nothing below is required — these are references you may consult the way you would on the job.*

- **ACFE Occupational Fraud "fraud tree"** — Association of Certified Fraud Examiners. The standard taxonomy of fraud schemes; the *fraudulent disbursements* branch (billing, check tampering, expense schemes) is your map of what to look for.
- **AU-C 240 (AICPA) / PCAOB AS 2401**, *Consideration of Fraud in a Financial Statement Audit* — the external auditor's fraud standard your internal work feeds. Context for the professional lineage, not a manual.
- **Benford's Law** (Nigrini, *Benford's Law: Applications for Forensic Accounting*) — the distributional test everyone reaches for first and misapplies second. Its conditions matter more than its formula.

## Materials

*Your agency assignment, the shared deliverable rubric, and office-hours details posted here as the module approaches.*
