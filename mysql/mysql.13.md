---
title: 函数
tags: 
- MySQL
- 数据库
categories: 
- Database
---

# 函数
## 常用的文本处理函数

```
Concat(): 拼接字段
Left()：返回串左边的字符
Length()：返回串的长度
Lecate()：找出串的一个子串
Lower()：将串转换为小写
Upper()：将串转换为大写
LTrim()：去掉串左边的空格
RTrim()：去掉串右边的空格
Trim()：去掉串两边的空格
Right()：返回串右边的字符
Soundex()：返回串的SOUNDEX值
SubString()：返回子串的字符
```
## 常用时间和日期处理函数
```
AddDate()：增加一个日期（天、周等）
AddTime()：增加一个时间（时、分等）
CurDate()：返回当前日期
CurTime()：返回当前时间
Date()：返回日期时间的日期部分
DateDiff()：计算两个日期之差
Date_Add()：高度灵活的日期运算函数
Date_Format()：返回一个格式化的日期或时间串
Day()：返回一个日期的天数部分
DayOfWeek()：对于一个日期，返回对应的星期几
Hour()：返回一个时间的小时部分
Minute()：返回一个时间的分钟部分
Month()：返回一个日期的月份部分
Now()：返回当前日期和时间
Second()：返回一个时间的秒部分
Time()：返回一个日期时间的时间部分
Year()：返回一个日期的年份部分
```

检索日期
```java
//假设order_date数据类型datetime，其格式为2005-09-01 21:21:53,所以需要使用Date()来提取order_date的日期部分
SELECT cust_id, order_num
FROM orders
WHERE Date(order_date) = '2005-09-01'
```


---

注意
- 日期格式必须为 yyyy-mm-dd ,如2005年9月1日，则- 2005-09-01

---

## 数值处理函数
```
Abs()：返回一个数的绝对值
Cos()：返回一个角度的余弦
Exp()：返回一个数的指数值
Mod()：返回一个除操作的余数
Pi()：返回圆周率
Rand()：返回一个随机数
Sin()：返回一个角度的正弦
Sqrt()：返回一个数的平方根
Tan()：返回一个角度的正切
```

## 聚焦函数
```
AVG()函数：返回某列的平均值(忽略值为NULL的行)  
COUNT(*):对表中行的数据进行计算，包含空值NULL 
COUNT(column):对特定列中具有值的行进行计数，忽略NULL值。  
MAX()函数：返回某列的最大值(忽略NULL值)
MIN()函数：返回某列的最小值（与MAX()相反）
SUM()：返回某列值之和（忽略NULL值）,如，计算总金额时使用
```