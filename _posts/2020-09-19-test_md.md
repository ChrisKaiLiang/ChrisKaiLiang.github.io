---
layout: post
title: "markdown test"
subtitle: "subtitle"
date: 2020-02-27 00:30:00 -0100
background: '/img/posts/06.jpg'
---

# Test the markdown

## test table
| A | B | C |
| - | - | - |
| 1 | 2 | 3 |

### test subtitle 3
#### test subtitle 4

**test img**  
![imgg](/img/posts/06.jpg)


**test python**  
```python
import pandas as pd

pd.read_csv('a.csv')
print("hello world")
```

**test SQL**
```SQL
SELECT
    A,
    B,
    SUM(C)
FROM 
    table_A
GROUP BY
    A, B
ORDER BY
    A, B;
