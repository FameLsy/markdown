---
title: 使用游标(MySQL5)
tags: 
- MySQL
- 数据库
categories: 
- Database
---

# 使用游标(MySQL5)
游标(cursor)
- 存储在MySQL服务器上的数据库查询，不是SELECT语句，而是被该语句检索出来的结果集。
- 根据需要滚动或浏览其中的数据
- 主要用于交互式应用
- 只能用于存储过程（和函数）

游标的使用
- 先声明游标，这个过程没有检索数据。只用来定义要使用的SELECT语句
- 声明后，打开游标。这个过程用前面定义的SELECT语句把数据检索出来
- 对于填有数据的游标，根据需要检索各行
- 在结束游标使用时，必须关闭游标（如果不明确关闭游标，MySQL会在达到END语句时自动关闭）

创建游标
```
DECLARE 游标名 CURSOR FOR
```
示例
```java
//如,定义了名为ordernumbers的游标

CREATE PROCEDURE processorders()
BEGIN 
-- 定义游标
DECLARE ordernumbers CURSOR
FOR 
SELECT order_num FROM orders;
-- 打开游标
OPEN ordernumbers;
-- 关闭游标
CLOSE ordernumbers;
END; 
```

打开游标
```
OPEN 游标名
```
关闭游标
```
CLOSE 游标名
```
使用游标数据
```
FETCH
```
示例
```
//FETCH语句会自动从第一行开始把order_num列复制到变量o中(这里并没有操作变量o)

CREATE PROCEDURE processorders()
BEGIN 
-- 定义一个本地变量o
DECLARE o INT;
-- 定义游标
DECLARE ordernumbers CURSOR
FOR 
SELECT order_num FROM orders;
-- 打开游标
OPEN ordernumbers;
-- 获取数据
FETCH ordernumbers INTO o;
-- 关闭游标
CLOSE ordernumbers;
END;
```

该存储过程执行后，会循环检索数据，从第一行到最后一行
- FETCH在REPEAT内，所以会反复执行。根据UNTIL done END REPEAT;，直到done为真时结束循环(done默认0，表示假)
- CONTINUE HANDER：在条件出现时被执行的代码。这里表名当SQLSTATE '02000'出现时，将SET done = 1
- SQLSTATE '02000'表示一个未找到的条件，当REPEAT没有更多行出供循环而不能继续时，就会出现这个条件

```
CREATE PROCEDURE processorders()
BEGIN 
-- 定义一个本地变量o和一个布尔值变量done
DECLARE done BOOLEAN DEFAULT 0;
DECLARE o INT;

-- 定义游标
DECLARE ordernumbers CURSOR
FOR 
SELECT order_num FROM orders;

-- 定义了一个continue handler,在条件出现时将done设置成1
DECLARE CONTINUE HANDLER FOR SQLSTATE '02000' SET done=1;

-- 打开游标
OPEN ordernumbers;

REPEAT
	-- 获取数据
	FETCH ordernumbers INTO o;
UNTIL done END REPEAT;

-- 关闭游标
CLOSE ordernumbers;
END;
```

示例：
- 创建了一个ordertotals表
- 定义了一个变量t,使用ordertotal()存储过程为其复制
- 通过INSERT INTO 将o和t的值插入ordertotals表中
- 使用 CALL processorders();来使用存储过程,这时表ordertotals应该已经创建好并且有数据了
- 使用SELECT * FROM ordertotals;来查看下数据

```
CREATE PROCEDURE processorders()
BEGIN 
-- 定义一个本地变量o、t和一个布尔值变量done
DECLARE done BOOLEAN DEFAULT 0;
DECLARE o INT;
DECLARE t DECIMAL(8, 2);

-- 定义游标
DECLARE ordernumbers CURSOR
FOR 
SELECT order_num FROM orders;

-- 定义了一个continue handler
DECLARE CONTINUE HANDLER FOR SQLSTATE '02000' SET done=1;

-- 创建一个表来存储结果
CREATE TABLE IF NOT EXISTS ordertotals(
	order_num INT, 
	total DECIMAL(8, 2)
);

-- 打开游标
OPEN ordernumbers;

REPEAT
	-- 获取数据
	FETCH ordernumbers INTO o;
	-- 这里是在学习存储过程中写的一个存储过程,用来计算t的值
	CALL ordertotal(o, t);
	-- 把数据插入表
	INSERT INTO ordertotals(order_num, total)
	VALUES(o, t);
UNTIL done END REPEAT;

-- 关闭游标
CLOSE ordernumbers;
END;

CALL processorders();

SELECT * FROM ordertotals;
```