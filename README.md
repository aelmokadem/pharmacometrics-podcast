# The Pharmacometrics Podcast — Technical Companion

Explore the models, mathematics, and code behind pharmacometrics. This repository contains educational notebooks and simulations on PK/PD, population pharmacokinetics, PBPK, quantitative systems pharmacology, and computational tools.

**[Read the technical companion](https://aelmokadem.github.io/pharmacometrics-podcast/)** · **[Browse notebooks](https://aelmokadem.github.io/pharmacometrics-podcast/resources.html)** · **[Podcast episodes and show notes](https://aelmokadem.me/Podcasts/)**

Some notebooks accompany podcast episodes; others are standalone tutorials. You can use any notebook without listening to an episode first.

## Start here

Read [Simulating a One-Compartment Pharmacokinetic Model](https://aelmokadem.github.io/pharmacometrics-podcast/notebooks/pkpd/one-compartment-pk.html) for an introduction to an intravenous bolus model, its equations, and a concentration–time simulation in base R. The [source notebook](notebooks/pkpd/one-compartment-pk.qmd) contains the explanation and executable code together.

You can read the published notebooks, code, and results in your browser without installing anything.

## Find the code

Notebooks are organized by scientific topic:

| Folder | Topic |
|---|---|
| [`notebooks/pkpd/`](notebooks/pkpd/) | PK/PD modeling |
| [`notebooks/poppk/`](notebooks/poppk/) | Population pharmacokinetics |
| [`notebooks/pbpk/`](notebooks/pbpk/) | Physiologically based pharmacokinetic modeling |
| [`notebooks/qsp/`](notebooks/qsp/) | Quantitative systems pharmacology |
| [`notebooks/simulation/`](notebooks/simulation/) | Simulation and computational tools |

The initial notebook uses R. Python and Julia environments are ready for future notebooks. Each notebook explains its assumptions and dependencies; some topic folders do not have notebooks yet. Shared datasets belong in `data/`.

## Run a notebook yourself

Clone or download this repository. The commands below run from its root directory. To execute a complete `.qmd` notebook, install [Quarto](https://quarto.org/docs/get-started/) (version 1.8.24 matches this project) and the notebook's language runtime.

For the initial R example, use R 4.5.3:

```sh
# Restore the notebook's R packages.
Rscript -e 'renv::restore(prompt = FALSE)'
# Run the code and generate the notebook as HTML.
quarto render notebooks/pkpd/one-compartment-pk.qmd
```

Open the resulting `_site/notebooks/pkpd/one-compartment-pk.html` in your browser. You can also open the source in an editor with Quarto support, such as RStudio or VS Code, to explore the code and change the hypothetical parameters.

Set up only the language you need:

| Language | Environment | Restore packages |
|---|---|---|
| R 4.5.3 | renv | `Rscript -e 'renv::restore(prompt = FALSE)'` |
| Python 3.13.12 | uv | `uv sync --locked` |
| Julia 1.12.4 | Pkg | `julia --project=. -e 'using Pkg; Pkg.instantiate()'` |

Lockfiles record package versions to help reproduce the results. For Python and Julia execution, including the Julia notebook kernel setup, see [language environment setup](MAINTAINING.md#r-python-and-julia-environments).

## Questions and suggestions

Found an error or have a topic suggestion? [Open an issue](https://github.com/aelmokadem/pharmacometrics-podcast/issues) and include the notebook name and any relevant details.

For adding notebooks, updating packages, or publishing the website, see the [maintainer guide](MAINTAINING.md).
