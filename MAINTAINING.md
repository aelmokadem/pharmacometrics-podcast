# Maintaining the Technical Companion

This guide covers authoring notebooks, managing dependencies, and publishing the website. For reading and running the notebooks, start with the [README](README.md).

## Repository structure

```text
_quarto.yml                  Website, navigation, themes, and execution settings
index.qmd                    Homepage
resources.qmd                Automatic directory of published notebooks
about.qmd                    Purpose and relationship to the podcast
notebooks/
  pkpd/one-compartment-pk.qmd Initial base R example
  poppk/                     Population pharmacokinetics
  pbpk/                      Physiologically based pharmacokinetics
  qsp/                       Quantitative systems pharmacology
  simulation/                Simulation and computational tools
data/                        Small, shareable notebook datasets
images/                      Images and future podcast artwork
styles/custom.scss           Minimal Bootstrap theme customization
.Rprofile, renv.lock, renv/   R project activation and package lock
pyproject.toml, uv.lock      Python project and package lock
.python-version              Python interpreter pin
Project.toml, Manifest.toml  Julia project and package lock
_freeze/                     Computational results and figures; must be committed
.github/workflows/publish.yml GitHub Pages build and deployment
```

`_site/` and `.quarto/` are generated locally and ignored. Empty content directories are retained with `.gitkeep`. There is no episode directory.

## Software and local preview

