# nextup-lab

[![PyPI](https://img.shields.io/pypi/v/nextup-lab.svg)](https://pypi.org/project/nextup-lab/)
[![Python](https://img.shields.io/pypi/pyversions/nextup-lab.svg)](https://pypi.org/project/nextup-lab/)
[![License: Proprietary](https://img.shields.io/badge/license-Proprietary-blue.svg)](LICENSE)

**更快找到最好的实验条件。**

`nextup-lab` 帮你挑下一批要跑的实验。把已经测过的条件和还没测的候选丢进去,它返回最有可能超过你当前最好结果的几个候选。

```python
from nextup_lab import suggest

next_batch = suggest(history, candidates, objective="yield", k=4)
```

→ **[在 Colab 里跑 demo](https://colab.research.google.com/github/hg125chinese-sketch/nextup-lab/blob/main/quickstart.ipynb)** · [English README](README.md)

---

## 为什么需要它

实验贵,时间更贵。筛一轮催化剂、溶剂、温度、底物,常常烧掉几周的实验时间,而大部分结果是中等的。`nextup-lab` 读你已测的数据,对每一个未测条件按"超过当前最好结果的概率"打分排序,让每一批下一步实验离最优更近一步。

它不限领域:有机合成、配方优化、菌株筛选、过程参数寻优——只要一个条件能写成表格里的一行,就能用。

## 输入数据长什么样

一个 CSV。其中一列是你要测的指标(产率、转化率、EC50、……)。测过的行有值,没测的行留空。其它列是条件——数字、类别标签、或者 SMILES,可以混用。

| catalyst | solvent | temp_C | substrate         | yield |
|----------|---------|--------|-------------------|------:|
| Pd-A     | toluene | 80     | CC(=O)Oc1ccccc1   |    73 |
| Ni-B     | DMF     | 100    | CC(=O)Oc1ccccc1   |    12 |
| Pd-A     | toluene | 100    | Cc1ccccc1O        |       |
| …        | …       | …      | …                 |       |

列的类型会自动识别,不需要手工标注。

## 快速上手

```python
import pandas as pd
from nextup_lab import suggest

df = pd.read_csv("my_experiments.csv")
history    = df[df["yield"].notna()]
candidates = df[df["yield"].isna()].drop(columns=["yield"])

next_batch = suggest(history, candidates, objective="yield", k=4)
print(next_batch)
```

`next_batch` 是排序好的前 4 个候选,带一列 `nextup_rank`(1 = 优先跑)和一列 `nextup_score`(模型认为这一行落在响应 top-5% 区间的概率,越高越自信)。把这 4 个跑出来,填回结果,再调 `suggest()`。如此循环。

## 安装

```bash
pip install nextup-lab
```

Python 3.10、3.11、3.12,Linux / macOS / Windows 都支持。纯 CPU,不需要 GPU。几百个候选的池子,典型调用时间在一分钟内。

## 内部原理(简要)

底层是贝叶斯优化:对已测数据拟合一个代理模型,按"超过当前最好结果的尾概率"给每个候选打分,返回 top-k。如果输入太小(已测 < 5 行或候选 < 20 行),自动切换到 diversity 采样模式——这种规模下贝叶斯方法跑不过随机,所以不假装能赢。

默认模型是从一个 5 数据集 × 165 配置 × 64 种子的公开基准筛选中选出来的(Doyle Pd、Stevens Ni、Perera Suzuki、DeoxyF Ag 等)。基准和选型方法在项目内部文档里。

## License

闭源。PyPI 上的包遵循 [`LICENSE`](LICENSE) 中的条款——仅二进制分发,不含源码。
