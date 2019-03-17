---
title: SELECT子句-ORDER BY
tags: 
- MySQL
- 数据库
categories: 
- Database
---

# 排序检索数据（位于 WHERE 之后）

关键字：
- ORDER BY
- DESC:降序  
- ASC:升序(默认)  

```java
ORDER BY <column-name> [DESC/ASC]
//按多个列进行排序（先根据列1排，在根据列2排）
ORDER BY <column-name>,<column-name2> [DESC/ASC]
//指定排序方向的多个列排序，DESC/ASC只应用到直接位于其前面的列名
ORDER BY <column-name> [DESC/ASC],<column-name2> [DESC/ASC]
```

---

注:  
ORDER BY 子句中使用的列通常为要显示的列，但用非检索的列排序数据也是完全合法的

---

