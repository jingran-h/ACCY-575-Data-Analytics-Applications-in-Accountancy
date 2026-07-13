# Module 8 — Walkthrough: WRDS Data Acquisition

## 1. Stand up a new repo for Part 2

Part 1's `ACCY575-walkthrough` was workflow practice on small data. Part 2 is research data, multiple models, and a branch-per-module review workflow. Keeping them in separate repos avoids a tangled history and gives you a Part 2 repo that's clean enough to point a recruiter at.

Pick a name and create the local folder:

```bash
cd ~/Projects/accy575
uv init ACCY575-wrds-data-analysis
cd ACCY575-wrds-data-analysis
```

Mirror the Part 1 layout you set up in Module 4:

```bash
mkdir -p data/raw data/interim src notebooks tests
touch data/raw/.gitkeep src/__init__.py src/pull_fundamentals.py src/pull_mdna.py
touch .env
```

Update `.gitignore` (this is the same shape as Module 4 but with WRDS-specific lines added — copy the whole block):

```
# Python
__pycache__/
*.pyc
.venv/

# Secrets
.env
.env.*
.pgpass

# Data — WRDS is licensed, do not commit raw pulls (test fixtures under tests/ stay committable)
data/raw/
data/interim/
data/*.csv
data/*.parquet
data/*.xlsx

# OS
.DS_Store
Thumbs.db

# IDE
.vscode/settings.json
.idea/
```

Now create the GitHub repo and push:

```bash
git init
git add .
git commit -m "Initial Part 2 scaffold"
gh repo create ACCY575-wrds-data-analysis --public --source=. --remote=origin --push
```

> **`gh` not installed?** `brew install gh` on macOS, then `gh auth login`. Or create the repo through the GitHub UI and `git remote add origin …` manually. Either is fine.

You should now be able to open the repo on GitHub and see the empty scaffold. This is the `main` you'll branch off in every later module.

## 2. Enroll in the ACCY 575 WRDS class

Your access for this course comes through a **WRDS Education Classroom account** — a special type of WRDS account tied to a specific class, with a defined start and end date. It is *not* a personal researcher account, and it expires when the semester ends. Class accounts include the same things you need for this course: SSH to the WRDS Cloud, the JupyterHub and SAS Studio environments, disk storage, and the standard data libraries.

The instructor sets up the class on the WRDS side and gives you a **Class Code** (a short alphanumeric token) at the start of Module 8. You use that code to enroll. Without it you can't get in — registering as a generic individual on `wrds-www.wharton.upenn.edu` will not give you the access this course assumes.

