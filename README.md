# ACCY 575: Data Analytics Applications in Accountancy

**Syllabus — Fall 2026**

| | |
|---|---|
| **Credit** | 4 hours |
| **Term dates** | Aug 24, 2026 – Dec 9, 2026 |
| **Section A** (CRN 74620) | TR 12:30 PM – 1:50 PM · 3001 Business Instructional Facility |
| **Section I** (CRN 79414) | TR 11:00 AM – 12:20 PM · 3001 Business Instructional Facility |
| **Instructor** | *TBD* |
| **Office hours** | *TBD* |
| **Prerequisite** | ACCY 570 |

---

## 1. Course Description

The course teaches you how to do real data analysis on real accounting and finance data the way it's done in 2026: with modern tooling, a coding agent at your side, and your own judgment as the load-bearing skill.

The semester is split into three parts:

- **Part 1 (Modules 1–7) — The Modern Python Project Workflow.** You learn to set up and run a professional-grade analytical project: VS Code, `uv`, Git/GitHub, project structure, secrets handling, data validation, and AI-assisted coding. Your deliverable is a reproducible GitHub repository, not a Jupyter notebook.
- **Part 2 (Modules 8–14) — Agent-Driven Data Analysis.** You pull a real research dataset from WRDS and analyze it with a coding agent doing the implementation. Each module covers one analytical method (OLS, gradient boosting, BERT, LLM zero-shot, retrieval-augmented generation, full agent loop) applied to the same dataset. The lecture is the **concept** — when each method applies, what it assumes, what its output means, how to evaluate it. The agent writes the code; you direct, review, and correct it.
- **Part 3 — Accounting Cases.** Apply Parts 1 and 2 to a sequence of accounting cases. *Content TBD; will be added once Parts 1–2 are stable.*

Modules 1–14 pace at roughly two per calendar week, fitting into the first eight weeks of the term. Part 3 occupies weeks 9–16.

---

## 2. Learning Objectives

By the end of this course you will be able to:

- Set up a reproducible Python analytical project from scratch using modern tooling.
- Use Git and GitHub to manage, share, and review analytical work in a team workflow.
- Pull a real research dataset from WRDS and prepare it for analysis.
- Direct a coding agent through an end-to-end analysis pipeline — and critically evaluate its output rather than accepting it blindly.
- Choose appropriately among modern analytical methods (linear, tree-based, encoder-LM, LLM-based) for a given accounting question, and explain what each method is doing and why.
- Validate analytical work for correctness and reproducibility — catch the failure mode where "code ran but the answer is wrong."
- Translate business questions in accounting contexts into rigorous analyses, and communicate the results in a form a manager would actually read.

---

## 3. Required Tools

