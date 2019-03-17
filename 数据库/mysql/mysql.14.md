---
title: 数据分组GROUP BY
tags: 
- MySQL
- 数据库
categories: 
- Database
---

# 数据分组GROUP BY(WHERE子句之后，ORDER BY之前)
```
GRPUP BY <column-name>
```

创建分组,通过GROUP BY将数据按vend_id分组后，COUNT（*）将会对分组后的数据分别计算一次。
```
SELECT vend_id, COUNT(*) AS num_prods
FROM products
GOURP BY vend_id
```