Install [Quarto CLI](https://quarto.org/docs/get-started/) using its installer for your operating system. The workflow pins **Quarto 1.8.24**; use that version locally for matching rendering behavior. No Node or frontend package installation is needed.

For the initial executable notebook, install R 4.5.3 and restore the project library from the repository root:

```sh
Rscript -e 'renv::restore(prompt = FALSE)'
```

The project `.Rprofile` activates renv and bootstraps its locked version if needed. The lockfile includes `knitr`, `rmarkdown`, and their dependencies. See the language environment instructions below for Python and Julia.

The simulation uses base R and standard graphics only. Initial outputs were generated with R 4.5.3. To check your installation and preview from the repository root:

```sh
quarto check
quarto preview
```

Quarto prints the local preview URL. Use `Ctrl+C` to stop. Previewing changed computational notebooks can execute code; install their documented dependencies first.

## Execute a notebook and refresh frozen results

Execute the example explicitly:

```sh
quarto render notebooks/pkpd/one-compartment-pk.qmd
```

A single-file render executes code even with `freeze: auto`. It updates the corresponding results and figures under `_freeze/`. Run this after changes to code, external data, or dependencies: automatic freezing detects changes to notebook source, **not changes to external inputs**.

Then render the **whole project**:

```sh
quarto render
```

With `execute: freeze: auto`, a whole-project render reuses frozen computations for unchanged notebooks. New or changed computational notebooks execute and update their frozen results. Do not set `execute: false`: it would prevent normal authoring and regeneration.

Review the resulting site, including equations, code, plots, links, and light/dark themes. When approved for publication, include each changed `.qmd` and its corresponding `_freeze/` files together in the commit. Frozen output can contain computed data: review it before sharing. Do not edit frozen JSON manually or ignore `_freeze/`.

## Add a notebook

Create a lowercase, hyphenated `.qmd` filename in the relevant topic folder. The resources listing discovers `notebooks/**/*.qmd` automatically. Use this front matter, replacing the example metadata:

```yaml
---
title: "Your notebook title"
description: "A short explanation of the scientific question and approach."
categories: [PK/PD Modeling]
programming-language: R
engine: knitr
---
```

Use the topic names shown on the Resources page: `PK/PD Modeling`, `Population Pharmacokinetics`, `PBPK Modeling`, `Quantitative Systems Pharmacology`, or `Simulation & Computational Tools`. Multiple topics are allowed. The listing displays title, description, topic, and language, with the title linking to the notebook.

Start with intuition before equations. Adapt this flexible outline to the subject:

1. Introduction and learning objectives
2. Scientific background and assumptions
3. Mathematical formulation with notation and units
4. Commented implementation
5. Simulation or analysis
6. Results and interpretation
7. Limitations and key takeaways
8. Verified references or further reading
9. Optional associated episode link, only when a real URL is available

State runtime and package requirements in the notebook, identify hypothetical inputs, and provide figure captions and alternative text. Use native LaTeX math, cross-references, and citation syntax (`[@key]` with a real bibliography file when needed). Static figures and self-contained HTML widgets are supported; interactive outputs must work in a static website without a running backend.

Internal links should use relative `.qmd` paths. Add shareable data to `data/` and refer to it relative to the notebook's directory (for example `../../data/example.csv`). Execute each new notebook locally and render the whole project before publication.

### R, Python, and Julia environments

Each language has one shared environment at the repository root. Set up only the languages you use. Lockfiles record package versions; installed libraries, virtual environments, and machine-specific kernels are not committed. These files manage packages, not system libraries or Quarto itself.

| Language | Runtime used to initialize | Committed environment files | Notebook front matter |
|---|---|---|---|
| R | 4.5.3 (recorded in `renv.lock`) | `.Rprofile`, `renv.lock`, `renv/activate.R`, `renv/settings.json`, `renv/.gitignore` | `engine: knitr`, `programming-language: R`; use `{r}` chunks |
| Python | 3.13.12 (pinned in `.python-version`) | `pyproject.toml`, `uv.lock`, `.python-version` | `jupyter: python3`, `programming-language: Python`; use `{python}` chunks |
| Julia | 1.12.4 (recorded in `Manifest.toml`, project accepts 1.12.x) | `Project.toml`, `Manifest.toml` | `jupyter: pharmacometrics-julia`, `programming-language: Julia`; use `{julia}` chunks |

For Python and Julia notebooks, omit `engine: knitr`. Keep each notebook's scientific dependencies documented in its introduction. Start all commands below at the repository root.

#### R with renv

```sh
# Restore the locked package library after cloning or pulling changes.
Rscript -e 'renv::restore(prompt = FALSE)'
# Check that the library and lockfile agree.
Rscript -e 'renv::status()'
# Execute using the activated project library.
quarto render notebooks/pkpd/one-compartment-pk.qmd
```

In an R session started at the project root, add a needed package with `renv::install("package-name")`, then run `renv::snapshot()`. This project uses snapshot type `all` to record the project library, including rendering tools that are not explicitly imported by notebook code. Install only project dependencies there. Use `renv::update("package-name")` for an intentional upgrade, snapshot, and rerun affected notebooks. Restore and inspect changes rather than automatically upgrading when opening the project. renv records the R version but does not install R itself.

#### Python with uv

Install [uv](https://docs.astral.sh/uv/getting-started/installation/), then:

```sh
# Restore exactly the committed lockfile; uv can install the pinned Python.
uv sync --locked
# Check the interpreter and available notebook kernels.
uv run --locked python --version
uv run --locked jupyter kernelspec list
# Execute a future Python notebook (replace the path with a real notebook).
uv run --locked quarto render notebooks/simulation/your-python-notebook.qmd
```

`uv run` puts the project `.venv` on PATH; Quarto detects that environment. If your editor selects a different Python, select `.venv` or set `QUARTO_PYTHON` to its Python executable for that session. Jupyter and ipykernel are included; no scientific packages have been added yet.

Add dependencies with `uv add package-name`, remove them with `uv remove package-name`, and intentionally upgrade one with `uv lock --upgrade-package package-name` followed by `uv sync --locked`. Commit `pyproject.toml` and `uv.lock` together. Changing the Python minor version also requires updating `requires-python`, the interpreter pin, and the lockfile.

#### Julia with Pkg

Install Julia 1.12.4, matching the manifest. If using juliaup, `juliaup add 1.12.4` installs it; use `julia +1.12.4` in place of `julia` below if it is not your default. This machine already had Julia installed under `~/.juliaup/bin`; add that directory to PATH if your shell cannot find it.

First run `uv sync --locked` to provide Jupyter. Then, on macOS/Linux:

```sh
# Restore the Julia packages, using the uv-managed Jupyter installation.
JUPYTER="$PWD/.venv/bin/jupyter" julia --project=. -e 'using Pkg; Pkg.instantiate()'
# Install a local kernelspec whose Julia process always activates this project.
JUPYTER_DATA_DIR="$PWD/.venv/share/jupyter" julia --project=. -e 'using IJulia; IJulia.installkernel("Julia (Pharmacometrics Podcast)", "--project=" * pwd(); specname="pharmacometrics-julia")'
uv run --locked jupyter kernelspec list
# Execute a future Julia notebook (replace the path with a real notebook).
uv run --locked quarto render notebooks/simulation/your-julia-notebook.qmd
```

The kernelspec lives inside the ignored `.venv`; regenerate it after moving the checkout, recreating `.venv`, or changing the Julia executable. Its absolute project path is local metadata and is never committed. On Windows, use the corresponding `.venv/Scripts` executables and PowerShell environment-variable syntax.

Add a needed package with `julia --project=. -e 'using Pkg; Pkg.add("PackageName")'`. Use `Pkg.rm("PackageName")` to remove one and `Pkg.update("PackageName")` for an intentional upgrade. Pkg updates `Project.toml` and `Manifest.toml`; commit both. Use `Pkg.instantiate()` after pulling changes to restore the recorded versions. IJulia is the only direct dependency so far.

For all languages, rerun affected notebooks after package or runtime changes, review results, and include updated `_freeze/` outputs with the environment files. The freeze hash tracks notebook source, so changing a lockfile alone does **not** force computational results to refresh. GitHub Actions continues to install none of these language environments and relies on committed frozen results.

See the official [renv](https://rstudio.github.io/renv/articles/renv.html), [uv project](https://docs.astral.sh/uv/guides/projects/), [Pkg environment](https://pkgdocs.julialang.org/v1/environments/), and [IJulia kernel](https://ijulia.org/stable/manual/installation/#Installing-and-customizing-kernels) guides.

## GitHub Pages

The remote identifies this repository as [aelmokadem/pharmacometrics-podcast](https://github.com/aelmokadem/pharmacometrics-podcast). Its configured project-site URL is **https://aelmokadem.github.io/pharmacometrics-podcast/**. Internal navigation remains relative so it works beneath the project prefix.

Before the first deployment:

1. In the GitHub repository, open **Settings → Pages → Build and deployment** and choose **GitHub Actions** as the source.
2. Ensure repository Actions policy allows the official GitHub actions and `quarto-dev/quarto-actions/setup` used in the workflow.
3. If the `github-pages` environment has branch restrictions or required reviewers, allow `main` and complete any required approvals.
4. After reviewing and approving the local changes, commit the source **and `_freeze/`**, then push to `main`. This triggers publication. You can also select **Actions → Publish technical companion → Run workflow** after the workflow exists on the default branch.

The workflow installs Quarto, performs a whole-project render, uploads `_site/` with the official Pages artifact action, and deploys with the official Pages deployment action. Build has read-only repository access; only deployment receives `pages: write` and `id-token: write`. Deployments are serialized. No `gh-pages` branch, personal access token, or local `quarto publish` command is required.

Always refresh results locally before pushing changed notebooks. Missing or stale frozen output can trigger execution during CI; CI deliberately does not provision the language runtimes and packages to support it. Fix the notebook and regenerate its results locally rather than adding execution environments to the workflow.

For future updates: edit → execute changed notebooks → `quarto render` → review → commit source and frozen results together → push to `main`. Pushing publishes automatically once Pages is enabled.

Implementation follows Quarto's [freeze workflow](https://quarto.org/docs/projects/code-execution.html#freeze), [native listings](https://quarto.org/docs/websites/website-listings.html), and GitHub's [custom Pages workflows](https://docs.github.com/en/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages).
