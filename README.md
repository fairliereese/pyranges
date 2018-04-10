# pyranges

Not optimized, not ready for use, only the "in self but not in other" operator
(-) implemented.

The input dataframe needs at least the columns Chromome, Start, End. The rest are arbitrary.

Using all operations on the GRanges object return a new GRanges object, not a
view. (Execpt those that just change the underlying dataframe, which is
accessible with gr.df)

Columns in the dataframe given to the constructor are available as an attribute
of the object. I.e. gr.Score returns the Score series in the example below.

You can add columns to the underlying dataframe just fine and change all columns
except Chromosome, Start and End. If you want to change those columns you should
create a new GRanges object with the new values. Note: Changing the Chromosome, Start
and End columns does not change the datastructures in the GRanges object that
are used to perform quick range operations on the GRange and will therefore lead to
erroneus results.

```
from pyranges import GRanges

import pandas as pd
from io import StringIO

c = """Chromosome Start End
chr1 3 6
chr1 5 7
chr1 8 9"""

df = pd.read_table(StringIO(c), sep="\s+", header=0)

c = """Chromosome Start End
chr1 1 2
chr1 6 6"""
df2 = pd.read_table(StringIO(c), sep="\s+", header=0)

gr1 = GRanges(df)

gr2 = GRanges(df2)

print(gr1)
+----|--------------|---------|-------+
|    | Chromosome   |   Start |   End |
|----|--------------|---------|-------|
|  0 | chr1         |       3 |     6 |
|  1 | chr1         |       5 |     7 |
|  2 | chr1         |       8 |     9 |
+----|--------------|---------|-------+
GRanges object with 3 sequences from 1 chromosomes.

print(gr2)
+----|--------------|---------|-------+
|    | Chromosome   |   Start |   End |
|----|--------------|---------|-------|
|  0 | chr1         |       1 |     2 |
|  1 | chr1         |       6 |     6 |
+----|--------------|---------|-------+
GRanges object with 2 sequences from 1 chromosomes.

gr1 - gr2
<pyranges.pyranges.GRanges at 0x11299b198>

print(gr1 - gr2)
+----|--------------|---------|-------+
|    | Chromosome   |   Start |   End |
|----|--------------|---------|-------|
|  0 | chr1         |       8 |     9 |
+----|--------------|---------|-------+
GRanges object with 1 sequences from 1 chromosomes.

df = pd.read_table("example_data/lamina.bed", sep="\s+", header=None, names="Chromosome Start End Score".split(), skiprows=1)

gr = GRanges(df)

gr
<pyranges.pyranges.GRanges at 0x112341f60>

print(gr)
+------|--------------|----------|----------|--------------------+
|      | Chromosome   | Start    | End      | Score              |
|------|--------------|----------|----------|--------------------|
| 0    | chr1         | 11323785 | 11617177 | 0.86217008797654   |
| 1    | chr1         | 12645605 | 13926923 | 0.9348914858096831 |
| 2    | chr1         | 14750216 | 15119039 | 0.9459459459459459 |
| ...  | ...          | ...      | ...      | ...                |
| 1341 | chrY         | 13556427 | 13843364 | 0.892655367231638  |
| 1342 | chrY         | 14113371 | 15137286 | 0.9364089775561101 |
| 1343 | chrY         | 15475619 | 19472504 | 0.8138424821002389 |
+------|--------------|----------|----------|--------------------+
GRanges object with 1344 sequences from 24 chromosomes.

subset_gr = gr[:111617177]

print(subset_gr)
+-----|--------------|-----------|-----------|--------------------+
|     | Chromosome   | Start     | End       | Score              |
|-----|--------------|-----------|-----------|--------------------|
| 0   | chr15        | 50892438  | 53249359  | 0.920298879202989  |
| 1   | chr15        | 23238025  | 23460157  | 0.8214285714285708 |
| 2   | chr15        | 20639479  | 22659952  | 0.8454692556634301 |
| ... | ...          | ...       | ...       | ...                |
| 989 | chr5         | 107749740 | 107997497 | 0.9004975124378108 |
| 990 | chr5         | 108850224 | 109049698 | 1.0                |
| 991 | chr5         | 111540207 | 112050044 | 0.9844054580896691 |
+-----|--------------|-----------|-----------|--------------------+
GRanges object with 992 sequences from 24 chromosomes.

print(subset_gr["chr1"])
+-----|--------------|-----------|-----------|--------------------+
|     | Chromosome   | Start     | End       | Score              |
|-----|--------------|-----------|-----------|--------------------|
| 0   | chr1         | 95358151  | 96453308  | 0.952742616033755  |
| 1   | chr1         | 38272060  | 39078902  | 0.940932642487047  |
| 2   | chr1         | 12645605  | 13926923  | 0.9348914858096831 |
| ... | ...          | ...       | ...       | ...                |
| 45  | chr1         | 110470808 | 110667025 | 0.7201646090534979 |
| 46  | chr1         | 108566438 | 108844851 | 1.0                |
| 47  | chr1         | 111580508 | 111778144 | 0.9533678756476679 |
+-----|--------------|-----------|-----------|--------------------+
GRanges object with 48 sequences from 1 chromosomes.

gr.Score.head()

0    0.862170
1    0.934891
2    0.945946
3    0.895175
4    0.892526
Name: Score, dtype: float64
```

# See also

https://github.com/endrebak/pyrle
https://github.com/vsbuffalo/BioRanges/tree/master/BioRanges