# The Pharmacometrics Podcast — Technical Companion

Explore the models, mathematics, and code behind pharmacometrics. This repository contains educational notebooks and simulations on PK/PD, population pharmacokinetics, PBPK, quantitative systems pharmacology, and computational tools.

**[Read the technical companion](https://aelmokadem.github.io/pharmacometrics-podcast/)** · **[Browse notebooks](https://aelmokadem.github.io/pharmacometrics-podcast/resources.html)** · **[Podcast episodes and show notes](https://aelmokadem.me/podcasts/pharmacometrics-podcast/)**

Some notebooks accompany podcast episodes; others are standalone tutorials. You can use any notebook without listening to an episode first.

## Start here

Start with [From Dose to Exposure to Response](https://aelmokadem.github.io/pharmacometrics-podcast/notebooks/dose-exposure-response.html) for IV and oral dosing, exposure, biomarker inhibition, and dosing schedules in base R. The [source notebook](notebooks/dose-exposure-response.qmd) contains the explanations and executable code together.

Notebooks may include a **Related podcast episodes** section with episode titles and listening links. One notebook can support multiple episodes; standalone tutorials are welcome and do not need an episode association.

You can read the published notebooks, code, and results in your browser without installing anything.

## Find the code

All source notebooks live directly in [`notebooks/`](notebooks/). The website generates topic filters from each notebook's `categories` metadata, so a notebook can cover multiple topics without belonging to a topic folder. Categories can expand as notebooks are added; they do not depend on a fixed episode sequence.

The notebooks use R. Python and Julia environments are ready for future notebooks. Each notebook explains its assumptions and dependencies. Shared datasets belong in `data/`.

## Run a notebook yourself

Clone or download this repository. The commands below run from its root directory. To execute a complete `.qmd` notebook, install [Quarto](https://quarto.org/docs/get-started/) (version 1.8.24 matches this project) and the notebook's language runtime.

For the initial R example, use R 4.6.2:

Check `R --version` before restoring packages. If you manage R with rig, select your R 4.6.2 installation with `rig default` first.

```sh
# Restore the notebook's R packages.
Rscript -e 'renv::restore(prompt = FALSE)'
# Run the code and generate the notebook as HTML.
quarto render notebooks/dose-exposure-response.qmd
```

Open the resulting `_site/notebooks/dose-exposure-response.html` in your browser. You can also open the source in an editor with Quarto support, such as RStudio or VS Code, to explore the code and change the hypothetical parameters.

Set up only the language you need:

| Language | Environment | Restore packages |
|---|---|---|
| R 4.6.2 | renv | `Rscript -e 'renv::restore(prompt = FALSE)'` |
| Python 3.14.6 | uv | `uv sync --locked` |
| Julia 1.13.3 | Pkg | `julia +1.13.3 --project=. -e 'using Pkg; Pkg.instantiate()'` (with juliaup) |

Lockfiles record package versions to help reproduce the results. For Python and Julia execution, including the Julia notebook kernel setup, see [language environment setup](MAINTAINING.md#r-python-and-julia-environments).

## Questions and suggestions

Found an error or have a topic suggestion? [Open an issue](https://github.com/aelmokadem/pharmacometrics-podcast/issues) and include the notebook name and any relevant details.

For other questions or collaboration inquiries, email Ahmed Elmokadem at [podcast@aequantitative.com](mailto:podcast@aequantitative.com) or connect on [LinkedIn](https://www.linkedin.com/in/ahmed-elmokadem-87b01875/).

For adding notebooks, updating packages, or publishing the website, see the [maintainer guide](MAINTAINING.md).
