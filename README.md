# The Pharmacometrics Podcast — Technical Companion

Educational Quarto notebooks, mathematical explanations, simulations, and code for pharmacometrics and model-informed drug development. Content is organized by scientific topic and can be read independently of the podcast.

The [main podcast website](https://aelmokadem.me) hosts episode pages and listening links. This repository hosts the technical material.

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
_freeze/                     Computational results and figures; must be committed
.github/workflows/publish.yml GitHub Pages build and deployment
```

`_site/` and `.quarto/` are generated locally and ignored. Empty content directories are retained with `.gitkeep`. There is no episode directory.

## Software and local preview

Install [Quarto CLI](https://quarto.org/docs/get-started/) using its installer for your operating system. The workflow pins **Quarto 1.8.24**; use that version locally for matching rendering behavior. No Node or frontend package installation is needed.

For the initial executable notebook, install R and its Quarto rendering packages:

```r
install.packages(c("knitr", "rmarkdown"))
```

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

### R, Python, and Julia

Each notebook needs only its own language:

| Language | Front matter | Local execution requirements |
|---|---|---|
| R | `engine: knitr`, `programming-language: R` | R, knitr, rmarkdown, and packages used by that notebook; use executable `{r}` chunks |
| Python | `jupyter: python3`, `programming-language: Python` (omit `engine: knitr`) | Python, Jupyter, ipykernel, and the notebook's packages; use `{python}` chunks |
| Julia | `jupyter: julia-1.x`, `programming-language: Julia` (omit `engine: knitr`) | Julia, IJulia, Jupyter, and the notebook's packages; use `{julia}` chunks and replace `julia-1.x` with the installed kernel name |

For Python, an optional local environment can be created with `python3 -m venv .venv`, activated, then populated with `python -m pip install jupyter ipykernel`. Select that environment through Quarto's `QUARTO_PYTHON` setting if necessary. For Julia, install IJulia using `import Pkg; Pkg.add("IJulia")`; find available kernel names with `jupyter kernelspec list`. See the official [Python](https://quarto.org/docs/computations/python.html) and [Julia](https://quarto.org/docs/computations/julia.html) guides for runtime setup.

Document exact versions when an analysis needs them. Introduce language-specific environment files only when real notebook dependencies justify them. GitHub Actions installs none of these language environments; it relies on the committed frozen results.

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
