# nextup-lab

**扔几个实验进去，告诉你下一批跑什么——化学、化工、生物，啥都行。**

```python
from nextup_lab import suggest

next_batch = suggest(history, candidates, objective="yield", k=4)
```

完事。把你测过的实验丢进去，下一批跑什么就出来了。

## 现在就试

→ **[打开 Colab demo](https://colab.research.google.com/github/hg125chinese-sketch/nextup-lab/blob/main/quickstart.ipynb)** ←

不装东西、不登账号。上传你的 CSV，运行所有 cell，看结果。

## 它干啥的

你手上有：
- 几个已经做完的实验（条件 + 测到的结果）
- 还没做的一堆候选条件

你想要：下一批先跑哪几个——挑出最有可能拿到好结果的那几个。

`suggest()` 给你这答案。返回排序好的 top-k。

## 输入长这样

一个 CSV。里面**一列**是你测的东西（产率、选择性、转化率、EC50……）。已测的行有值，没测的行留空。剩下的列就是条件：数字、类别标签、分子 SMILES，混着写都行。

| catalyst | solvent | temp_C | substrate         | yield |
|----------|---------|--------|-------------------|-------|
| Pd-A     | toluene | 80     | CC(=O)Oc1ccccc1   | 73    |
| Ni-B     | DMF     | 100    | CC(=O)Oc1ccccc1   | 12    |
| Pd-A     | toluene | 100    | Cc1ccccc1O        |       |
| ...      | ...     | ...    | ...               |       |

里面是啥，它自己识别。

## 安装

```bash
pip install nextup-lab
```

Python 3.10 / 3.11 / 3.12，Linux / macOS / Windows 都行。纯 CPU，不要 GPU。

## License

闭源。见 `LICENSE`。

---

[English README](README.md)
