---
layout:     post
title:      Read Dirty Csv
subtitle:   
date:       2020-07-09
author:     Chauncey
header-img: img/post-bg-isos9-web.jpg
catalog: true
tags:
    - High Frequency Trading
    - Notes
    - Strategy

---





1 先把一行直接读成一个小单元格
2 对于一个单元格子，用split(逗号)分开，横向expanding拓展很多col，这样col[0]就是instrument了



```python
# Input
1,2,3,4,5
1,2,3,4,5,6
,,3,4,5
1,2,3,4,5,6,7
,2,,4


# Output (DataFrame)
   0  1  2  3     4     5     6
0  1  2  3  4     5  None  None
1  1  2  3  4     5     6  None
2        3  4     5  None  None
3  1  2  3  4     5     6     7
4     2     4  None  None  None
```



```python
import pandas as pd

df = pd.read_csv('Test.csv', header=None, sep='\n')
df = df[0].str.split(',', expand=True)
# ... do some modifications with df
```

