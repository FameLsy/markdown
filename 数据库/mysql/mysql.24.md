---
title: 存储过程(MySQL5)
tags: 
- MySQL
- 数据库
categories: 
- Database
---


# 存储过程(MySQL5)


创建存储过程
```CREATE PROCEDURE 存储过程名() BEGIN 语句; END
```
示例
```
CREATE PROCEDURE productpricing()
BEGIN 
SELECT avg(prod_price) AS priceaverage
FROM products;
END;
```

命令行模式下的DELIMITER //
- MySQL命令行默认使用;来表示分隔符。而MySQL语句也使用;表示。这样还没执行到END，就在前面的;就结束了。DELIMITER //是将MySQL命令行的分隔符修改。
```
DELIMITER //
CREATE PROCEDURE productpricing()
BEGIN 
SELECT avg(prod_price) AS priceaverage
FROM products;
END//
```

使用存储过程
```
CALL productpricing()
```

删除存储过程
- 过程不存在回产生错误，可使用DROP PROCEDURE IF EXISTS 存储过程名
```
DROP PROCEDURE 存储过程名
DROP PROCEDURE IF EXISTS 存储过程名
```

使用参数
- IN：传递给存储过程
- OUT：从存储过程传出
- INOUT：可传入传出

```java
//IN需要传入数据（20005）,SELECT的数据通过INTO传入到ototal中。通过SELECT @total查看变量的值。

CREATE PROCEDURE ordertotal(
	IN onumber INT,
	OUT ototal DECIMAL(8, 2)
)
BEGIN 
	SELECT sum(item_price*quantity)
	FROM orderitems
	WHERE order_num = onumber 
	INTO ototal;
END;
CALL ordertotal(20005, @total);
SELECT @total;
```
检查存储过程
```
SHOW CREATE PROCEDURE 存储过程
```
列出所有存储过程
```
WHO PROCEDURE STATUS
```
可以使用LIKE过滤,如
```
WHO PROCEDURE STATUS LIKE 'ordertotal'
```