# Module 1 — Walkthrough: Terminal, VS Code, and Your First `uv` Project

## 1. Open a terminal

- **macOS:** open the Terminal app (`Cmd+Space` → type "Terminal"). The default shell is `zsh`.
- **Windows:** install [Windows Terminal](https://aka.ms/terminal) from the Microsoft Store and use PowerShell.

The four commands you will use most:

```bash
pwd          # print working directory — where am I?
ls           # list files here
cd Desktop   # go into a directory
cd ..        # go up one level
```

Try each one. Get muscle memory.

*Optional further reading: [The Missing Semester — "The Shell"](https://missing.csail.mit.edu/2020/course-shell/) (MIT) — a 30-minute primer that goes well beyond these four commands for anyone who'll live in a terminal.*

## 2. Install VS Code

Download from [code.visualstudio.com](https://code.visualstudio.com/) and install. Open it once.

Add two extensions (left sidebar → Extensions):

- **Python** — by Microsoft.
- **Even Better TOML** — you'll be reading `pyproject.toml` files all semester.

## 3. Install `uv`

First, a definition. A **Python package** is a reusable bundle of code somebody else wrote and published — `pandas` for data manipulation, `pytest` for testing, `numpy` for math, etc. Almost no real Python project is just code you wrote; you stand on top of dozens of packages pulled from a public registry called [PyPI](https://pypi.org). A *package manager* is the tool that downloads them, resolves dependencies (packages depend on other packages, which depend on others...), and puts everything in the right place.

`uv` is a fast, modern Python project manager built by Astral. One small binary replaces a tangled stack of older tools (`pip`, `venv`, `pip-tools`, `pyenv`, `conda`, `poetry`). We use it for four reasons:

- **Speed.** Written in Rust. Installing dependencies is 10–100× faster than `pip` or `conda` — fast enough that you stop being annoyed at it.
- **Reproducibility.** `uv.lock` records exact versions, so anyone who clones your project ends up with the same environment.
- **Standards-based.** Uses `pyproject.toml` (the Python community standard). Your project stays compatible with everyone else's tooling instead of being locked into one ecosystem.
- **Simpler.** One tool, one mental model. No more "should I use `pip` or `conda` or `pyenv`?" for any given task.

*Optional further viewing: [uv: An Extremely Fast Python Package Manager](https://youtu.be/gSKTfG1GXYQ) — Charlie Marsh, the creator of `uv`, on why it exists and how it works (~25 min).*

### How `uv`, `venv`, Python, conda, and VS Code fit together

A quick mental model so the cast of characters doesn't feel arbitrary:

- **Python** is the language interpreter — the thing that actually runs your code.
- **`venv`** is a *cordoned-off folder* containing one Python interpreter and one set of installed packages, isolated from the rest of your machine. One project = one `venv`. It's built into Python; nothing fancy.
- **`uv`** is what *creates and manages* `venv`s and the packages inside them. It also installs Python itself when you need a version you don't have. When you `uv init` a project, it scaffolds the structure; when you `uv add pandas`, it auto-creates `.venv/` (if missing) and drops pandas in.
- **`conda`** is an older alternative to `uv` from the scientific-Python world. Same job, but uses its own package ecosystem and is much slower. Its share of the Python world has been shrinking; a lot of companies have moved off it.
- **VS Code** is just your editor. It auto-detects the `.venv` in your project and uses that Python interpreter when running, debugging, or opening notebooks. It doesn't care who *made* the `.venv` — `uv`, `conda`, or `pip`.

### Why one `venv` per project?

Different projects need different versions of the same package, and the versions fight. Project A might need `pandas 1.5`; project B uses a feature that only shipped in `pandas 2.2`. If you install pandas once, globally, you can have one or the other — not both. The same applies to Python itself: legacy code might require Python 3.10, while newer code wants 3.13.

A `venv` per project sidesteps the whole mess. Each project's `.venv/` holds exactly the versions it needs, pinned by `uv.lock`. Two practical bonuses fall out of this: (1) deleting a project means deleting its folder, `.venv` and all — no leftover packages cluttering your machine; (2) a collaborator can clone your repo, run `uv sync`, and get the *same* working environment in one command — no "what version of pandas do you have?" emails.

### Install it

In your terminal:

```bash
# macOS / Linux
curl -LsSf https://astral.sh/uv/install.sh | sh

# Windows (PowerShell)
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

Verify:

```bash
uv --version
```

If you see a version number, you're set. If `command not found`, restart the terminal and try again.

*Optional further reading: [uv — Getting Started](https://docs.astral.sh/uv/getting-started/) (Astral) — the official docs; handy as a reference once you're using `uv` daily.*

## 4. Pick where your code will live

Decide *now* where on your computer you keep code, and stick with it. Scattering projects across Desktop, Downloads, and Documents will cost you a lot of pain later.

**If you already have a folder where you keep coursework or other course-related files, use that** — no need to invent a new one. Otherwise, common choices: `~/Projects/`, `~/Code/`, `~/Documents/code/`. Pick one and be consistent.

```bash
mkdir -p ~/Projects        # use the path you picked
cd ~/Projects
```

For the rest of these walkthroughs, `~/Projects` means "wherever *you* decided to keep code." Substitute your own path everywhere.

## 5. Create your course project

A folder-organization habit worth picking up early: keep one **umbrella folder** for the entire course, and put each Git-tracked project inside it as its own subfolder. This walkthrough's project is the first one — you'll add more later, each its own GitHub repo.

Make the umbrella first:

```bash
cd ~/Projects               # your code folder from Step 4
mkdir -p accy575            # umbrella folder for all ACCY 575 work
cd accy575
```

Now create the project for Part 1's work *inside* the umbrella. In Module 2 you'll connect this project to GitHub as its own repository, so the name you pick now is the name you'll see in your GitHub repo list for years. Pick something descriptive: a name like `ACCY575-walkthrough` tells you what the project is at a glance; a generic `coursework` or `walkthrough` won't. Pick anything that's **not** `accy575` (we just used that for the umbrella) and stay consistent.

```bash
uv init ACCY575-walkthrough
cd ACCY575-walkthrough
ls
```

You should see `pyproject.toml`, `main.py`, `.python-version`, `README.md`. Open it in VS Code:

```bash
code .
```

If `code .` doesn't work (the `code` CLI isn't always on your PATH by default), open VS Code from your Applications / Start menu and use *File → Open Folder…* to pick the `ACCY575-walkthrough` folder.

You'll use **this same project** for all of Part 1 (Modules 1–7). Don't make a new one each module — you'll lose the benefit of compound progress.

## 6. Run Python via `uv`

Open VS Code's integrated terminal (`` Ctrl+` `` — that's Control + backtick, same shortcut on Windows and macOS). If the shortcut doesn't work, use the menu: *View → Terminal*. Then:

```bash
uv run main.py
```

You should see `Hello from ACCY575-walkthrough!`.

While that ran, `uv` quietly created a `.venv/` directory in your project — that's where the project's Python interpreter and its packages live (the "virtual environment").

## 7. Add a dependency

```bash
uv add pandas
```

Three things happened:
- `pyproject.toml` gained a `dependencies = [...]` line.
- A `uv.lock` file appeared. Commit `uv.lock` once you start using Git next module — it pins exact versions.
- `pandas` got installed inside `.venv/`. (Still don't commit `.venv/` — `uv.lock` is the canonical "what's installed" record.)

## 8. Try a Jupyter notebook

For exploratory data work — pandas, charts, ad-hoc analysis — notebooks are the right tool. (For real, shippable code, scripts in `src/` are; we'll cover that distinction throughout the course.)

Add the Jupyter kernel as a **dev dependency** (`--dev` means: needed to *develop* the project, not to *run* it — Jupyter belongs to you the developer, not to your shipped code):

```bash
uv add --dev ipykernel
```

In VS Code, install the **Jupyter** extension (Extensions → search "Jupyter" → install the one from Microsoft).

Create a new file `explore.ipynb` in your project root (File → New File → save as `explore.ipynb`). VS Code opens it as a notebook.

In the first cell, paste:

```python
import pandas as pd

df = pd.DataFrame({"a": [1, 2, 3], "b": [4, 5, 6]})
df
```

(In a notebook, the last expression of a cell auto-renders — no `print()` needed.)

Press `Shift+Enter` to run. The first time, VS Code asks which kernel to use — pick the one for your `ACCY575-walkthrough` `.venv` (it should be auto-detected).

You should see a small DataFrame rendered as an HTML table beneath the cell.

## 9. Use the debugger (on a script)

Notebooks have their own debugging, but the **VS Code script debugger** is the workhorse you'll use for real work. Try it now while the project is small.

Edit `main.py` to give it a couple of variables worth inspecting:

```python
def main():
    name = "ACCY575-walkthrough"
    greeting = f"Hello from {name}!"
    print(greeting)


if __name__ == "__main__":
    main()
```

Click in the gutter to the left of `print(greeting)` to set a breakpoint (red dot).

Press `F5` (or Run menu → Start Debugging). Choose "Python File" if prompted.

Execution pauses at your breakpoint. In the **Variables** panel on the left, expand **Locals** to see `name` and `greeting`.

Far more powerful than `print()` debugging — you can inspect any variable at any execution point without changing your code.

*Optional further reading: [VS Code — Python Tutorial](https://code.visualstudio.com/docs/python/python-tutorial) (Microsoft) — goes deeper into IntelliSense, the debugger, and other Python-specific editor features.*

## 10. Read a stack trace

Errors will happen. Reading them well is a skill.

Add a new cell to `explore.ipynb`:

```python
df["c"]  # column "c" doesn't exist
```

Run it. You'll get a traceback. Read it **bottom-up** — the last line (`KeyError: 'c'`) is the actual error; the lines above tell you the call chain that got you there.

The same rule applies to script errors: bottom is *what went wrong*, top is *where it started*.

## You're done if…

- [ ] `uv --version` works in a fresh terminal.
- [ ] `uv run main.py` works in your project.
- [ ] You can run a Jupyter notebook cell with `Shift+Enter` and see a pandas DataFrame.
- [ ] You can set a breakpoint in `main.py` and inspect a variable in VS Code's debugger.
- [ ] You can identify the file, line, and error type from a stack trace.
