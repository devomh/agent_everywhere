# Project: Markdown → Jupyter Notebook (via jupytext)

## Goal

Demonstrate a remote Claude Code session that produces a self-contained, executable
Jupyter notebook from scratch: choose a Python programming concept, write a
well-structured markdown file in jupytext's MyST format, convert it to `.ipynb`,
and execute it to verify all code cells run without errors.

## Task prompt (send this to `claude --remote`)

```
Choose a single Python programming concept that is interesting but explainable in
under 30 lines of code (e.g. generator functions, decorators, context managers,
or dataclasses).

Then carry out the following steps in order:

1. Write `notebook.md` in jupytext MyST Markdown format.
   The file must include:
   - A YAML front-matter block declaring kernelspec (python3) and jupytext format.
   - A title and an introductory prose section.
   - At least three `{code-cell} ipython3` blocks with short, runnable examples
     that progressively illustrate the concept.
   - A "Summary" section with a brief prose conclusion.

   Example front-matter:
   ---
   jupytext:
     formats: md:myst
     text_representation:
       extension: .md
       format_name: myst
   kernelspec:
     display_name: Python 3
     language: python
     name: python3
   ---

2. Convert `notebook.md` to `notebook.ipynb` using jupytext:
       jupytext --to ipynb notebook.md

3. Execute the notebook to verify all cells run cleanly:
       jupyter nbconvert --to notebook --execute notebook.ipynb \
           --output notebook_executed.ipynb

4. If any cell raises an error, fix `notebook.md`, re-convert, and re-execute.
   Repeat until execution succeeds with exit code 0.

5. Report the chosen concept and the titles of all prose sections.
```

## Custom environment requirements

A setup script must install jupytext, Jupyter, and nbconvert before the session starts.
See `remote_teleport_guide.md` for where to paste this script.

```bash
#!/usr/bin/env bash
set -euo pipefail
pip install --upgrade pip
pip install jupytext jupyter nbconvert ipykernel
python -m ipykernel install --user --name python3
```

## Expected outputs

| File | Description |
|------|-------------|
| `notebook.md` | Source MyST markdown file |
| `notebook.ipynb` | Notebook converted by jupytext |
| `notebook_executed.ipynb` | Notebook with cell outputs after execution |

## What makes this a good remote-teleport test

- jupytext conversion and nbconvert execution require real tooling — not just
  text transformations.
- The execution step catches code errors that a syntax check would miss (import
  errors, runtime exceptions, wrong output types).
- After teleporting, the local `.ipynb` file can be opened in VS Code or JupyterLab
  immediately, giving a tangible artifact to inspect.
- The MyST format keeps the source human-readable and diff-friendly in git.
