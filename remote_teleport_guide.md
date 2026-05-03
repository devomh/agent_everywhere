# Remote-Teleport Workflow Guide

A step-by-step guide to running Claude Code tasks in Anthropic's cloud and pulling
the results back to your local machine.

---

## Prerequisites

- Claude Code CLI installed (`claude --version`)
- Active **Pro, Max, Team, or Enterprise** subscription on `claude.ai`
- CLI authenticated: `claude login` (use your claude.ai account, not a bare API key)
- Repository pushed to GitHub and connected to claude.ai (or use bundle mode — see §3)

---

## Step 1 — Configure the cloud environment

Cloud sessions start from a clean VM. If your project needs extra tooling (LaTeX,
jupytext, custom packages), provide a **setup script** that runs once before Claude
launches. The filesystem is cached after the first run so subsequent sessions are fast.

### How to add a setup script

1. Open `claude.ai/code` in your browser.
2. Navigate to **Settings → Environments → (your environment) → Setup script**.
3. Paste the script from your project file (e.g. from `project_latex_pdf.md` or
   `project_jupytext_notebook.md`).
4. Save. The script runs with root access on the next session start.

### Environment variables

Add any secrets or config values in **Settings → Environments → Environment variables**.
These are available as shell variables in every cloud session.

### Network access

The default **Trusted** level allows outbound access to common registries (PyPI, npm,
apt, crates.io). This is sufficient for both projects in this repo. Only change it if
you need to reach internal services or want to lock down outbound traffic.

---

## Step 2 — Commit repo configuration

The cloud VM clones your repository, so anything you want Claude to see must be
committed. Relevant files for these projects:

```
CLAUDE.md                  # optional: global instructions for Claude
.claude/settings.json      # hooks, permissions, allowed tools
```

Your local `~/.claude/` user config does **not** carry over to cloud sessions.

Push all commits before launching a remote session:

```bash
git push
```

---

## Step 3 — Launch a remote session

### Standard (GitHub-connected repo)

```bash
claude --remote "$(cat project_latex_pdf.md | grep -A 30 'Task prompt' | tail -n +3 | head -n -1)"
```

Or write the prompt inline:

```bash
claude --remote "Follow the instructions in project_latex_pdf.md to produce doc.pdf"
```

The simplest form — pass the file as context and let Claude read it:

```bash
claude --remote "Read project_latex_pdf.md and carry out all steps listed under 'Task prompt'."
```

### Bundle mode (no GitHub connection needed)

If your repo isn't on GitHub, or you want to force an upload of local uncommitted
state, set:

```bash
CCR_FORCE_BUNDLE=1 claude --remote "Read project_latex_pdf.md and carry out all steps."
```

The CLI bundles your working tree (up to 100 MB) and uploads it directly.

### Running both projects in parallel

Remote sessions are independent — you can fire them simultaneously:

```bash
claude --remote "Read project_latex_pdf.md and carry out all steps listed under 'Task prompt'." &
claude --remote "Read project_jupytext_notebook.md and carry out all steps listed under 'Task prompt'." &
```

---

## Step 4 — Monitor progress

### From the CLI

```bash
/tasks        # list all running cloud sessions and their status
```

Press `o` on a session to open it in your browser.

### From the browser

Visit `claude.ai/code`. Active sessions appear in the sidebar. You can watch the
conversation in real time, see tool calls, and read output as it streams.

### From the mobile app

The claude.ai mobile app shows running sessions — useful for checking in without
opening a laptop.

The session keeps running even if you close the browser tab or shut your laptop.

---

## Step 5 — Teleport back to your terminal

Once the session is complete (or at any point you want to take over locally):

```bash
# Interactive picker — choose from all your cloud sessions
claude --teleport

# Resume a specific session by ID (shown in /tasks output)
claude --teleport <session-id>
```

What teleport does:
- Loads the full conversation history from the cloud session into your terminal
- Fetches and checks out the branch the cloud session was working on
- Stashes any local uncommitted changes to keep your working directory clean

After teleporting you are in a normal local Claude Code session — you can inspect
files, run commands, request changes, and create a PR.

---

## Step 6 — Inspect outputs and wrap up

### LaTeX / PDF project

```bash
ls -lh doc.pdf doc.tex doc.md doc.log   # check artifacts exist
xdg-open doc.pdf                         # open the PDF (Linux)
open doc.pdf                             # macOS
```

Check `doc.log` if you want to review the compilation history.

### Jupytext / Notebook project

```bash
ls -lh notebook.md notebook.ipynb notebook_executed.ipynb
# open in VS Code
code notebook_executed.ipynb
# or start JupyterLab
jupyter lab notebook_executed.ipynb
```

### Commit the outputs

```bash
git add .
git commit -m "Add generated outputs from remote session"
git push
```

---

## Troubleshooting

| Symptom | Likely cause | Fix |
|---------|--------------|-----|
| Session fails immediately | Setup script error | Check script syntax; run `check-tools` in a manual session first |
| `pdflatex: command not found` | TeX not installed | Verify the setup script ran; check environment logs on claude.ai |
| `jupytext: command not found` | pip install failed | Confirm network access allows PyPI; add `pip install jupytext` to setup script |
| Teleport says "branch not found" | Cloud session didn't push the branch | Ask Claude in the session to `git push -u origin HEAD` before teleporting |
| IP allowlist error | Org blocks Anthropic infrastructure IPs | Cloud sessions call Anthropic APIs from Anthropic's network — exempt those IPs or use a personal account |
| Bundle too large | Repo exceeds 100 MB | Add large binaries to `.gitignore` or use a GitHub-connected repo |

---

## Quick reference

```bash
# Launch remote session
claude --remote "<prompt>"

# Force bundle upload (no GitHub needed)
CCR_FORCE_BUNDLE=1 claude --remote "<prompt>"

# Monitor sessions
/tasks

# Pull cloud session to terminal
claude --teleport
claude --teleport <session-id>

# Check available tools inside a cloud session
check-tools
```