*Optional further reading: [WRDS Student Guide: Enrolling in a Class Account](https://wrds-www.wharton.upenn.edu/pages/about/wrds-account-types/student-guide-enrolling-in-a-class-account/) — the canonical step-by-step for the enrollment flow below.*

### 2a. If you have no WRDS account yet

1. Go directly to the class-student registration form: <https://wrds-www.wharton.upenn.edu/register/?user_type=class-student>. The user-type radio button arrives pre-selected to "Class - Students with Code."
2. Enter your identifying information. For **Subscriber**, choose **University of Illinois at Urbana-Champaign** from the drop-down. Use your `@illinois.edu` email.
3. Paste the **Class Code** the instructor posted to the course site.
4. Click **Register for WRDS**.

### 2b. If you already have a WRDS account from prior coursework or research

1. Log into <https://wrds-www.wharton.upenn.edu>.
2. Top right: **Your Account → Your Account Info**.
3. Scroll to **Your Classes** and click **Enroll in a Class**.
4. Enter the Class Code, confirm "ACCY 575 — Fall 2026," and submit.

### 2c. Set up Duo two-factor authentication

WRDS requires two-factor authentication. Once your enrollment goes through, WRDS will walk you through linking Duo Mobile:

1. Install the **Duo Mobile** app on your phone (free, App Store / Play Store).
2. Follow the on-screen QR-code flow at the [Duo setup guide](https://wrds-www.wharton.upenn.edu/pages/about/log-in-to-wrds-using-two-factor-authentication/) WRDS surfaces during first login.
3. From now on every WRDS access channel — the website, SSH to the WRDS Cloud, and `wrds.Connection()` from Python — challenges you for Duo. The website and SSH accept either a Duo Push or a 6-digit passcode; **Postgres / `wrds.Connection()` accepts Push only.**
4. WRDS retains your MFA session for up to **30 days** as long as your IP address (and, on the web, your browser + cookies) doesn't change. So in practice you'll see the Duo prompt once per network, not once per login.

### 2d. Confirm you can log in

Within an hour of enrollment, log into the WRDS website and click into any data product (e.g., Compustat → Fundamentals Annual). If the query form loads, you're in. If it says you need a subscription, the enrollment isn't through yet — wait an hour, then email the instructor.

> **Heads up — account expiration.** Your class account expires at the end of the term. Anything you want to keep — pulled data, cached embeddings, code — has to live in your local Git repo, not on the WRDS Cloud. Plan accordingly: do not treat your `~/` on the WRDS server as a long-term home directory.

> **Heads up — academic, non-commercial use only.** WRDS data is licensed for **academic and non-commercial research**. That's the binding line. The clear-cut "no" cases: don't trade on it, don't run it as input to a personal investing strategy, don't hand it to an internship or employer for their analysis, don't bundle it into a commercial product. The clear-cut "yes": coursework, papers, theses, any non-commercial research output — with a WRDS acknowledgement when you publish.
>
> A separate rule that *is* immediate-termination: **never share your WRDS credentials with anyone.** Each student has their own account through the class enrollment in §2.
>
> One more nuance: the underlying data vendors (Compustat, CRSP, IBES, etc.) each have their own redistribution clauses on top of the WRDS umbrella, and some are stricter than others about reposting raw extracts. When in doubt — and especially before pushing raw vendor data to a public repo — check the specific vendor's terms or ask the instructor. For this course, §10 covers what's safe to commit and what isn't, and you can follow that without reading every vendor license.

## 3. SSH into the WRDS Cloud (one time)

**What is SSH?** Short for *Secure Shell*. You open an encrypted terminal session on a remote computer; you type commands locally and they execute on the server. It's how every cloud server and research compute cluster is reached — once you've done it once, the pattern carries everywhere. For the one-time look around below we'll authenticate with your WRDS password. SSH also supports keypair-based authentication, which is what you'd use for any sustained WRDS Cloud work — but you don't need that today.

The WRDS Cloud is a Linux compute environment Wharton hosts. For very large queries you want to run code there rather than pulling 50 GB to your laptop. We will mostly work locally in this course, but you should know what's on the other end of the wire — this is the literal "real research environment" your data is coming from.

### 3a. Connect

In your terminal:

```bash
ssh <your-wrds-username>@wrds-cloud.wharton.upenn.edu
```

`<your-wrds-username>` is the username WRDS assigned you when your enrollment in §2 was approved (visible in **Your Account → Your Account Info**). The hostname `wrds-cloud.wharton.upenn.edu` is the password-auth endpoint. (The separate `wrds-cloud-sshkey.wharton.upenn.edu` host is for SSH-key auth — you don't need it today.)

First connection asks you to confirm the host fingerprint — type `yes`. Then it prompts for your **WRDS password** (the one you set during enrollment).

### 3b. Pass the Duo prompt

After the password, you'll see a Duo two-factor menu in the terminal — not a popup, plain text:

```
Duo two-factor login for <your-username>

Choose an authentication method:
 1. Duo Push to your phone
 2. Phone passcode

Passcode or option (1-2):
```

Type `1` and press Enter. Approve the push on your phone. (If your IP doesn't change, WRDS keeps the MFA session valid for ~30 days, so subsequent SSHes from the same network skip this.)

You should land at a prompt that looks roughly like:

```
[your-username@wrds-cloud1 ~]$
```

You're now on a Wharton server. Look around briefly and leave:

```bash
pwd                  # /home/<group>/<your-username>  — WRDS assigns the group segment for your class
ls /wrds             # data libraries available — compustat, crsp, ibes, audit, ...
exit
```

That's the whole familiarization step. You've confirmed the account works and seen the shape of the filesystem on the other side. The next two sections set up the real remote dev workflow you'd use if you ever needed to *work* on the WRDS Cloud rather than just visit it.

> **Why bother, if we're mostly going to work locally?** Some queries are too big to pull — CRSP daily for 30 years is 50+ GB — and you'll move the analysis to the cloud when that happens. In other contexts (regulatory filings, controlled-data audits) the data is *not allowed* to leave the host environment, and SSH-into-the-data is the only legal workflow. Knowing the option exists changes how you think about Part 3 cases.

## 4. Connect VS Code to the WRDS Cloud

Working in a raw SSH terminal is fine for a quick visit. For anything beyond that you want a real editor pointed at the remote filesystem. VS Code's **Remote-SSH** extension does exactly this: it connects to the server over SSH, installs a small VS Code Server in your home directory there, and gives you a normal-looking editor window in which **the file tree, the terminal, and the Python interpreter all live on WRDS Cloud** — your laptop is just rendering the UI.

This is the same pattern Big Four data teams use to work on regulated datasets that aren't allowed to leave a hardened environment. Worth one setup.

*Optional further reading: [Remote Development using SSH](https://code.visualstudio.com/docs/remote/ssh) — Microsoft's official docs for the Remote-SSH extension, if you want the full feature set beyond what §4 uses.*

### 4a. Install the extension

Open VS Code locally → **Extensions** sidebar → search `Remote - SSH` → install Microsoft's official one (publisher: *Microsoft*, ID `ms-vscode-remote.remote-ssh`).

### 4b. Add the WRDS Cloud as a host

Open the Command Palette (`⇧⌘P` / `Ctrl+Shift+P`) → **Remote-SSH: Add New SSH Host…**. When VS Code asks for the connection, paste the same SSH command you used in §3:

```
ssh <your-wrds-username>@wrds-cloud.wharton.upenn.edu
```

VS Code asks which config file to write to — pick the default (`~/.ssh/config` in your home folder). The extension creates the entry for you; you never edit the file by hand.

### 4c. Connect

Command Palette → **Remote-SSH: Connect to Host…** → pick `wrds-cloud.wharton.upenn.edu` from the list. A new VS Code window opens. The first connection installs VS Code Server on the remote (~30 seconds) and asks for your WRDS password, then surfaces the same Duo text-menu prompt you saw in §3b — pick `1`, approve the push.

When the bottom-left corner shows a green badge reading **SSH: wrds-cloud.wharton.upenn.edu**, you're in. **File → Open Folder** → pick `/home/<group>/<your-username>` (or any subdirectory). The file explorer now shows the remote filesystem. **Terminal → New Terminal** opens a shell on WRDS Cloud — every command runs there.

Extensions you install in this remote window go onto WRDS Cloud's VS Code Server, not your local VS Code. Install the Python and Jupyter extensions on the remote when prompted — those are the ones you'll use here.

## 5. Connect GitHub from the WRDS Cloud

You now have a real editor on WRDS Cloud, but if you can't `git push` to GitHub from inside it, anything you build there is stuck on the server. **The fix is to do exactly what you did in Module 2 — `gh auth login` — but inside the remote VS Code terminal instead of a local one.**

That's the point of VS Code Remote-SSH. The screens, the prompts, the key presses are identical to what you'd type on your laptop. The bytes happen to land on a Wharton server instead of your machine, but you're not supposed to feel that. Your habits do not change.

### 5a. Open a terminal on the remote

In the VS Code window that's connected to WRDS Cloud (the one with the green **SSH: wrds-cloud.wharton.upenn.edu** badge in the bottom-left), open **Terminal → New Terminal**. The shell that opens is on WRDS Cloud — every command in this section runs there.

### 5b. Authenticate to GitHub

```bash
gh auth login
```

If `gh` isn't installed yet (`command not found`), install it into your home directory — you don't have root on the WRDS Cloud, so system package managers are out. Grab the current `gh_*_linux_amd64.tar.gz` from [GitHub CLI's releases page](https://github.com/cli/cli/releases/latest) and unpack it (substitute the current version number for `X.Y.Z`):

```bash
wget https://github.com/cli/cli/releases/download/vX.Y.Z/gh_X.Y.Z_linux_amd64.tar.gz
tar xzf gh_X.Y.Z_linux_amd64.tar.gz
mkdir -p ~/bin && cp gh_X.Y.Z_linux_amd64/bin/gh ~/bin/
export PATH="$HOME/bin:$PATH"     # add this line to your ~/.bashrc too
```

Then re-run `gh auth login`. (If you happen to have a user-space conda such as Miniconda in your home directory, `conda install -c conda-forge gh -y` also works.)

Pick **GitHub.com → HTTPS → Login with a web browser**. Copy the one-time code, press Enter, the browser opens *on your laptop* and you authorize. This is the exact flow from [Module 2](../module-02-git-fundamentals/walkthrough.md) — same screens, same code-paste step.

### 5c. Tell Git who you are (per-server)

```bash
git config --global user.name  "Your Name"
git config --global user.email "your-netid@illinois.edu"
```

The only thing that doesn't carry over from your laptop is this `git config` — it's per-server, so you set it once on each machine you push from.

### 5d. Clone your Part 2 repo

```bash
mkdir -p ~/Projects && cd ~/Projects
gh repo clone <your-github-username>/ACCY575-wrds-data-analysis
cd ACCY575-wrds-data-analysis
```

`git push` from this WRDS Cloud terminal now lands in the same GitHub repo your laptop pushes to. You can develop in either place; GitHub is the meeting point.

> **What just happened.** You repeated the Module 2 flow on a different machine. Same UX, same end result. Under the hood the mechanism is different — your laptop is rendering the editor while WRDS Cloud is doing the work — but that's a detail of the implementation, not of your experience. This is the headline benefit of Remote-SSH: a hardened or licensed environment that you operate the same way you operate your laptop.

## 6. Configure local Python access (`.pgpass`)

For the rest of this course we work locally. The `wrds` Python package connects to WRDS over the Postgres protocol. The clean way to authenticate is a `~/.pgpass` file — credentials saved with OS-level permissions, never in your repo.

The first call to `wrds.Connection()` will prompt for username and password and offer to save a `.pgpass` for you. Accept. Or set it up manually:

```bash
echo "wrds-pgdata.wharton.upenn.edu:9737:wrds:<your-username>:<your-password>" > ~/.pgpass
chmod 600 ~/.pgpass
```

The `chmod 600` is required — Postgres refuses to read a `.pgpass` that's world-readable. Confirm:

```bash
ls -l ~/.pgpass
# -rw-------  1 you  staff  ...
```

> **Why not put credentials in `.env`?** `.env` is fine if you read it into Python explicitly. `.pgpass` is the Postgres-native mechanism — the `wrds` client picks it up automatically with no code change, and it's a system-wide credential rather than a per-project one. Both are acceptable; `.pgpass` is the WRDS-recommended path.

*Optional further reading: [Using Python on the WRDS Platform](https://wrds-www.wharton.upenn.edu/pages/grid-items/using-python-wrds-platform/) — WRDS's own reference for the `wrds` client and `.pgpass` setup.*

## 7. Install the Python tooling

Back in your `ACCY575-wrds-data-analysis` project:

```bash
uv add wrds pandas pyarrow pandera
uv add --dev pytest ipykernel jupyterlab
```

(`ipykernel` is what VS Code needs to run notebook cells; `jupyterlab` adds the standalone `jupyter lab` command that Modules 9–11 use to open notebooks from the terminal. Without it, `uv run jupyter lab` fails with `Error executing Jupyter command 'lab'`.)

Quick sanity check:

```bash
uv run python -c "import wrds; db = wrds.Connection(); print(db.list_libraries()[:3]); db.close()"
```

What you should see on a fresh `.pgpass`'d setup, the first time you run this from a new network:

1. The command appears to hang for a few seconds — *that's the Postgres server waiting on Duo.*
2. A **Duo Push** lands on your phone. Approve it. (Postgres connections don't accept passcode entry — Push is the only second-factor option here.)
3. Back in the terminal, three library names print and the script exits.

After that, WRDS keeps your MFA session warm for ~30 days from the same IP — re-runs from the same network return instantly with no Duo prompt. From a different network (a coffee shop, a VPN switch), the Duo Push step comes back.

> **Aside — `python -c "…"`.** The `-c` flag tells Python to run the string that follows as a complete program and exit, no file or REPL involved. It's the fastest way to execute one or two lines of Python without opening an editor or `python` shell — useful for sanity checks like this, for grabbing a quick value out of a Parquet file (you'll see this pattern again in §8a), or for running anything you'd otherwise type into iPython once and never again. Statements in the string are separated by `;` since `-c` is one line; for anything multi-line, put the code in a `.py` file instead.

## 8. Build the Part 2 dataset

Open your coding agent (Copilot Chat, Claude Code, Cursor — whichever you're using). Write briefs the way Module 6 trained you to: a clear spec gets you a clean result; a vague prompt gets you a confidently wrong one.

The pull has **two parts that live in different places**:

- **Compustat fundamentals.** Sit in WRDS Postgres. Reachable from your laptop over `.pgpass`. Pull this locally.
- **MD&A text from 10-K filings.** WRDS Postgres only stores the *index* (`wrdssec_all.wrds_forms`) — the actual filing files live on the WRDS Cloud's local filesystem under `/wrds/sec/warchives/…` (with parsed/cleaned copies under `/wrds/sec/wrds_clean_filings/…`), accessible only from a process *running on the WRDS Cloud*. **You'll do this part in your remote VS Code session from §4.**

This is where §4 (Remote-SSH) and §5 (`gh auth login` on the cloud) stop being demo-only setup and become required. If you skipped them, go back.

### 8a. Compustat fundamentals — *local*

In your local `ACCY575-wrds-data-analysis` repo, brief the agent:

> **Brief.** Write `src/pull_fundamentals.py`. Connect to WRDS using `wrds.Connection()`, pull annual Compustat fundamentals (`comp.funda` table) for S&P 500 firms over 2010–2024, save to `data/raw/fundamentals.parquet`.
>
> Variables to pull: `gvkey`, `datadate`, `fyear`, `tic`, `conm`, `at` (assets), `lt` (liabilities), `revt` (revenue), `ni` (net income), `oancf` (operating cash flow), `xrd` (R&D), `sich` (industry).
>
> Compustat filters: `indfmt='INDL'`, `datafmt='STD'`, `popsrc='D'`, `consol='C'`, `fyear` between 2010 and 2024 inclusive.
>
> S&P 500 membership comes from **CRSP, not Compustat** — S&P Dow Jones revoked Compustat's license to redistribute index constituents in mid-2020, and the S&P index history was pulled from `comp.idxcst_his` (depending on your data vintage it is missing outright or frozen at mid-2020). Use `crsp_a_indexes.dsp500list_v2` (or the legacy `dsp500list`), join to Compustat through `crsp.ccmxpf_lnkhist` on `permno → gvkey` with the standard `linktype in ('LU','LC')` and `linkprim in ('P','C')` filters. Apply the membership window with the columns that match the table you chose: `mbrstartdt <= datadate <= mbrenddt` for `dsp500list_v2`, or `start <= datadate <= ending` for the legacy `dsp500list`.
>
> Print the row count at the end. Don't commit the parquet file.

What this brief gets right that a sloppy prompt wouldn't:

- The four standard Compustat filter flags (`INDL`/`STD`/`D`/`C`). Without them you get duplicates from the supplemental and restated formats.
- **CRSP-not-Compustat for S&P 500 membership.** Most tutorials online still tell you to use `comp.idxcst_his`. They predate the 2020 licensing change and will silently give you a broken panel — the S&P membership rows are gone or frozen at mid-2020, depending on the data vintage. This is exactly the leakage-class mistake §8 is supposed to teach you to catch.
- The link-history join (`ccmxpf_lnkhist`) with `linktype`/`linkprim` filters. Naive joins on `permno` or `cusip` produce duplicate-firm and history-misalignment bugs.
- "Don't commit the parquet" — the agent will sometimes try to `git add` the data file. Stop it.

Read the file the agent produces. Make sure it does what the brief said and nothing else. Then run it:

```bash
uv run python -m src.pull_fundamentals
```

Expected: a Duo Push on first run (§7), then 1–3 minutes of querying, then a row count and a written `data/raw/fundamentals.parquet`. Inspect:

```bash
uv run python -c "import pandas as pd; print(pd.read_parquet('data/raw/fundamentals.parquet').head())"
uv run python -c "import pandas as pd; print(pd.read_parquet('data/raw/fundamentals.parquet').shape)"
```

Sanity checks worth running by hand:

- Row count plausibility: ~500 firms × ~15 years ≈ 7,000–8,000 rows (less, because of constituent changes).
- Year coverage: `df['fyear'].value_counts().sort_index()` — should be roughly even across 2010–2024.
- No duplicate `(gvkey, fyear)` pairs.

If any of these look wrong, that's a brief problem, not a data problem — go back to the brief and fix the spec.

### 8b. MD&A text — *on the WRDS Cloud*

This is the more involved half of the pull, and three later modules depend on it:

- **Module 11 (BERT / FinBERT)** encodes each MD&A paragraph into a 768-dim vector and adds those embeddings as features in the M9/M10 models — the question being whether what management *says* adds signal beyond the numbers.
- **Module 12 (LLM zero-shot)** classifies and extracts structured fields from MD&A: risk-factor categorization, forward-looking-statement detection, segment disclosures.
- **Module 13 (RAG)** indexes the MD&A chunks and lets you query the corpus with natural language ("which firms discussed supply chain in 2022?").

Why MD&A and not the full 10-K or earnings-call transcripts: it's a standardized location (Item 7) the parser can find reliably, it's where management writes in their own words rather than reciting GAAP, and it joins cleanly to Compustat on `(gvkey, fyear)` so the text and fundamentals signals are directly comparable.

Open the VS Code Remote-SSH window connected to WRDS Cloud (§4) and clone (or `cd` into) your repo there (§5). In that remote terminal, brief the agent:

> **Brief.** Write `src/pull_mdna.py`, intended to run **on the WRDS Cloud** (not locally). Two stages.
>
> **Stage 1 — find the right 10-K filings.** Connect via `wrds.Connection()`. Use the same S&P 500 / 2010–2024 panel as `src/pull_fundamentals.py` — re-derive `(gvkey, fyear)` from CRSP + `ccmxpf_lnkhist` so this script is independent. Map each `gvkey` to its historical `cik` via `wrdssec_all.wciklink_gvkey` (this is the WRDS-maintained linking table; pick the link active for each firm-year, not just the most recent). Then query `wrdssec_all.wrds_forms` for `form='10-K'` filings whose filing date falls in fiscal year `fyear` (or shortly after — 10-Ks typically file 60–90 days after fiscal year-end, so allow `fyear+1` Q1). Each row of the result has a `wrdsfname` column naming the filing's file — resolve it against the archive root, i.e. `/wrds/sec/warchives/{wrdsfname}` for the raw filing (cleaned versions live under `/wrds/sec/wrds_clean_filings/`).
>
> **Stage 2 — read the file and extract Item 7 (MD&A).** For each row, open `wrdsfname` from the local filesystem, read the contents, and extract the "Item 7" section (Management's Discussion and Analysis). Use a 10-K-aware parser — `sec-parser` from PyPI is the cleanest path; regex on `r"item\s*7[.\s]"` … `r"item\s*7a[.\s]"` is the fallback. If extraction fails for a filing, log the `gvkey/fyear` and skip — don't fabricate text.
>
> Output to `data/raw/mdna.parquet` with columns `(gvkey, fyear, cik, fdate, mdna_text, char_count)`. Print row counts and the count of failed extractions. Don't commit the parquet.
>
> **Parallelize Stage 2.** Per-filing parsing takes 1–2 seconds; doing 7,000 of them sequentially is hours. Use `multiprocessing.Pool` with **4–6 workers** (the WRDS Cloud login node is shared — don't claim 32 cores) to bring total runtime under an hour. Make sure the per-filing function is picklable: read the path, return the parsed text, no shared state.

> **What is `multiprocessing`?** Python's standard library for true parallel execution. By default, Python's Global Interpreter Lock (GIL) prevents threads from running Python bytecode in parallel — `threading` is fine for IO-bound work but doesn't speed up CPU-bound work like parsing. **`multiprocessing` sidesteps the GIL by spawning separate Python processes**, each with its own interpreter, on different CPU cores. The `Pool` API distributes a list of inputs across N workers: you give it a function and an iterable, it runs them in parallel and returns the results. Each input is *pickled* (serialized) to ship to a worker and the result is pickled back, so the function and its arguments have to be picklable — top-level functions and plain values work; closures and lambdas don't. Use it when (a) the work is CPU-bound (this is) and (b) you have cores to use (login nodes typically have 8–32, but be polite — 4–6 is appropriate on a shared resource).

What this brief gets right:

- **Where it runs.** The `wrdsfname` paths only resolve on WRDS Cloud's filesystem; from your laptop, even with valid `.pgpass`, the file reads would fail. This is *the* reason §4/§5 exist.
- **Historical CIK linking.** Companies change CIKs (mergers, restructurings). `wciklink_gvkey` keeps history; using a current-only mapping produces silent gaps for firms that switched.
- **Filing-date window.** 10-K filing date is usually in early `fyear+1`, not in `fyear`. Naively filtering by `fdate ∈ fyear` drops most of your panel.
- **Skip-and-log on parse failure.** A few percent of 10-Ks have non-standard structure that the parser will choke on. Better to log them than to ship a Parquet with hallucinated text in a few rows.

Read the file the agent produces. Then run it — this is the long step, plan for it.

**Add the parsing dependency.** `sec-parser` isn't in the existing lockfile yet:

```bash
uv add sec-parser
```

> `sec-parser`'s last PyPI release is `0.58.1` (June 2024) and the project's GitHub README now states it is no longer maintained — it still works, so pin the version and keep the regex fallback from the brief ready in case a filing trips it up.

*Optional further reading: [`sec-parser`](https://pypi.org/project/sec-parser/) — Alphanome.AI's 10-K-aware parser, the package doing the Item 7 extraction in Stage 2.*

(If `uv` isn't available on the WRDS Cloud, `pip install --user uv` first — then make sure `~/.local/bin` is on your `PATH`.)

**Wrap the run in `tmux`.** A 30+ minute job over SSH dies the moment your laptop sleeps, your Wi-Fi switches, or you close the VS Code window. `tmux` is a terminal multiplexer that keeps your shell session alive on the server even when your client disconnects:

```bash
tmux new -s mdna     # start a named session called "mdna"
# (now you're inside tmux — your prompt changed slightly)
uv run python -m src.pull_mdna
```

If you get disconnected (close the laptop, lose Wi-Fi, anything), the script keeps running. To reattach next time you SSH back in: `tmux attach -t mdna`. To leave the session running and detach voluntarily: press `Ctrl+b` then `d`.

**Realistic runtime:**

| Setup | Time for ~7,000 filings |
|---|---|
| Sequential (no `multiprocessing`) | 2–3 hours |
| 4-worker pool | 35–55 min |
| 6-worker pool | 25–40 min |

If the brief was followed correctly, you should be in the second or third row. Approve the Duo Push on first Postgres connection, watch the progress for the first few hundred filings to make sure the rate is sensible (**3–4 filings/sec with 6 workers** after the first couple of minutes — the very start is slower while workers warm up), then `Ctrl+b d` out of `tmux` and let it cook. Come back in 30–60 minutes.

**Keeping an eye on a shared server.** The WRDS Cloud login node is one machine shared by the whole class, with memory and disk quotas — the most common way a big pull dies is silently exhausting one of them. A few commands tell you what's happening on the box: `df -h` shows free disk, `free -h` shows free memory, and `top` (or `htop`, if it's installed) gives a live per-process view so you can spot a job about to be killed. When you want to know *why* something is slow, prefix it with `time` — `time uv run python -m src.pull_mdna` reports **real** (wall-clock) time separately from **user + sys** (CPU) time. If wall-clock dwarfs CPU time, the job is waiting on the network or disk; if CPU time is the bottleneck, that's exactly what the `multiprocessing.Pool` above buys you.

*Optional further reading: [`memory_profiler`](https://pypi.org/project/memory-profiler/) — line-by-line RAM profiling for when a Compustat or text pull blows past the server's memory limit.*

Sanity checks once it finishes:

- Row count should be close to (but slightly less than) §8a's row count. Some firms file 10-K-equivalents (10-KSB, 10-K405) instead, and a small fraction of extractions will fail.
- Failed-extraction count should be under ~5% of the panel. Higher means the parser is broken — fix and re-run.
- Spot-check three random rows by printing `mdna_text[:500]` — does it look like real MD&A prose?

If any of these look wrong, fix the brief above and re-run.

**Push the script back to GitHub** from the cloud:

```bash
git add src/pull_mdna.py
git commit -m "M8: MD&A pull from wrds_forms"
git push
```

The Parquet file (`data/raw/mdna.parquet`) stays on the WRDS Cloud — it's gitignored. **What you push back is the script**, so a teammate or a grader on their own WRDS Cloud session can re-run it.

**Copy the Parquet down to your laptop.** For the rest of Part 2 your laptop is the primary work environment, so once `mdna.parquet` is built on the WRDS Cloud you'll copy *that one file* down. (Re-running `src/pull_mdna.py` locally is *not* a fallback — the raw filings the script reads from `/wrds/sec/warchives/` only exist on the WRDS Cloud's filesystem, and pulling them in bulk runs into hundreds of GB of transfer plus vendor licensing.) Expect the parsed Parquet to be **roughly 500 MB to 1 GB** depending on how aggressively the parser strips boilerplate. It's a one-time download.

> **What is `scp`?** Short for *Secure Copy* — it's `cp` (the Unix file-copy command) but where one side is a remote server reached over SSH instead of another folder on your local machine. The syntax is `scp <source> <destination>`, and either path can be remote, written as `user@host:/absolute/or/~/relative/path`. Authentication, encryption, and the Duo step are all the same as in §3 — it's the same SSH connection, just used to move bytes instead of running commands. (For very large transfers, `rsync` over SSH is the heavier-duty version; for a one-shot file like this, `scp` is fine.)

```bash
# from your laptop, after the cloud script has produced mdna.parquet there:
scp <your-wrds-username>@wrds-cloud.wharton.upenn.edu:~/Projects/ACCY575-wrds-data-analysis/data/raw/mdna.parquet ./data/raw/
```

This `scp` is the only place a raw parquet crosses the boundary from server to laptop. After it lands, both Parquets sit in your local `data/raw/` and Modules 9–14 can join them on `(gvkey, fyear)`.

## 9. Validate with `pandera`

Same pattern as Module 5. Add `src/schema.py` with one schema per Parquet:

```python
import pandera.pandas as pa
from pandera.typing import Series

class FundamentalsSchema(pa.DataFrameModel):
    gvkey: Series[str]
    datadate: Series[pa.DateTime]
    fyear: Series[int] = pa.Field(ge=2010, le=2024)
    tic: Series[str]
    at: Series[float] = pa.Field(ge=0, nullable=True)
    lt: Series[float] = pa.Field(ge=0, nullable=True)
    revt: Series[float] = pa.Field(nullable=True)
    ni: Series[float] = pa.Field(nullable=True)

    class Config:
        strict = "filter"
        coerce = True
        unique = ["gvkey", "fyear"]

class MdnaSchema(pa.DataFrameModel):
    gvkey: Series[str]
    fyear: Series[int] = pa.Field(ge=2010, le=2024)
    cik: Series[str]
    fdate: Series[pa.DateTime]
    mdna_text: Series[str] = pa.Field(str_length={"min_value": 200})
    char_count: Series[int] = pa.Field(ge=200)

    class Config:
        strict = "filter"
        coerce = True
        unique = ["gvkey", "fyear"]
```

`coerce = True` matters here: WRDS's Postgres driver hands numeric columns back as `float64` (a nullable `fyear` comes across as `2010.0`), and a Parquet round-trip can shift dtypes again. Coercion casts each column to the declared type as part of validation — and still fails loudly on real problems, like an `fyear` that's missing or fractional.

Wire each schema into the pull script that produces it (`pull_fundamentals.py` validates against `FundamentalsSchema` *before* writing; `pull_mdna.py` validates against `MdnaSchema` *before* writing). Re-run; if validation fails, you have a real problem to look at — don't paper over it by relaxing the schema.

The `min_value=200` on `mdna_text` is deliberate: an extraction that returns 50 characters is almost certainly the parser misfiring, not a real MD&A. The schema catches it before the bad row makes it downstream.

## 10. Decide what gets committed

WRDS itself permits academic, non-commercial use. The catch is the **underlying data vendors** — Compustat, CRSP, IBES, Audit Analytics — each have their own redistribution clauses on top of the WRDS umbrella, and several restrict reposting raw extracts even for academic users. Reading every vendor license to figure out what's allowed is more work than just keeping raw pulls off public repos.

So the default for this course is: **don't commit raw vendor data**. Partly that's a licensing call — some vendors are stricter than the WRDS umbrella, and default-private spares you reading each license. But the bigger reason is that the data file isn't worth committing: anyone reproducing your work — a grader, a teammate, future-you — has their own WRDS access and can re-run the pull from your script in a minute. A frozen Parquet snapshot is *less* flexible than the script that generated it.

What you commit:

- `src/pull_fundamentals.py` and `src/pull_mdna.py` — the queries (one local, one cloud-only).
- `src/schema.py` — the validation.
- A small **fixture** file for tests: `tests/fixtures/fundamentals_sample.parquet`, ~50 rows of synthetic-or-anonymized data for `pytest` to chew on.
- `README.md` describing how to reproduce.

What you do **not** commit:

- `data/raw/*.parquet` — already in `.gitignore`.
- `.pgpass` — already in `.gitignore`. Double-check.
- WRDS username, password, or any URL with credentials baked in.

Verify:

```bash
git status                   # nothing in data/raw/ should appear
git log --all -- '*.pgpass'  # should print nothing
```

## 11. Commit and push

You've now made changes in **two working trees** — your laptop and your VS Code Remote-SSH session on the WRDS Cloud. Each is its own Git checkout with its own staged files. Both push to the same GitHub remote, which is the rendezvous point. Do the commit/push on **both**.

### On your laptop

```bash
git add src/ tests/ pyproject.toml uv.lock README.md .gitignore
git status                   # eyeball one more time before committing
git commit -m "M8: fundamentals pull + validation schemas"
git push
```

This is the laptop side: `pull_fundamentals.py`, the `pandera` schemas in `src/schema.py`, the local lockfile changes from §7's `uv add wrds pandas pyarrow pandera`.

### On the WRDS Cloud

In your remote VS Code terminal:

```bash
git pull                     # bring down the schema you just pushed from laptop
# (wire MdnaSchema into pull_mdna.py — quick edit; re-run §8b if you want validation
#  on a fresh write, otherwise the existing parquet is still good)
git add src/pull_mdna.py pyproject.toml uv.lock
git status
git commit -m "M8: MD&A pull script + sec-parser dep"
git push
```

This commits the cloud-side delta — `pull_mdna.py` itself (already pushed earlier in §8b if you followed along, but any post-validation tweaks land here) and the lockfile change from `uv add sec-parser` in §8b. **Don't forget the lockfile** — without it, a teammate cloning the repo and trying to re-run the cloud script gets `ModuleNotFoundError: sec_parser` and has no way to know why.

### After both are pushed

```bash
# back on your laptop:
git pull
```

Now `main` on your laptop, your cloud session, and GitHub are all in sync. This is the `main` that Modules 9–14 will each branch from — and **work on `main` directly is rare from here on out**: every model gets its own branch.

### Letting the agent do this

You can also just tell your coding agent **"push all"** or **"commit and push the schema work"** — most agents (Claude Code, Cursor in agent mode, Copilot agent) will run `git status`, draft a commit message, stage, commit, push, and report back. For routine work, this is genuinely faster than typing the four lines above.

It's not a free pass, though. Two things you watch for, every time:

- **The staged file list, before you approve the commit.** Most agents pause and show what's staged; some quietly run `git add -A` and sweep in *everything*, including a forgotten `data/raw/foo.parquet`, a `.env`, or a notebook with credentials in cell output. A leaked WRDS password or API key in a `git push origin main` to a public repo is compromised within minutes — there's no "untoast the bread" command for it.
- **The commit message, before you approve it.** Vague messages ("update files", "fix stuff") aren't useful to a teammate, future-you, or a grader. If the agent's draft is bad, send it back — same brief-and-review loop as Module 6.

For Part 2 work going forward, **"push all" is fine on a feature branch** (low blast radius — anything wrong gets caught at the PR review in §6 of each subsequent module). For pushes to `main`, slow down. The blast radius is the public repo and your reputation as someone whose history doesn't contain accidents.

## You're done if…

- [ ] `https://github.com/<your-username>/ACCY575-wrds-data-analysis` exists, public, with both `src/pull_fundamentals.py` and `src/pull_mdna.py` visible.
- [ ] You SSHed into WRDS Cloud at least once from the terminal and saw `/wrds`.
- [ ] You connected VS Code Remote-SSH to WRDS Cloud and opened a folder there at least once.
- [ ] You ran `gh auth login` inside the remote VS Code terminal and `git clone` of your Part 2 repo works from the WRDS Cloud.
- [ ] `uv run python -m src.pull_fundamentals` produces `data/raw/fundamentals.parquet` locally.
- [ ] `uv run python -m src.pull_mdna` produces `data/raw/mdna.parquet` *on the WRDS Cloud*; you've `scp`'d it back to your local `data/raw/`.
- [ ] Both `pandera` schemas pass on their respective files.
- [ ] No raw WRDS data, no `.pgpass`, no credentials of any kind appear in `git log`.
- [ ] Your repo's `main` is the foundation Module 9 will branch off.