- **VS Code** (free)
- **Python**, managed via **`uv`**
- **Git** and a **GitHub** account (free for students via [GitHub Education](https://education.github.com/))
- A coding agent: **GitHub Copilot** (free for students via GitHub Education), or an equivalent (Claude Code, Cursor)
- **WRDS** account (provided through the College of Business; setup in Module 8)

No prior VS Code or Git experience is required. Modules 1–4 cover both from scratch.

---

## 4. Course Schedule

### Part 1 — The Modern Python Project Workflow

| Module | Topic | Focus |
|------|-------|-------|
| 1 | Terminal/CLI fundamentals; VS Code setup; first Python project with `uv` | shell, VS Code, `uv` |
| 2 | Git fundamentals: commits, branches, GitHub workflow | Git, GitHub |
| 3 | Pull requests & code review; pair-review a classmate's PR | GitHub PRs |
| 4 | Project structure & `pyproject.toml`; secrets (`.env`, `.gitignore`); a real README | `uv`, `.env` |
| 5 | Data validation & reproducibility: asserts, types, seeds, snapshot tests, `pandera` | `pandera`, `pytest` basics |
| 6 | AI-assisted coding & debugging; AI permissions; prompt-injection awareness | GitHub Copilot, VS Code debugger, OWASP LLM Top 10 |
| 7 | **Mini-project:** reproducible analysis repo (individual) | Full Part 1 workflow |

### Part 2 — Agent-Driven Data Analysis

A single dataset, pulled from WRDS in Module 8, is reused across every subsequent Part 2 module. Each module teaches one analytical method *conceptually*; the agent does the implementation.

| Module | Topic | Focus |
|------|-------|-------|
| 8 | WRDS data acquisition; pull and validate the Part 2 dataset | WRDS, `wrds` Python client |
| 9 | OLS baseline: when linear regression is the right answer (and when it isn't) | OLS, diagnostics, baseline mindset |
| 10 | Gradient-boosted trees: modern tabular SOTA | XGBoost / LightGBM, feature importance |
| 11 | BERT / FinBERT: encoder transformers for financial text | Hugging Face, embeddings as features |
| 12 | LLM zero-shot extraction & classification | structured output, evaluation |
| 13 | Embeddings + retrieval-augmented generation | semantic search, RAG over filings |
| 14 | Agent end-to-end pipeline | Claude Code / agent SDKs running the full loop |

### Part 3 — Accounting Cases

*Content TBD. Cases will draw from internal audit, managerial accounting, financial communications, and advisory work. To be finalized once Parts 1–2 are stable.*

---

## 5. Assessment

### Part 1 — Mini-project (Module 7)

Graded against objective workflow criteria: does the repo run on a clean environment, is the structure clear, do the validation checks pass, is the results README clear and accurate?

### Part 2 — Per-module deliverables

Each Part 2 module produces a short write-up — what method, what question, what the agent built, what you changed, what the answer is — graded on:

| Criterion | Weight | What we are looking for |
|-----------|--------|--------------------------|
| Correctness and rigor of analysis | 40% | Is the question actually answered? Are conclusions supported by the data and the method? |
| Code quality and reproducibility | 25% | Does the repo run on a clean environment? Is the structure clear? |
| AI Collaboration Log quality | 25% | Does the log show genuine analytical judgment — not just acceptance of agent output? |
| Presentation / communication | 10% | Is the write-up clear and appropriately concise? |

### Part 3 — Cases

*TBD.*

### The AI Collaboration Log

Every Part 2 submission includes a one-page log structured as four columns:

| Brief | Agent Output | Your Edits | Why |
|-------|--------------|------------|-----|
| The spec you wrote — what you asked the agent to do, the data shape, the constraints | What the agent produced (paste or summarize concisely) | What you kept, changed, or rejected | The reasoning behind each change or rejection |

Writing a clear **brief** is itself the skill being evaluated. In professional work you give specs to AI tools the same way you give them to a junior staff or contractor — the quality of what you get back is determined upstream, by what you ask for and how you frame it.

This is not a compliance exercise. The log is the primary evidence of your analytical judgment. A submission whose log shows the student blindly accepted agent output will be graded accordingly, even if the underlying analysis happens to be correct.

---

## 6. AI Use Policy

AI tools (Claude, GitHub Copilot, ChatGPT, Cursor, and equivalents) are **permitted and expected** at every stage of work in this course. The skill we are evaluating is *judgment* — your ability to direct, evaluate, and correct AI output — not memorization of syntax.

This policy is consistent with the broader university policy on generative AI in coursework. If those policies update during the term, this section will be updated and announced.

---

## 7. Datasets

### Part 2 — WRDS

A single research dataset pulled from WRDS in Module 8 and reused across every Part 2 module. The exact tables (e.g., Compustat fundamentals paired with a textual companion source) are finalized by the instructor before the term begins.

### Part 3 — TBD

Case datasets will be added once Part 3 content is finalized.

---

## 8. Course Policies

- **Late submissions:** *TBD*
- **Attendance:** *TBD*
- **Academic integrity:** *TBD — note interaction with §6 AI policy*
- **Accommodations:** *TBD*

---

*This syllabus is subject to revision. Material changes will be announced and a dated version posted to the course repository.*
