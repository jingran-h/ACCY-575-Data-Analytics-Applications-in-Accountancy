# Module 8 — WRDS Data Acquisition

> **Hands-on:** [walkthrough.md](walkthrough.md) — setting up a new Part 2 repo, enrolling in the WRDS class, hooking VS Code Remote-SSH to the cloud, and running an agent-directed two-source pull.

## Motivation

Real accounting and finance research runs on Wharton Research Data Services (WRDS): Compustat, CRSP, IBES, Audit Analytics, the SEC textual datasets. Industry analytics teams and PhD programs alike pull from there. If you want to do their work, you have to know how to pull from there too.

This is the only logistical module in Part 2. The six modules that follow (M9–14) all use the panel you build here — Compustat firm-year **fundamentals** joined with **10-K MD&A text** on `(gvkey, fyear)` — with each method getting its own branch and review pass. Get this module right once and the rest of Part 2 is about methods and judgment, not data wrangling.

## Goals

- Stand up a new GitHub repository for Part 2, separate from your Part 1 work.
- Enroll in the ACCY 575 WRDS Education Classroom account and complete Duo two-factor setup.
- SSH into the WRDS Cloud, then connect VS Code Remote-SSH and run `gh auth login` from inside that remote session.
- Configure local Python access to WRDS via `~/.pgpass`.
- Brief an agent to write two pulls — Compustat fundamentals locally, 10-K MD&A text on the cloud — and validate both with `pandera`.
- Commit and push from both environments and `scp` `mdna.parquet` down to your laptop.
- Apply the academic / non-commercial use rules: code can be public, raw vendor data cannot.

## Going Deeper *(optional)*

*The lecture covers what you need. These are here if you want to dig further.*

- [Student Guide: Enrolling in a Class Account](https://wrds-www.wharton.upenn.edu/pages/about/wrds-account-types/student-guide-enrolling-in-a-class-account/) — WRDS. Canonical source for §2's enrollment flow.
- [Python on the WRDS Cloud](https://wrds-www.wharton.upenn.edu/pages/grid-items/python-wrds-cloud/) — WRDS. Reference for the `wrds` client and `.pgpass` setup.
- [Remote Development using SSH](https://code.visualstudio.com/docs/remote/ssh) — Microsoft. Official docs for the VS Code Remote-SSH extension used in §4.
- [`sec-parser`](https://pypi.org/project/sec-parser/) — Alphanome.AI. The 10-K-aware parser used for Item 7 extraction in §8b.

## Materials

*Slides, in-class exercises, and recordings posted here as the module approaches.*
