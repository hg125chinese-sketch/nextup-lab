# nextup-lab

[![PyPI](https://img.shields.io/pypi/v/nextup-lab.svg)](https://pypi.org/project/nextup-lab/)
[![Python](https://img.shields.io/pypi/pyversions/nextup-lab.svg)](https://pypi.org/project/nextup-lab/)
[![License: Proprietary](https://img.shields.io/badge/license-Proprietary-blue.svg)](LICENSE)

**Find the best experimental conditions faster.**

`nextup-lab` picks which experiments to run next when you're optimizing yield, selectivity, EC50 — anything you can measure. Hand it the conditions you've already tested and the candidates you haven't, and it ranks the candidates most likely to beat your current best.

```python
from nextup_lab import suggest

next_batch = suggest(history, candidates, objective="yield", k=4)
```

→ **[Try it in Colab](https://colab.research.google.com/github/hg125chinese-sketch/nextup-lab/blob/main/quickstart.ipynb)** · [中文 README](README.zh.md)

---

## Why this exists

A single round of experiments — catalysts, solvents, temperatures, substrates — can burn weeks of lab time, and most of the results are mediocre. `nextup-lab` reads what you've measured so far and ranks every untested condition by its probability of beating your current best, so each next batch is closer to optimal.

It's domain-agnostic: synthesis, formulation, microbial strain screening, process optimization — anywhere a condition is a row in a table.

## How the input looks

One CSV. One column is what you measure (yield, conversion, EC50, …). Tested rows have a value; untested rows leave it blank. Everything else is conditions — numbers, category labels, or molecule SMILES, mixed freely.

| catalyst | solvent | temp_C | substrate         | yield |
|----------|---------|--------|-------------------|------:|
| Pd-A     | toluene | 80     | CC(=O)Oc1ccccc1   |    73 |
| Ni-B     | DMF     | 100    | CC(=O)Oc1ccccc1   |    12 |
| Pd-A     | toluene | 100    | Cc1ccccc1O        |       |
| …        | …       | …      | …                 |       |

Column types are auto-detected; you don't have to label anything.

## Quickstart

```python
import pandas as pd
from nextup_lab import suggest

df = pd.read_csv("my_experiments.csv")
history    = df[df["yield"].notna()]
candidates = df[df["yield"].isna()].drop(columns=["yield"])

next_batch = suggest(history, candidates, objective="yield", k=4)
print(next_batch)
```

`next_batch` is the top-4 candidate rows, sorted best-first, with a `nextup_rank` column and a `nextup_score` column (higher = more confident the row is in the top-5% region of the response). Run those four, fill in the results, call `suggest()` again. Repeat.

## Install

```bash
pip install nextup-lab
```

Python 3.10, 3.11, 3.12, on Linux / macOS / Windows. CPU-only — no GPU required. Typical call is well under a minute on pools of a few hundred candidates.

## How it works (briefly)

Bayesian optimization under the hood: fit a surrogate to what you've measured, score every candidate by tail probability against your current best, return top-k. For very small inputs (fewer than 5 tested rows, or fewer than 20 candidates) it falls back to a diversity-spread pick — Bayesian methods underperform random in that regime, so it doesn't pretend otherwise.

The default model was selected from a 5-dataset × 165-config × 64-seed screen on published optimization benchmarks (Doyle Pd, Stevens Ni, Perera Suzuki, DeoxyF Ag, others). The benchmark and selection method are described in the project notes.

## License

Proprietary. The package on PyPI is distributed under the license in [`LICENSE`](LICENSE) — closed-source binary distribution only. No source code is included in the wheel.
