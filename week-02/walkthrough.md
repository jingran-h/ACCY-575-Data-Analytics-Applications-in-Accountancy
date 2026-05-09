# Week 2 — Walkthrough: Git Fundamentals

## Before you begin: create your GitHub account

Open a tab and do this **now**.

1. **Required:** Have a GitHub account. If you already have one, use it — no need to create a new one. Otherwise, sign up at [github.com](https://github.com); any email is fine.
2. **Optional:** Apply for [GitHub Education](https://education.github.com/) — verification unlocks **GitHub Copilot Pro for free** as your AI coding assistant for the rest of the course. (You'll need an `@illinois.edu` email on your account for student verification — add it under *Settings → Emails* if it's not there yet.) Approval can take up to 48 hours, so start early if you want it.

   *Skip step 2 if you already have a paid LLM subscription* you plan to use as your coding agent (Claude Pro, ChatGPT Plus, Cursor Pro, Claude Code, etc.). The course doesn't care which agent you use; we'll cover setup options in Week 6.

Then come back and continue. None of this blocks Steps 1–5 below.

## 1. Install and configure Git (one time only)

First, check whether Git is already installed:

```bash
git --version
```

If you see a version number (e.g. `git version 2.45.0`), skip ahead to the configuration block below.

If you get `command not found` (or similar):

- **macOS:** the system will prompt you to install the **Command Line Tools** — click *Install* and wait a few minutes. (Alternative: `brew install git` if you have Homebrew.)
- **Windows:** download and run the installer from [git-scm.com/download/win](https://git-scm.com/download/win). Default options are fine. Restart your terminal after installing.

Once `git --version` works, configure your identity. Git uses this to attribute every commit to you:

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"   # use the email on your GitHub account
git config --global init.defaultBranch main
```

Verify:

```bash
git config --global --list
```

## 2. Initialize a repo in your course project

```bash
cd ~/Projects/accy575/ACCY575-walkthrough
git status              # error: not a git repo
git init
git status              # now: lots of untracked files
```

The default `.gitignore` from `uv init` already excludes `__pycache__/` and `.venv/`. (In Week 4, when we introduce `.env` for secrets, you'll see why this list matters — once a real API key lands in Git history, it's compromised forever, even if you delete it later.)

## 3. First commit

A **commit** is a saved snapshot of your project at one moment in time. Like `Ctrl+S` for the whole project — except each commit also records *who* made it, *when*, *what* changed (the diff from the previous commit), and a *message* explaining *why*. Each commit gets a unique hash (a long hex string) so you can refer back to it later.

> **Word analogy:** a commit is like saving a version of a Word document, except you never need to name anything `report_v3_FINAL_actually_final.docx`. Git tracks every save automatically, attaches a written reason to each, and lets you see exactly what changed between any two saves.

Your project's "history" is just a chain of commits. `git log` lists them.

```bash
git add .
git status              # everything now staged
git commit -m "initial commit: project scaffold"
git log
```

Read the log carefully. Notice the **commit hash** (long hex), the author, the date, and the message — every commit has all four.

## 4. Make a meaningful change and commit it

Edit `main.py` (anything — change the print message, add a comment).

```bash
git status              # see "modified: main.py"
git diff                # see what changed, line by line
git add main.py
git commit -m "tweak hello message"
git log --oneline       # compact view
```

### Why the message matters

The **commit message** is the *why*. Six months from now, someone (probably you) will be reading `git log` trying to remember what changed in this commit and why. A good message answers that in three seconds. A bad message (`fix`, `wip`, `update code`) tells you nothing — you'll end up reading the diff and re-deriving the reasoning yourself.

**Convention**: subject line ~50 characters, present-tense imperative ("Add summary stats" — not "Added" or "Adds"). Optional blank line + body for a longer explanation.

| Bad | Good |
|---|---|
| `fix` | `Fix off-by-one in monthly aggregation` |
| `update code` | `Switch to month-end timestamps to allow date arithmetic` |
| `wip` | `Add stratified sampling helper — not yet wired into pipeline` |

The single rule: a teammate (or future-you) skimming `git log --oneline` should understand the project's evolution at a glance.

## 5. Branch and merge

A **branch** is a movable pointer to a commit — nothing more. When you create a branch, no files are copied; you've just made a second pointer to the same commit. As you commit on the new branch, *its* pointer advances while `main` stays put. The two branches "diverge."

```
A — B — C            ← main
         \
          D — E      ← experiment-1
```

This is why branches are cheap: they're pointers, not file copies.

**Why bother**: try things without breaking working code; let multiple people work in parallel; review work as a unit (Week 3). Real example for this course: you want to test a different aggregation on PCard data — branch off, experiment, then merge back if it works or throw the branch away if it doesn't.

> **Word doc analogy:** the "approved version" lives in `main`. A branch is "let me try rewriting Section 3 without messing up the approved doc." If the rewrite works, merge it in. If not, delete the branch — main is untouched.

**Merging** brings a branch's changes into another (usually back into `main`):

```bash
git checkout -b experiment-1
# edit something in main.py
git add main.py
git commit -m "try a different greeting"
git checkout main                # back to main — your edit is "gone" here
git merge experiment-1           # bring experiment-1's commit in
git branch -d experiment-1       # clean up; the branch pointer is no longer needed
```

Notice: while you were on `experiment-1`, your edits were visible. The moment you `git checkout main`, the file reverts to main's version. That's what "isolation" means in practice — `main` was never touched by your experiment until you explicitly merged.

### Merge conflicts

If both branches changed the *same lines* of the *same file*, Git can't auto-merge. It pauses and asks you to resolve. You'll see markers like these in the file:

```
<<<<<<< HEAD
df = df.groupby("month").sum()
=======
df = df.groupby("month_end").sum()
>>>>>>> experiment-1
```

Edit the file to keep the version you want (and delete the `<<<`, `===`, `>>>` marker lines), then `git add <file>` and `git commit`. You'll hit one of these eventually — they're routine, not an emergency.

## 6. Connect your local repo to GitHub

You should already have a GitHub account from the prelude above. If GitHub Education is still pending verification, that's fine — basic push/pull works immediately; Copilot Pro just won't be active yet.

Authenticate with the GitHub CLI (one time, much simpler than setting up SSH keys):

```bash
# Install gh first if you don't have it
# macOS:    brew install gh
# Windows:  winget install --id GitHub.cli

gh auth login
```

Follow the prompts: pick **GitHub.com** → **HTTPS** → **Login with a web browser**. Copy the one-time code shown in your terminal, press Enter, the browser opens, authorize. Done. Credentials are now cached locally — `git push` won't ask for a password again.

> *Why not SSH?* SSH keys also work and have advanced uses (commit signing, server-to-server automation, multi-platform key reuse). But the setup is `ssh-keygen` → `ssh-add` → copy the public key → paste into GitHub, and the failure modes are intimidating. `gh auth login` is the modern equivalent. If you outgrow it later, you'll know.

Then create a new repo on GitHub:

- Click **+ → New repository**.
- Name: `ACCY575-walkthrough`. Set to **Private**.
- Do **not** check "initialize with README" — your local repo already has files.

GitHub shows you commands. Run them locally:

```bash
git remote add origin https://github.com/<your-username>/ACCY575-walkthrough.git
git branch -M main
git push -u origin main
```

## 7. Make a change, push it

```bash
echo "## Notes" >> README.md
git add README.md
git commit -m "add notes section to readme"
git push
```

Refresh the GitHub repository page. Your latest commit should appear at the top, with the message you wrote.

## 8. Use VS Code's Source Control panel (optional shortcut)

Now that you know what each Git command actually does, here's a productivity shortcut: VS Code's **Source Control panel** gives you a GUI for almost everything you just did from the terminal.

Open it with `Ctrl+Shift+G` (or click the **branched-line icon** in the left sidebar).

What you can do without leaving VS Code:

- **See modified files** at a glance, with a badge on the icon when you have changes.
- **Stage** specific files — or specific lines — via the inline `+` button; **unstage** with `−`.
- **Commit** by typing a message in the box at the top and pressing `Cmd/Ctrl+Enter`.
- **Push, pull, fetch** from the `…` menu at the top of the panel.
- **Switch branches** from the bottom-left corner of the VS Code window.
- **View the diff** for any file by clicking it in the changed-files list.

Most developers use the GUI for routine ops (commit, push, branch switch) and drop to the CLI for the harder things (rebase, complex merges, recovery). Either is fine; the point is knowing what's happening underneath — which the CLI exercises above teach you.

## You're done if…

- [ ] You have at least 5 commits with meaningful messages.
- [ ] You can use `git checkout -b` to make a branch and `git merge` to merge it back.
- [ ] Your project is on GitHub (private) and the latest commit shows there.
- [ ] You can explain (in one sentence) why we'll add `.env` to `.gitignore` in Week 4.
