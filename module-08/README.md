# Module 8 — WRDS Data Acquisition

> **Hands-on:** [walkthrough.md](walkthrough.md) — new Part 2 repo, SSH into WRDS, VS Code Remote-SSH + GitHub on the WRDS Cloud, agent-directed data pull.

## Motivation

Real accounting and finance research runs on Wharton Research Data Services (WRDS): Compustat, CRSP, IBES, Audit Analytics, the SEC textual datasets. Industry analytics teams and PhD programs alike pull from there. If you want to do their work, you have to know how to pull from there too.

This is the only logistical module in Part 2. The six modules that follow (Modules 9–14) all use the dataset you build here, on the same repo, with each model getting its own branch and review pass. Get this module right once and the rest of Part 2 is about methods and judgment, not data wrangling.

## Goals

- Stand up a **new** GitHub repository for Part 2 — separate from your Part 1 `ACCY575-walkthrough`. Mirror the layout you already know from Module 4.
- Enroll in the ACCY 575 **WRDS Education Classroom** account using the Class Code the instructor provides, and complete Duo two-factor setup.
- **SSH into the WRDS Cloud** at least once. Knowing what's on the other end is part of WRDS literacy.
- Hook **VS Code Remote-SSH** to the WRDS Cloud so you have a real editor pointed at the server, then run the same `gh auth login` from inside that remote VS Code session that you ran on your laptop in Module 2. You won't use this every module, but the setup itself is the lesson — and the point is that the user-facing flow does not change.
- Configure local Python access via `~/.pgpass` so the `wrds` client connects without prompting and without ever putting credentials in your code.
- Brief a coding agent to write the pull. Read what it produced. Run it. Validate the result with `pandera` (Module 5 muscle).
- Make a defensible call about what gets committed: code and schema, yes; raw WRDS data, usually no.
- Understand the **academic / non-commercial use** restriction on WRDS data: no trading, no employer or commercial use, never share credentials. Acknowledge WRDS when you publish.

## Going Deeper *(optional)*

*The lecture covers what you need. These are here if you want to dig further.*

- [WRDS — Student Guide: Enrolling in a Class Account](https://wrds-www.wharton.upenn.edu/pages/about/wrds-account-types/student-guide-enrolling-in-a-class-account/) — official enrollment instructions; the canonical source for §2 of the walkthrough.
- [WRDS — Python on the WRDS Cloud](https://wrds-www.wharton.upenn.edu/pages/grid-items/python-wrds-cloud/) — official client documentation, including `.pgpass` setup.
- [WRDS Cloud — SSH access](https://wrds-www.wharton.upenn.edu/pages/grid-items/setting-wrds-cloud-ssh-access/) — if you want to run heavy queries server-side instead of pulling them down.
- [SEC EDGAR Full-Text Search](https://efts.sec.gov/LATEST/search-index?) — for filing text outside WRDS proper.

## Materials

*Slides, in-class exercises, and recordings posted here as the module approaches.*
