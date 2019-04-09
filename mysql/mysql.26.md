---
title: 触发器(MySQL5)
tags: 
- MySQL
- 数据库
categories: 
- Database
---


# 触发器(MySQL5)

什么是触发器：
 - 是MySQL响应以下任意语句自动执行的的一条MySQL语句

```
DELETE
INSERT
UPDATE
```

- 其他MySQL语句不支持触发器
- 只有表支持触发器，视图不支持
- 每个表最多支持6个触发器（每条INSERT、UPDATE、DELETE的之前之后）

触发器失败
- BEFORE触发器失败，不执行请求操作
- BEFORE触发器或语句本身失败，不执行AFTER触发器

创建触发器
```
CREATE TRIGGER
```

示例
```java
//触发器在INSERT语句成功后，对每个插入的行显示一次Product added
- 创建了名为newproduct的触发器。
- AFTER INSERT表示触发器在INSERT语句成功后执行
- FOR EACH ROW表示代码对每个插入行执行
- SLELECT 'Product added'触发器执行的操作，即显示文本信息Product added

CREATE TRIGGER newproduct AFERT INSERT ON products
FOR EACH ROW SLELECT 'Product added'
```

删除触发器
- 触发器无法更新或覆盖，想要修改触发器，必须先删再创。
```
DROP TRIGGER 触发器名
```

INSERT触发器
- 在INSERT触发器代码内，可引用一个名为NEW的虚拟表，访问被插入的行
- 在BEFROE INSERT触发器中，NEW 的值也可以被更新。
- 对于AUTO_INCREMENT列，NEW在INSERT执行之前包含0，在INSERT执行之后包含新的自动生成值。

示例
```java
//order_num是一个AUTO_INCREMENT列,当orders表成功插入一行数据时，这行数据会保存到NEW中。此触发器执行的操作是SELECT NEW.order_num,即把自动生成的标号显示出来。

CREATE TRIGGER neworder AFTER INSERT ON orders
FOR EACH ROW SELECT NEW.order_num;
```

DELETE触发器
- 在DELETE触发器代码内，可以引用名为OLD的虚拟表，访问被删除的行
- OLD中的值都是只读，无法更新
```java

//将被删除的行的数据保存到archive_orders表中。BEGIN END块的好处是触发器器能容纳多条SQL语句。

CREATE TRIGGER deleteorder BEFORE DELETE ON roders
FOR EACH ROW
BEGIN
	INSERT INTO archive_orders(order_num, order_date, cust_id)
	VALUES (OLD.order_num, OLD.order_date, OLD.cust_id)  
END;
```

UPDATE触发器
- 在UPDATE触发器代码中，可以引用名为OLD的虚拟表访问以前的值，引用名为NEW的虚拟表访问新更新的值
- BEFORE UPDATE触发器中，NEW中的值可能也被更新
OLD只读，不能更新
示例：
```java
//在更新以前出发，保证更改的值永远是大写。即在更新前，先对值进行处理。（这就是所谓的NEW值可能被更新）

CREATE TRIGGER updatevendor BEFORE UPDATE ON vendors
FOR EACH ROW SET NEW.ven_state = Upper(NEW.vend_state);
```