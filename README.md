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

The course is built around real datasets and real business problems. Cases are drawn from internal audit, managerial accounting, database analysis, financial communications, advisory work, and a capstone partnership with PwC.

The semester is split into two halves:

- **Part 1 (Weeks 1–7) — The Modern Python Project Workflow.** You learn to set up and run a professional-grade analytical project: VS Code, `uv`, Git/GitHub, project structure, secrets handling, data validation, and AI-assisted coding. Your deliverable is a reproducible GitHub repository, not a Jupyter notebook.
- **Part 2 (Weeks 8–16) — AI-Assisted Case Work.** You apply that workflow to a sequence of accounting cases. The technical path is open: you decide which methods and libraries to use, and AI tools (Claude, GitHub Copilot, ChatGPT) are permitted at every stage. Each submission includes a one-page AI Collaboration Log documenting how you used and evaluated AI output.

---

## 2. Learning Objectives

By the end of this course you will be able to:

- Set up a reproducible Python analytical project from scratch using modern tooling.
- Use Git and GitHub to manage, share, and review analytical work in a team workflow.
- Use AI coding assistants like GitHub Copilot effectively — and critically evaluate their output rather than accepting it blindly.
- Validate analytical work for correctness and reproducibility — catch the failure mode where "code ran but the answer is wrong."
- Translate business questions in accounting contexts into rigorous analyses, and communicate the results in a form a manager would actually read.

---

## 3. Required Tools

- **VS Code** (free)
- **Python**, managed via **`uv`**
- **Git** and a **GitHub** account (free for students via [GitHub Education](https://education.github.com/))
- An AI coding assistant: **GitHub Copilot** (free for students via GitHub Education)

No prior VS Code or Git experience is required. Weeks 1–4 cover both from scratch.

---

## 4. Course Schedule

### Part 1 — The Modern Python Project Workflow

| Week | Topic | Focus |
|------|-------|-------|
| 1 | Terminal/CLI fundamentals; VS Code setup; first Python project with `uv` | shell, VS Code, `uv` |
| 2 | Git fundamentals: commits, branches, GitHub workflow | Git, GitHub |
| 3 | Pull requests & code review; pair-review a classmate's PR | GitHub PRs |
| 4 | Project structure & `pyproject.toml`; secrets (`.env`, `.gitignore`); a real README | `uv`, `.env` |
| 5 | Data validation & reproducibility: asserts, types, seeds, snapshot tests, `pandera` | `pandera`, `pytest` basics |
| 6 | AI-assisted coding & debugging; prompt-injection awareness; Markdown `results.md` | GitHub Copilot, VS Code debugger, OWASP LLM Top 10 |
| 7 | **Mini-project:** reproducible analysis repo (individual) | Full workflow |

### Part 2 — AI-Assisted Case Work

| Week | Topic | Focus |
|------|-------|-------|
| 8–9 | **PCard** — procurement card transactions | Internal Audit |
| 10 | **Differential Analyses** | Managerial Accounting |
| 11 | **Databases** — SQL + Python | Data Engineering |
| 12 | **Conference Calls** — Traditional dictionary vs. LLM comparison | Financial Communication |
| 13–14 | **Yellow Cab** — visualization & business advisory | Advisory |
| 15–16 | **PwC Capstone** — group project & presentations | Industry Partnership |

---

## 5. Assessment

### Part 1 — Mini-project (Week 7)

Graded against objective workflow criteria: does the repo run on a clean environment, is the structure clear, do the validation checks pass, is the results README clear and accurate?

### Part 2 — Cases

| Criterion | Weight | What we are looking for |
|-----------|--------|--------------------------|
| Correctness and rigor of analysis | 40% | Is the business question actually answered? Are conclusions supported by the data? |
| Code quality and reproducibility | 25% | Does the repo run on a clean environment? Is the structure clear? |
| AI Collaboration Log quality | 25% | Does the log show genuine analytical judgment — not just acceptance of AI output? |
| Presentation / communication | 10% | Is the write-up or presentation clear and appropriately concise? |

### The AI Collaboration Log

Every Part 2 submission includes a one-page log structured as four columns:

| Brief | AI Output | Your Edits | Why |
|-------|-----------|------------|-----|
| The spec you wrote — what you asked the AI to do, the data shape, the constraints | What the AI produced (paste or summarize concisely) | What you kept, changed, or rejected | The reasoning behind each change or rejection |

Writing a clear **brief** is itself the skill being evaluated. In professional work you will give specs to AI tools the same way you give them to a junior staff or contractor — the quality of what you get back is determined upstream, by what you ask for and how you frame it.

This is not a compliance exercise. The log is the primary evidence of your analytical judgment. A submission whose log shows the student blindly accepted AI output will be graded accordingly, even if the underlying analysis happens to be correct.

---

## 6. AI Use Policy

AI tools (Claude, GitHub Copilot, ChatGPT, and equivalents) are **permitted and expected** at every stage of work in this course. The skill we are evaluating is *judgment* — your ability to direct, evaluate, and correct AI output — not memorization of syntax.

This policy is consistent with the broader university policy on generative AI in coursework. If those policies update during the term, this section will be updated and announced.

---

## 7. Cases & Datasets

The Part 2 cases use the following real-world datasets:

- **PCard** — Internal audit on procurement card transactions
- **Differential Analyses** — Managerial accounting decisions
- **Databases** — SQL and Python integration on relational data
- **Conference Calls** — Earnings call transcript analysis (Loughran-McDonald dictionary vs. LLM API, side-by-side)
- **Yellow Cab** — Advisory and data visualization
- **PwC Capstone** — Group project run in partnership with PwC

---

## 8. Course Policies

- **Late submissions:** *TBD*
- **Attendance:** *TBD*
- **Academic integrity:** *TBD — note interaction with §6 AI policy*
- **Accommodations:** *TBD*

---

*This syllabus is subject to revision. Material changes will be announced and a dated version posted to the course repository.*
