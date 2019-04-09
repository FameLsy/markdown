---
title: 过滤分组HAVING
tags: 
- MySQL
- 数据库
categories: 
- Database
---

# 过滤分组HAVING

1. WHERE是过滤行，而HAVING是过滤分组
2. WHERE是在分组前过滤，而HAVING是在分组后过滤
3. HAVING支持所有WHERE操作符

```java
//过滤分组,按cust_id分组，然后过滤出行数大于等于2组。
SELECT cust_id, COUNT(*) AS orders
FROM orders
GROUP BY cust_id
HAVING count(*) = 2
```