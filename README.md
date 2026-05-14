# nextup-lab

**Drop your experiments in, get the next batch to run. Chemistry, chemical engineering, biology — whatever you're optimizing.**

[中文 README](README.zh.md)

```python
from nextup_lab import suggest

next_batch = suggest(history, candidates, objective="yield", k=4)
```

That's it. Drop a CSV of what you've tested in, get the next experiments out.

## Try it now

→ **[Open the Colab demo](https://colab.research.google.com/github/hg125chinese-sketch/nextup-lab/blob/main/quickstart.ipynb)** ←

No install. No login. Upload your CSV, run all cells, get suggestions.

## What it does

You have:
- A few experiments you've already run (conditions + the result you measured)
- A list of conditions you haven't tried yet

You want: the next handful to actually run — the ones most likely to be the best.

That's what `suggest()` returns. Top-k candidates, ranked.

## How the input looks

One CSV. One column is what you're measuring (yield, selectivity, conversion, EC50…). Tested rows have the value; candidates leave it blank. Everything else is conditions — numbers, category labels, or molecule strings, mix and match.

| catalyst | solvent | temp_C | substrate         | yield |
|----------|---------|--------|-------------------|-------|
| Pd-A     | toluene | 80     | CC(=O)Oc1ccccc1   | 73    |
| Ni-B     | DMF     | 100    | CC(=O)Oc1ccccc1   | 12    |
| Pd-A     | toluene | 100    | Cc1ccccc1O        |       |
| ...      | ...     | ...    | ...               |       |

Whatever's in there, it figures out.

## Install

```bash
pip install nextup-lab
```

Python 3.10 / 3.11 / 3.12, on Linux / macOS / Windows. CPU only — no GPU needed.

## License

Proprietary. See `LICENSE`.
