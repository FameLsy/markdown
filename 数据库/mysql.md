---
title: MySQL笔记
tags: 
- MySQL
- 数据库
categories: 
- Database
---

[《mysql必知必会》笔记，所使用的代码下载](https://download.csdn.net/download/masorl/10816192)
___
# 数据库相关概念
- 数据库(database)：保存有组织的数据的容器（通常事一个文件或一组文件）
- 表（table）：某种特定类型数据的结构化清单  
相同数据库无法使用相同的表名，不同数据库可以使用相同的表名
- 模式（schema）：关于数据库和表的分布及特性的信息
- 列（column）：表中的一个字段。所有表都是由一个或多个列组成的
- 数据类型（datatype）：所容许的数据的类型。每个表列都有相应的数据类型，它限制（或容许）该列中存储的数据
- 行（row）：表中的一个记录
- 主键（primary key）:一列（或一组列），其值能够唯一区分表中的每个行  

>大多数数据库设计人员都应保证他们创建的每个表具有一个主键，以便于以后的数据操纵和管理  
>列作为主键需满足:  
>&emsp;&emsp;1  任意两行都不具有相同的主键值  
>&emsp;&emsp;2  主键值不能为NULL  
>主键的好习惯：  
>&emsp;&emsp;1  不更新主键列中的值  
>&emsp;&emsp;2 不重用主键列的值  
>&emsp;&emsp;3 不在主键列中使用可能会更改的值

- 关键字（key word）：作为mysql语言组成部分的一个保留字。绝不要用关键字命名一个表或列。
- 字句（clause）：SQL语句由自居构成。一个子句通常由一个关键字和所提供的数据组成。
- NULL: 物质，与字段0、空字符串或仅仅包含空格不同。
- 操作符(operator)：用来联结或改编WHERE字句中的字句的关键字，也成为逻辑操作符。
- 字段（field）:基本上与列的意思相同，经常互换使用，不过数据库列一般称为列，而术语字段通常用在计算字段的连接上。
- 拼接（concatenate）：将值联结到一起构成单个值。
- 聚焦函数（aggregate function）：运行在行组上，计算和返回单个值的函数
- 外键（foreign key）：外键为某个表中的一列，它包含另一个表的主键值，定义了两个表之间的关系
- 可伸缩性（scale）：能够适应不断增加的工作量而不失败。设计良好的数据库或应用程序称之为可伸缩性号（scale well）
- 笛卡尔积（cartesian product）：由没有联结条件的表关系返回的结果为笛卡尔积。检索出的行的数目将是第一个表中的行数乘以第二个表中的行数。

# mysql命令行使用程序
## 连接操作：**mysql**

登录MySQL
```mysql
mysql -u root -p 
```

指定用户(-u)、密码登录(-p)、主机(-h,默认localhost)、端口(-P,3306)；
```mysql
mysql -u root -p -h myserver -P 9999
```

## 查看帮助文档：**help**
例如查看指令**SELECT**
```
help SELECT
```

## 退出操作**exit**或**quit**

# 测试服务器
- 查看mysql的一些基本信息：```/usr/bin/mysqladmin -u root -p version```
- 查看mysql的变量:```/usr/bin/mysqladmin -u root -p variables```
- 验证是否能关闭mysql:```/usr/bin/mysqladmin -u root -p shutdown```
- 再次启动Mysql:```/usr/bin/mysqld_safe --user=mysql &```
- 查看数据库:```/usr/bin/mysqlshow -u root -p```,指定数据库则显示数据库中的表，如```/usr/bin/mysqlshow -u root -p  test```
- 查看表中主句:```/usr/bin/mysql -e "SELECT prod_name FROM products" -u root -p test```
- 显示服务器状态：```service mysql status```

# 导入sql文件
命令行模式：```source xxx.sql```（指定sql文件的完全路径）
# 数据库相关
- 使用数据库：```USE```,如```USEtest```,将使用test数据库
- 查看所有数据库：```SHOW databases```
- 显示创建数据库语句：```SHOW CREATE DATABASE 数据库名```
- 
# 表相关
- 查看当前数据库所有表:```SHOW TABLES```
- 显示创建表语句：```SHOW CREATE TABLE 表```
- 查看表列(结构)：```SHOW COLUMNS FROM 表```,如```SHOW COLUMNS FROM  customers```,将列出customers表列
- 查看表列另一种方式：```DESCRIBE 表```,如```DESCRIBE customers```与```SHOW COLUMNS FROM 表```效果一样，是其一种快捷方式
- 创建表```CREATE TABLE```
>1. 创建表```CREATE TABLE 表名 (列, 列2,列3)```
>```
>CREATE TABLE customers (
> 	cust_id int(11) NOT NULL AUTO_INCREMENT,
>  	cust_name char(50) NOT NULL,
>  	cust_address char(50) DEFAULT NULL,
>  	cust_city char(50) DEFAULT NULL,
>  	cust_state char(5) DEFAULT NULL,
>  	cust_zip char(10) DEFAULT NULL,
>  	cust_country char(50) DEFAULT NULL,
 > 	cust_contact char(50) DEFAULT NULL,
>  	cust_email char(255) DEFAULT NULL,
>  	PRIMARY KEY (cust_id)
> )ENGINE=InnoDB;
> ```
> 2. 创建不存在的表```CREATE TABLE IF NOT EXISTS 表()```
> 3. ```NULL```或```NOT NULL```，在定义列时加入这两个关键字，表示允许```NULL```或不允许```NULL```,默认为```NULL```
> 4. 定义主键```PRIMARY KEY (列)```,定义组合主键```PRIMARY KEY (列1, 列2)```
> 5. 自动增量```AUTO_INCREMENT```,每次执行```INSERT```，自动对该列增量，赋值下一个可用的值。可以在```INSERT```语句中插入指定值，只要是没有使用的，该值回代替自动生成的值。
> 6. 查看自动增量的值，```SELECT last_insert_id()```,该函数回获取最后一个```AUTO_INCREATEMENT```的值
> 7. 指定默认值```DEFAULT```，如```cust_id int NOT NULL DEFAULT 1```
- 更新表```ALTER TABLE```，更改表结构
>1. 添加列```ADD```
>```
>ALTER TABLE bendors 
>ADD vend_phone CHAR(20)
>```
>2. 删除列```DROP COLUMN```
>```
>ALTER TABLE bendors 
>DROP COLUMN vend_phone
>```
>3. 定义外键
>- ```fk_orderitems_orders```：外键字段的名字
> ```FOREIGN KEY(order_num)```：当前表的字段
>```REFERENCES orders(order_num)```：对应的表的字段(```orders```表的```order_num```字段)
>```
>ALTER TABLE orderitems
>ADD CONSTRAINT fk_orderitems_orders
>FOREIGN KEY(order_num) REFERENCES orders(order_num)
>```
>4. 删除外键```ALTER TABLE DROP FOREIGN KEY 外键字段名```
- 删除表```DROP TABLE 表```
- 重命名表```RENAME TABLE 原表名 TO 更改的表名```
# 数据相关
- ```SELECT```子句顺序

|子句|说明|是否必须使用|
|--|--|--|
|SELECT|要返回的列或表达式 |是 |
|FROM|从中检索数据的表|仅在从表选择数据时使用
|WHERE|行级过滤|否
|GROUP BY|分组说明|仅在安祖计算聚焦时使用
|HAVING|组级过滤|否
|ORDER BY|输出排序顺序|否
|LIMIT|要检索的行数|否

- 检索数据:```SELECT...FROM```
>1. 检索单个数据: ```SELECT 列 FROM 表```,如
>```
>SELECT prod_name 
>FROM products
>```
>2. 检索多个列：```SELECT 列1, 列2,... FROM  表```,如
>```
>SELECT prod_id, prod_name, prod_price 
>FROM products
>```
>3. 检索所有列：```SELECT * FROM 表```,如
>```
>SELECT * 
>FROM products
>```
>4. 检索唯一值：```SELECT DISTINCT 列 FROM 表```,通过DISTINCT关键字，将SELECT检索的结果，只返回不同值，如
>```
>SELECT DISTINCT vend_id 
>FROM products
>```

- 限制结果```LIMIT```
>1. 返回n行：```LIMIT n```,如返回前5行
>```
>SELECT prod_name 
>FROM products 
>LIMIT 5
>```
>2. 从m位置开始返回n行：```LIMIT m,n```,如从第5行开始返回5行（第一行为0）
>```
>SELECT prod_name 
>FROM products 
>LIMIT 5,5
>```
>3. 从m位置开始返回n行的另一种方式：```LIMIT n OFFSET m```,相当于```LIMIT m,n```

- 完全限定命名
>1. 完全限定列名：```表.列```,如```products.pro_name```
>2. 完全限定表名：```数据库.列```,如```test.products```

- 排序检索数据```ORDER BY```（位于```WHERE```之后）
>**注**: ```ORDER BY ```子句中使用的列通常为要显示的列，但用非检索的列排序数据也是完全合法的
>1. 以字母顺序排序数据：```ORDER BY 列```,如
>```
>SELECT prod_name
> FROM products 
>ORDER BY prod_name
>```
>2. 按多个列进行排序：```ORDER BY 列1, 列2```,如 将会先根据prode_price进行排序，再根据prod_name进行排序
>```
>SELECT prod_id, prod_price, prod_name 
>FROM products
>ORDER BY prod_price, prod_name
>```
>3. 指定排序方向：```ORDER BY 列 DESC/ASC```,如,降序排序(从Z-A,大到小)
>```
>SELECT prod_id, prod_price, prod_name
>FROM products
>ORDER BY prod_price DESC
>```
>4. 指定排序方向的多个列排序：```ORDER BY 列1 [DESC/ASC] ,列2 [DESC/ASC], ...```,如
>```
>SELECT prod_id, prod_price, prod_name
>FROM products
>ORDER BY prod_price DESC,prod_name 
>```
>**注**：
>- DESC/ASC只应用到直接位于其前面的列名。
>- ASC没多大用，因为默认就是它

- 过滤数据```WHERE	```	（位于```ORDER BY```之前）
>1. 指定单个搜索条件```WHERE 列 操作符 数据```,如,检测prod_price = 2.50的行
>```
>SELECT prod_name, prod_price
>FROM products
>WHERE prod_price = 2.50
>```
>2. 条件 操作符
>```
>=：等于
><>：不等于
>!=：不等于
><：小于
><=：小于等于
>>：大于
>>=：大于等于
>BETWEEN：再指定的两个值之间
>```
>3. 范围检查：```WHERE 列 BEWEEN m AND n```,如,检查prod_price在5到10之间的（包括5和10）
>```
>SELECT prod_name, prod_price
>FROM products
>WHERE prod_price BETWEEN 5 AND 10
>```
>4. 空值检查```WHERE 列 IS NULL```,如,返回空（非空）prod_price字段的行
>```
>SELECT cust_id
>FROM customers
>WHERE cust_email IS/IS NOT NULL
>```
>5. ```AND```操作符，用于联结两个或多个条件（条件之间用AND联结），需全部满足
>```
>SELECT prod_id, prod_price, prod_name
>FROM  products
>WHERE vend_id = 1004 AND prod_price <= 10
>```
>6. ```OR```操作符,检索匹配任意条件的行,如，满足其一即可
>```
>SELECT  prod_name, prod_price
>FROM products
>WHERE vend_id = 1002 OR ven_id =1003
>```
>7. 计算次序
>- AND优先级比OR优先级高
>- 可以使用()来先进行OR操作
>8. ```IN```操作符,指定条件范围（包含头尾）
>```
>SELECT prod_name. prod_price
>FROM products
>WHERE vend_id IN (1002, 1003)
>ORDER BY prod_name
>```
>9. ```NOT```操作符,用于否定其后的条件（mysql仅支持对IN、BETWEEN、EXISTS句子取反）
>```
>SELECT prod_name, prod_price
>FROM products
>WHERE vend_id NOT IN (1002, 1003)
>ORDER BY prod_name
>```
>
>
>**注：**
>- 字符串类型用单引号''括起来
>- 值与字符串类型比较时，字符串会转为值。系统采取截取方式，截取字符串前面的数值，如```02asda```,那么就是02,而对于字母开头的,就直接转为0。
- 通配操作符```LIKE```
> 1. % 通配符：匹配0、1或多个字符,如,找出所有以```jet```开头的数据
> ```
> SELECT prod_id, prod_name
> FROM products
> WHERE prod_name LIKE 'jet%';
> ```
> 2. _通配符：匹配单个字符，如，将会匹配```1 ton anvil```,而不会匹配```.5 ton anvi```
> ```
> SELECT prod_id, prod_name 
> FROM prducts
> WHERE prod_name LIKE '_ ton  anvil'
>```
> 注: 
> - 尾空格会干扰通配符匹配。如anvil后多了一个或多个空格。```%anvil```将会不匹配到它，解决方法是在后面也加一个%.```%anvil%```或使用函数去除。
> - %无法匹配NULL值，即```LIKE '%'```不会匹配到值NULL的行。
> - 不要过度使用通配符，能使用其他操作符就使用其他操作符
> - 通配符置于开始，搜索最慢（即尽量不要使用在开头）

- 正则表达式```REGEXP```
> 1. MySQL用WHERE子句对正则表达式提供了支持,其用法与```LIKE```相似
> 2. 基本字符匹配```REGEXP 正则表达式```,如
> ```
> SELECT prod_name
> FROM products 
> WHERE prod_name REGEXP `1000`
> ORDER BY prod_name
> ```
> 3. 进行OR匹配：使用```|```,如,将匹配到包含1000或2000的行,两个以上则```1000|2000|3000|....```
> ```
> SELECT prod_name
> FROM products
> WHERE prod_name REGEXP '1000|2000'
> ORDER BY prod_name
> ```
> 4. 匹配几个字符之一,使用```[]```,如,```[123]```表示匹配1或2或3，是```[1|2|3]```的缩写，```[^123]```将匹配除这几个字符外的任何数据。```[]```相当于一个字符集合。
> ```
> SELECT prod_name
> FROM   products
> WHERE prod_name REGEXP '[123] ton'
> ORDER BY prod_name
> ```
> 5. 匹配范围
> ```
> [a-z][A-Z]：匹配字母
> [0-9]:匹配数字
> [a-z0-9]：组合使用
> ```
> 6. 匹配特殊字符,使用```\\```,如想要匹配点```.```，则```REGEXP '\\.'```,也可以用来引用元字符，如
> ```
> \\f：换页
> \\n：换行
> \\r：回车
> \\t：制表
> \\v：纵向制表
> ```
> 7. 匹配字符类,即自定义的字符集称为字符类
> ```
> [:alnum:]：任意字符和字母（同[a-zA-Z0-9]）
> [:alpha:]：任意字符(同[a-zA-Z])
> [:blank:]：空表和制表(同[\\t])
> [:cntrl:]：ASCII控制符(ASCII 0到31和127)
> [:digit:]：任意数字（同[0-9]）
> [:graph:]：任意可打印字符，与[:print:]相同，但不包括空格
> [:lower:]：任意小写字母(同[a-z])
> [:print:]：任意可打印字符，与[:graph:]相同，且包括空格
> [:punct:]：即不再[:alnum:]也不再[:cntrl:]中的任意字符
> [:upper:]：任意大写字母（同[A-Z]）
> [:xdigit:]：任意十六进制数字(同[a-fA-F0-9])
> ```
> 8. 匹配多个实力，通过泽正表达式重复元字符完成,如```[0-9][0-9][0-9][0-9]```相当于```[0-9]{4}```相当于```[:digit:]{4}```
> ```
> *：0个或1个或多个
> +：1个或多个，相当于{1,}
> ?：0个或一个，相当于{0,1}
> {n}：n个
> {n,}：n个或多于n个
> {n,m}：n~m个
> ```
> 9. 定位符，匹配特定位置的文本,如```^[0-9\\.]```，匹配以数字或小数点开始。注意```^```的位置，如果是在集合里```[^0-9\\.]```,则是否定该集合。
> ```
> ^：文本的开始
> $：文本的结尾
> [[:<:]]：词的开始
> [[:>:]]：词的结尾
> ```


- ```LIKE```与```REGEXP```的不同
> 如：
> ```LIKE '1000'```：匹配整个列的值，即要完全匹配
> ```REGEXP '1000'```：该值只要在列中出现，则匹配。相当于```LIKE '%1000%'```,想要匹配整个列，则```REGEXP '^1000$'```

- 拼接字段```Concat()```
>1. 拼接两个字段:,将会出书vend_name和vend_country的组合字段
>```
>SELECT Concat(vend_name, '(', vend_country, ')')
>FROM vendors
>ORDERBY vend_name
>```
>2. 去除右空格```RTrim()```，去除左空格```LTrim()```,去除两边空格```Trim()```,如
>```
>SELECT Concat(RTrim( vend_name, '(', vend_country, ')' ))
>FROM vendors
>ORDERBY vend_name
>```
>3. 别名```AS```,如，会将计算的字段的名字替换成```vend_title```
>```
>Concat(vend_name, '(', vend_country, ')') AS vend_title
>```
>4. 执行算术计算,如.```expanded_price```就是计算后的字段
>```
>SELECT prod_id,
>        quantity,
>	   item_price,
>	   quantity * item_price AS expanded_price
>FROM orderitems
>WHERE order_num = 20005;
>```
- 函数
>1. 常用的文本处理函数
>```
>Left()：返回串左边的字符
>Length()：返回串的长度
>Lecate()：找出串的一个子串
>Lower()：将串转换为小写
>Upper()：将串转换为大写
>LTrim()：去掉串左边的空格
>RTrim()：去掉串右边的空格
>Trim()：去掉串两边的空格
>Right()：返回串右边的字符
>Soundex()：返回串的SOUNDEX值
>SubString()：返回子串的字符
>```
>2. 常用时间和日期处理函数
>```
>AddDate()：增加一个日期（天、周等）
>AddTime()：增加一个时间（时、分等）
>CurDate()：返回当前日期
>CurTime()：返回当前时间
>Date()：返回日期时间的日期部分
>DateDiff()：计算两个日期之差
>Date_Add()：高度灵活的日期运算函数
>Date_Format()：返回一个格式化的日期或时间串
>Day()：返回一个日期的天数部分
>DayOfWeek()：对于一个日期，返回对应的星期几
>Hour()：返回一个时间的小时部分
>Minute()：返回一个时间的分钟部分
>Month()：返回一个日期的月份部分
>Now()：返回当前日期和时间
>Second()：返回一个时间的秒部分
>Time()：返回一个日期时间的时间部分
>Year()：返回一个日期的年份部分
>```
>3. 检索日期,（本例中```order_date```数据类型datetime，格式为```2005-09-01 21:21:53```,所以需要使用```Date()```来提取```order_date```的日期部分）
>```
>SELECT cust_id, order_num
>FROM orders
>WHERE Date(order_date) = '2005-09-01'
>```
>4. 数值处理函数
>```
>Abs()：返回一个数的绝对值
>Cos()：返回一个角度的余弦
>Exp()：返回一个数的指数值
>Mod()：返回一个除操作的余数
>Pi()：返回圆周率
>Rand()：返回一个随机数
>Sin()：返回一个角度的正弦
>Sqrt()：返回一个数的平方根
>Tan()：返回一个角度的正切
>```
>**注意**：
>- 日期格式必须为```yyyy-mm-dd```,如2005年9月1日，则```2005-09-01```
- 聚焦函数
>1. AVG()函数：返回某列的平均值(忽略值为NULL的行)
>```
>SELECT AVG(prod_price) AS avg_price
>FROM products
>```
>2. COUNT()函数：返回某列的行数。
>```COUNT(*)```对表中行的数据进行计算，包含空值NULL
>```COUNT(column)```对特定列中具有值的行进行计数，忽略NULL值。
>```
>SELECT COUNT(*) AS num_cust
>FROM customers
>```
>3. MAX()函数：返回某列的最大值(忽略NULL值)
>```
>SELECT MAX(prod_price) AS max_price
>FROM products
>```
>4. MIN()函数：返回某列的最小值（与MAX()相反）
>5. SUM()：返回某列值之和（忽略NULL值）,如，计算总金额时使用
>```
>SELECT SUM（item_price*qunantity）AS total_price
>FROM orderitems
>WHERE order_num = 20005
>```
>6. 聚焦不同值,```DISTINCT```参数保证值不同，```ALL```参数为默认值
>```DISTINCT```只能用于列名，不能用在计算或表达式
>```
>SELECT AVG(DISTINCT prod_price) AS avg_price
>FROM products
>WHERE vend_id = 1003
>```
>7.组合聚焦函数
>```
>SELECT COUNT(*) AS num_items,
>		MIN(prod_price) AS price_min,
>		MAX(prod_price) AS price_max,
>		AVG(prod_price) AS price_avg
>FROM products
>```
- 数据分组```GROUP BY```(```WHERE```子句之后，```ORDER BY```之前)
>1. 创建分组,通过```GROUP BY```将数据按```vend_id```分组后，COUNT（*）将会对分组后的数据分别计算一次。
>```
>SELECT vend_id, COUNT(*) AS num_prods
>FROM products
>GOURP BY vend_id
>```
>
- 过滤分组```HAVING```
>1. ```WHERE```是过滤行，而```HAVING```是过滤分组
>2. ```WHERE```是在分组前过滤，而```HAVING```是在分组后过滤
>3. HAVING支持所有WHERE操作符
>4. 过滤分组,按```cust_id```分组，然后过滤出行数大于等于2组。
>```
>SELECT cust_id, COUNT(*) AS orders
>FROM orders
>GROUP BY cust_id
>HAVING count(*) >= 2
>```
- 子查询，即嵌套在其他查询中的查询
>1. 利用子查询过滤
>将一条```SELECT```语句返回的结果用于另一条```SELECT```语句的```WHERE```子句
>```
>SELECT cust_id
>FROM orders
>WHERE order_num IN (SELECT order_num 
>		FROM orderitems 
>		WHERE prod_id = 'TNT2')
>```
>2. 作为计算字段使用子查询
>```
>SELECT cust_name,
>		cust_state.
>		(SELECT COUNT(*)
>		FROM orders
>		WHERE orders.cust_id = customers.cust_id) AS orders
>FROM customers
>ORDER BY cust_name
>```
- 联结
>1. 当数据存储在多个表中，使用联结方式，在单条SELECT 语句检索出数据。
>2. 创建联结
>引用列可能出现二义性，必须使用完全限定列名
>使用```WHERE```防止笛卡尔积
>```
>SELECT vend_name, prod_name, prod_price
>FROM vendors, prodcuts
>WHERE vendors.vend_id = products.vend_id
>ORDER BY vend_name , prod_name
>```
>3. 内部联结
>例与第一种```WHERE```方式返回数据相同
>使用```INNER JOIN ```指定两表关系，使用```ON```代替```WHERE```
>```ANSI SQL```规范首选```INNER JOIN```语法
>```
>SELECT vend_name, prod_name, prod_price
>FROM vendors INNER JOIN products
>ON vendors.vend_id = products.vend_id
>ORDER BY vend_name , prod_name
>```
>4. 使用表别名(可以省写表名)
>```
>SELECT cust_name, cust_contact
>FROM customers AS c, orders AS o, orderitems AS oi
>WHERE c.cust_id = o.cust_id
>AND oi.order_num = o.order_num
>AND prod_id = 'TNT2'
>```
>5. 自联结
>使用情况，如一个货物id为DTNTR,想要查询这个货物对应厂商的其他货物，就需要使用自联结了。（p1、p2实际为一张表）
>第一步：查询到id为DTNTR对应的vend_id
>第二步：查询到vend_id对应的prod_id, prod_name
>```
>SELECT p1.prod_id, p1.prod_name
>FROM products AS p1, products AS p2
>WHERE p1.vend_id = p2.vend_id
>AND p2.prod_id = 'DTNTR'
>```
>6. 自然联结
>使用情况，在对表进行联结时，肯定会有至少一个列的值时重复的。而自然联结，就是排除多次出现的列。使每个列只返回一次。具体做法为对一张表使用```SELECT *```,其他表的列使用明确的子集
>```
>SELECT c.*, 
>		o.order_num,
>		o.order_date,
>		oi.prod_id, 
>		oi.quantity, 
>		oi.item_price
>FROM customers AS c, orders AS o, orderitems AS oi
>WHERE c.cust_id = o.cust_id
>AND oi.order_num = o.order_num
>AND prod_id = 'FB'
>```
>7. 外部联结```LEFT/RIGHT OUTER JOIN```
>外部联结会包括没有关联行的行（即可查询到NULL值的行）
>如，```LEFT OUTER JOIN```表示左边表选择所有行(```customers```表),如果是```INNER JOIN```时不会检测出```10002 |      NULL ```这一行。
>```
>SELECT customers.cust_id, orders.order_num
>FROM customers LEFT OUTER JOIN orders
>ON customers.cust_id = orders.cust_id
>```
- 组合查询```UNION```
>1. 使用组合查询的两种情况
>在单个查询中从不同的表返回类似结构的数据
>对单个表执行多个查询，按单个查询返回数据
>2. 创建组合查询:如，通过```UNION```连接两个```SELECT```
>```
>SELECT vend_id, prod_id, prod_price
>FROM products
>WHERE prod_price <=5
>UNION
>SELECT vend_id, prod_id, prod_price
>FROM products
>WHERE vend_id IN(1001, 1002)
>```
>3. UNION规则
>  ```UNION```必须由两条或以上```SELECT```语句组成，语句之间用```UNION```分割
>  ```UNION```中的每个查询必须包含相同列、表达式或聚集函数（次序可以不同）
>  列数据列下必须兼容。（可不必完全相同，但必须隐含转换的类型）
>  4. 包含或取消重复的行
>  ```UNION```：自动去除重复的行
>  ```UNION ALLL```：返回全部的行
>  5. 对组合查询结果排序,在最后一个```SELECT```后面加```ORDER BY```，将排序所有```SELECT```语句返回的结果。

- 全文本搜索,搜索```text```数据（```MyISAM```支持，```InnoDB```不支持）
>1. 启用全文本搜索```FULLTEXT```
>Mysql会根据FULLTEXT（）的指示对它进行索引，自动维护该索引。在增加、更新或删除行时，索引随之自动跟新。
>可以在创建表时指定```FULLTEXT```（不推荐），也可以稍后指定。
>先导入数据，在定义```FULLTEXT```，有助于更快导入索引（更新索引需要时间）
>```
>CREATE TABLE productnotes
>(
>	note_id int NOT NULL ATUO_INCREMENT,
>	prod_id char(10) NOT NULL,
>	note_date datetime NOT NULL,
>	note_nect text NULL,
>	PRIMARY KEY(note_id),
>	FULLTEXT(note_text)
>) ENGINE = MyISAM
>```
>2. 进行全文本搜索```Match()```和```Against()```
>```Match()```指定所搜的列，```Against()```指定搜索表达式
>如，将会搜索包含```rabbit```的文本,不区分大小写
>```
>SELECT note_text
>FROM productnotes
>WHERE Match(note_text) Against('rabbit')
>```
>3. 与```LIKE```的区别
>如，也可以完成2中的搜索，但```LIKE```以不特别有用的顺序返回数据。全文本搜索返回以文本匹配的良好程度排序的顺序(如```rabbit```作为第3个词的行等级比作为第20个词的行高)
>```
>SELECT note_text
>FROM productnotes
>WHERE note_text LIKE '%rabbit%'
>```
>4. 查询扩展```WITH QUERY EXPANSION```
>用来放宽返回的全文本搜索结果的范围
>首先进行基本的全文本搜索，找出匹配的行，
>其次，检查这些匹配行并选择所有有用的词，
>最后，再次使用有用的词进行一次全文本上搜索。
>```
>SELECT note_text
>FROM productnotes
>WHERE Match(note_text) Against('anvils' WITH QUERY EXPANSION)
>```
>5. 布尔文本搜索```IN BOLEAN MODE```，可以提供内容细节
>要匹配的词
>要排斥的词（如果某行包含这个词，则不反回改行。即使包含匹配的词）
>排列提示（指定某些词比其他词更重要）
>表达式分组
>另外一些内容
>如，匹配```heavy```,但排除任何以```rope```开始的词或行
>```
>SELECT note_text
>FROM productnotes
>WHERE Match(note_text) Against ('heavy -rope*' IN BOOLEAN MODE)
>```
>6.全文本布尔操作符
>```
>+：包含，词必须存在
>-：排除，词必须不出现
>>：包含，而且增加等级
><：包含，而且减少等级
>()：把词组成子表达式（允许子表达式作为一个组被包含、排除、排列）
>~：取消一个词的排序值
>*：词尾通配符
>""：定义一个短语（与单个词的列表不一样，它匹配整个短语以便包含或排除这个短语）
>```
>如,包含```rabbit```和```bait```的行
>```
>SELECT note_text
>FROM productnotes
>WHERE Match(note_text) Against('+rabbit +bait' IN BOOLEAN MODE)
>```
>如,至少包含```rabbit```和```bait```其中一个的行
>```
>SELECT note_text
>FROM productnotes
>WHERE Match(note_text) Against('rabbit bait' IN BOOLEAN MODE)
>```
>如,匹配短语```rabbit bait```而不是匹配两个词
>```
>SELECT note_text
>FROM productnotes
>WHERE Match(note_text) Against('"rabbit bait"' IN BOOLEAN MODE)
>```
>如,至少包含```rabbit```和```bait```其中一个的行，增加```rabbit```的等级，降低```bait```的等级
>```
>SELECT note_text
>FROM productnotes
>WHERE Match(note_text) Against('>rabbit <bait' IN BOOLEAN MODE)
>```
>如,至少包含```safe ```和```combination```其中一个的行，降低```combination```的等级
>```
>SELECT note_text
>FROM productnotes
>WHERE Match(note_text) Against('+safe +(<combination)' IN BOOLEAN MODE)
>```
>7. 全文本搜索使用说明
>- 在索引全文本数据时，短词被忽略且从索引中排除。（短词：3个或3个一下字符的词）
>- MySQL带有一个内建的非用词列表，这些词在索引时被忽略
>- 高频词汇会被当做非用词（一个词出现在50%以上的行,该规则不用于IN BOOLEAN MODE）
>- 如果表中的行数少于3行，则全文本搜索不反回结果(要么没有，要么至少出现在50%行中)
>- 忽略此种的单引号。如```don't```索引为```dont```
>- 不具有此分隔符的语言不能恰当地返回全文本搜索结果（汉语、日语）
>- 仅在MyISAM引擎中支持全文本搜索

- 插入数据```INSERT```
>1. 插入完整的行,```INSERT INTO 表(列1, 列2, 列3,...) VALUES()```
>- 列可以不安次序，但值与列次序一一对应，没有值的使用NULL值
>- 可以省略某些列，但该列必须允许NULL值或在表定义中给出了默认值
>- 提高性能：```INSERT INTO```耗时，且可能降低等待处理的```SELECT```语句的性能。如果数据检索是最重要的，可以使用```INSERT LOW_PRIORITY INTO```来降低```INSERT```语句的优先级(同样适用```UPDATE```和```DELETE```)
>2. 插入多个行：每组值用一堆圆括号括起来，用逗号分隔
>```
>INSERT INTO customers(
>	cust_name,
>	cust_address,
>	cust_state
>	......
>)
>VALUES(
>	'adsada',
>	'wewew',
>	'wwead',
>......
>),
>(
>	'adsada',
>	'wewew',
>	'wwead',
>	......
>),
>(......)
>```
>3.插入检索出的数据```INSERT  SELECT ```
>如，完成将```custnew```表中的数据插入到```customers```表中,```SELECT```语句可包含```WHERE```子句。
>```
>INSERT INTO customers(
>	cust_name,
>	cust_address,
>	cust_state
>	......
>)
>SELECT cust_name,
>	cust_address,
>	cust_state,
>	...
> FROM custnew;
- 更新数据```UPDATE```
>**注意**
>- 不要省略WHERE子句，因为稍不注意，可能更新表中所有行
>1. 更新数据```UPDATE 表 SET 列1=值1,列2=值2,列3=值3... WHERE```
>```
>UPDATE customers
>SET cust_email = '45545@qq.com',
>cust_name = 'msy'
>WHERE cust_id = 10005
>```
>2. 忽略错误```UPDATE INGNORE```
>- ```UPDATE```在更新时如果出现错误，整个操作都会被取消
>- ```UPDATE INGNORE```可以忽略错误，继续更新
>3. 删除某个列的值,可以设置为```NULL```(假设允许NULL)
- 删除数据```DELETE```
>**注意**
>- 不要省略WHERE子句，因为稍不注意，可能删除表中所有行
>- ```DELETE```删除的是整行。想要删除列，还是要用```UPDATE```
>1. 删除行，如。删除id=10005的行
>```
>DELETE FROM customers
>WHERE cust_id = 10005
>```
>2.删除所有行```DELETE FROM 表```
>- 更快的删除：```TRUNCATE TABLE 表```,实际上是删除原表重新创建，而不是逐行删除。所以比```DELETE```快
- 更新和删除所遵循的习惯
>1. 除非打算更新和删除所有行。否则不要用不带```WHERE```的```UPDATE```、```DELETE```语句
>2. 保证每个表都有逐渐，尽可能像````WHERE```子句那样使用它
>3. 对```UPDATE```和```DELETE```语句使用WHERE子句前，先使用```SELECT```进行测试，保证正确的记录。
>4. 使用强制实施完整性的数据库，这样MySQL将不允许删除具有与其他表相关联的数据的行。
>5. MySQL没有撤销按钮，所以要小心使用```UPDATE```和```DELETE```
# 视图（MySQL5 ）
- 什么是视图
>1. 视图：
>- 将一个```SELECT```的查询结果包装成一个虚拟的表
>- 本身不包含数据，仅仅用来查看存储在别处的数据的一种设施。
>2. 好处：
>- 重复SQL语句
>- 鸡蛋花复杂的SQL操作。在编写查询后，可以方便地重用它而不必知道它的基本查询细节
>- 使用表的组成部分而不是整个表
>- 保护数据，可以给用户授予表的特定部分的访问权限而不是整个表的访问权限
>- 更改数据格式和表示。视图可返回与底层表的表示和格式不同的数据。
>3. 规则和限制
>- 与表一样，视图名唯一
>- 对于可创建的视图数目没有限制
>- 为了创建视图，必须具有足够的访问权限
>- 可以嵌套，即可以利用从其他视图中检索数据的查询来构造一个视图
>- ```ORDER BY```可以用在视图中。但如果从该视图检索数据```SELECT```中也含有```ORDER BY```,那么视图中的```ORDER BY```被覆盖。
>- 视图不能索引，也不能由关联的触发器或默认值
>- 视图可以和表一起使用。例如编写一条联结表和视图的```SELECT```语句
- 使用视图
>1. 创建视图```CREATE VIEW 视图名```
>```
>CREATE VIEW prdouccustomers AS
>SELECT  cust_name, cust_contact, prod_id
>FROM customers, orders, orderitems
>WHERE customers.cust_id = orders.cust_id
>AND orderitems.order_num = orders.order_num
>```
>2. 查看创建视图的语句```SHOW CREATE VIEW viewname```
>3. 更新视图
>- 方式一：先```DROP```在```CREATE```
>- 方式二：```CREATE OR REPLACE VIEW```，不存在创建，存在覆盖
> 4. 个人理解：
>- 创建完视图后，发现通过```SHOW TABLES```可以查看到视图（暂且理解为视图其实算是一种特殊的表）
>- 通过```SHOW CREATE TABLE 视图```,也可以显示视图的创造语句（更加觉得视图是一种特殊的表）
>- 在实际使用中，可以当表来进行操作(```WHERE```，```ORDER BY```等)
>- 虽然视图也可以做到更新（改动基表），但视图本身是用于检索，所以想要更新数据，还是直接操作基表比较好
# 存储过程(MySQL5)
- 使用存储过程
>1. 创建存储过程```CREATE PROCEDURE 存储过程名() BEGIN 语句; END```
>```
>CREATE PROCEDURE productpricing()
>BEGIN 
>SELECT avg(prod_price) AS priceaverage
>FROM products;
>END;
>```
>2. 命令行模式下的```DELIMITER //```
>- MySQL命令行默认使用```;```来表示分隔符。而MySQL语句也使用```;```表示。这样还没执行到```END```，就在前面的```;```就结束了。```DELIMITER //```是将MySQL命令行的分隔符修改。
>```
>DELIMITER //
>CREATE PROCEDURE productpricing()
>BEGIN 
>SELECT avg(prod_price) AS priceaverage
>FROM products;
>END//
>```
>3. 使用存储过程```CALL```
>```
>CALL productpricing()
>```
>4. 删除存储过程```DROP PROCEDURE 存储过程名```,过程不存在回产生错误，可使用```DROP PROCEDURE IF EXISTS 存储过程名```,
>```
>DROP PROCEDURE productpricing;
>DROP PROCEDURE IF EXISTS productpricing;
>```
>5. 使用参数
>- ```IN```：传递给存储过程
>- ```OUT```：从存储过程传出
>- ```INOUT```：可传入传出
>如, ```IN```需要传入数据（20005）,```SELECT```的数据通过```INTO```传入到```ototal```中。通过```SELECT @total```查看变量的值。
>```
>CREATE PROCEDURE ordertotal(
>	IN onumber INT,
>	OUT ototal DECIMAL(8, 2)
>)
>BEGIN 
>	SELECT sum(item_price*quantity)
>	FROM orderitems
>	WHERE order_num = onumber 
>	INTO ototal;
>END;
>CALL ordertotal(20005, @total);
>SELECT @total;
>```
>6. 检查存储过程```SHOW CREATE PROCEDURE 存储过程```
>7. 列出所有存储过程```WHO PROCEDURE STATUS```,可以使用```LIKE```过滤,如```WHO PROCEDURE STATUS LIKE 'ordertotal'```
# 使用游标(MySQL5)
- 游标(cursor)
>- 存储在MySQL服务器上的数据库查询，不是```SELECT```语句，而是被该语句检索出来的结果集。
>- 根据需要滚动或浏览其中的数据
>- 主要用于交互式应用
>- 只能用于存储过程（和函数）
- 游标的使用
>1. 先声明游标，这个过程没有检索数据。只用来定义要使用的```SELECT```语句
>2. 声明后，打开游标。这个过程用前面定义的```SELECT```语句把数据检索出来
>3. 对于填有数据的游标，根据需要检索各行
>4. 在结束游标使用时，必须关闭游标（如果不明确关闭游标，MySQL会在达到```END```语句时自动关闭）
- 创建游标```DECLARE 游标名 CURSOR FOR```
>如,定义了名为```ordernumbers```的游标
>```
>CREATE PROCEDURE processorders()
>BEGIN 
>-- 定义游标
>DECLARE ordernumbers CURSOR
>FOR 
>SELECT order_num FROM orders;
>-- 打开游标
>OPEN ordernumbers;
>-- 关闭游标
>CLOSE ordernumbers;
>END; 
- 打开游标```OPEN 游标名```.如```OPEN ordernumbers```
- 关闭游标```CLOSE 游标名```,如```CLOSE ordernumbers```
- 使用游标数据```FETCH```
>如,```FETCH```语句会自动从第一行开始把```order_num```列复制到变量```o```中(这里并没有操作变量```o```)
>```
>CREATE PROCEDURE processorders()
>BEGIN 
>-- 定义一个本地变量o
>DECLARE o INT;
>-- 定义游标
>DECLARE ordernumbers CURSOR
>FOR 
>SELECT order_num FROM orders;
>-- 打开游标
>OPEN ordernumbers;
>-- 获取数据
>FETCH ordernumbers INTO o;
>-- 关闭游标
>CLOSE ordernumbers;
>END;
>```
>如：该存储过程执行后，会循环检索数据，从第一行到最后一行
>- ```FETCH```在```REPEAT```内，所以会反复执行。根据```UNTIL done END REPEAT;```，直到```done```为真时结束循环(```done```默认0，表示假)
>- ```CONTINUE HANDER```：在条件出现时被执行的代码。这里表名当```SQLSTATE '02000'```出现时，将```SET done = 1```
>- ```SQLSTATE '02000'```表示一个未找到的条件，当```REPEAT```没有更多行出供循环而不能继续时，就会出现这个条件
>```
>
>CREATE PROCEDURE processorders()
>BEGIN 
>-- 定义一个本地变量o和一个布尔值变量done
>DECLARE done BOOLEAN DEFAULT 0;
>DECLARE o INT;
>
>-- 定义游标
>DECLARE ordernumbers CURSOR
>FOR 
>SELECT order_num FROM orders;
>
>-- 定义了一个continue handler,在条件出现时将done设置成1
>DECLARE CONTINUE HANDLER FOR SQLSTATE '02000' SET done=1;
>
>-- 打开游标
>OPEN ordernumbers;
>
>REPEAT
>	-- 获取数据
>	FETCH ordernumbers INTO o;
>UNTIL done END REPEAT;
>
>-- 关闭游标
>CLOSE ordernumbers;
>END;
>```
>如，
>- 创建了一个```ordertotals```表
>- 定义了一个变量```t```,使用```ordertotal()```存储过程为其复制
>- 通过```INSERT INTO ```将```o```和```t```的值插入```ordertotals```表中
>- 使用 ```CALL processorders();```来使用存储过程,这时表```ordertotals```应该已经创建好并且有数据了
>- 使用```SELECT * FROM ordertotals;```来查看下数据
>```
>
>CREATE PROCEDURE processorders()
>BEGIN 
>-- 定义一个本地变量o、t和一个布尔值变量done
>DECLARE done BOOLEAN DEFAULT 0;
>DECLARE o INT;
>DECLARE t DECIMAL(8, 2);
>
>-- 定义游标
>DECLARE ordernumbers CURSOR
>FOR 
>SELECT order_num FROM orders;
>
>-- 定义了一个continue handler
>DECLARE CONTINUE HANDLER FOR SQLSTATE '02000' SET done=1;
>
>-- 创建一个表来存储结果
>CREATE TABLE IF NOT EXISTS ordertotals(
>	order_num INT, 
>	total DECIMAL(8, 2)
>);
>
>-- 打开游标
>OPEN ordernumbers;
>
>REPEAT
>	-- 获取数据
>	FETCH ordernumbers INTO o;
>	-- 这里是在学习存储过程中写的一个存储过程,用来计算t的值
>	CALL ordertotal(o, t);
>	-- 把数据插入表
>	INSERT INTO ordertotals(order_num, total)
>	VALUES(o, t);
>UNTIL done END REPEAT;
>
>-- 关闭游标
>CLOSE ordernumbers;
>END;
>
>CALL processorders();
>
>SELECT * FROM ordertotals;
>```

# 触发器(MySQL5)

- 触发器
> 1.什么是触发器：
> - 是MySQL响应以下任意语句自动执行的的一条MySQL语句
> ```DELETE```
>```INSERT```
>```UPDATE```
>- 其他MySQL语句不支持触发器
>- 只有表支持触发器，视图不支持
>- 每个表最多支持6个触发器（每条```INSERT、UPDATE、DELETE```的之前之后）
>2. 触发器失败
>- ```BEFORE```触发器失败，不执行请求操作
>- ```BEFORE```触发器或语句本身失败，不执行```AFTER```触发器
>3
- 创建触发器```CREATE TRIGGER```
>如，触发器在```INSERT```语句成功后，对每个插入的行显示一次```Product added```
>- 创建了名为```newproduct```的触发器。
>- ```AFTER INSERT```表示触发器在```INSERT```语句成功后执行
>- ```FOR EACH ROW```表示代码对每个插入行执行
>- ```SLELECT 'Product added'```触发器执行的操作，即显示文本信息```Product added```
>```
>CREATE TRIGGER newproduct AFERT INSERT ON products
>FOR EACH ROW SLELECT 'Product added'
>```
- 删除触发器```DROP TRIGGER 触发器名```
>触发器无法更新或覆盖，想要修改触发器，必须先删再创。
- ```INSERT```触发器
>- 在```INSERT```触发器代码内，可引用一个名为```NEW```的虚拟表，访问被插入的行
>- 在```BEFROE INSERT```触发器中，```NEW ```的值也可以被更新。
>- 对于```AUTO_INCREMENT```列，```NEW```在```INSERT```执行之前包含0，在```INSERT```执行之后包含新的自动生成值。
>- 如，```order_num```是一个```AUTO_INCREMENT```列,当```orders```表成功插入一行数据时，这行数据会保存到```NEW```中。此触发器执行的操作是```SELECT NEW.order_num```,即把自动生成的标号显示出来。
>```
>CREATE TRIGGER neworder AFTER INSERT ON orders
>FOR EACH ROW SELECT NEW.order_num;
>```
- ```DELETE```触发器
>- 在```DELETE```触发器代码内，可以引用名为```OLD```的虚拟表，访问被删除的行
>- OLD中的值都是只读，无法更新
>- 如,将被删除的行的数据保存到```archive_orders```表中。```BEGIN END```块的好处是触发器器能容纳多条SQL语句。
>```
>CREATE TRIGGER deleteorder BEFORE DELETE ON roders
>FOR EACH ROW
>BEGIN
>	INSERT INTO archive_orders(order_num, order_date, cust_id)
>	VALUES (OLD.order_num, OLD.order_date, OLD.cust_id)  
>END;
- ```UPDATE```触发器
>- 在```UPDATE```触发器代码中，可以引用名为```OLD```的虚拟表访问以前的值，引用名为```NEW```的虚拟表访问新更新的值
>- ```BEFORE UPDATE```触发器中，```NEW```中的值可能也被更新
>```OLD```只读，不能更新
>- 如,在更新以前出发，保证更改的值永远是大写。即在更新前，先对值进行处理。（这就是所谓的```NEW```值可能被更新）
>```
>CREATE TRIGGER updatevendor BEFORE UPDATE ON vendors
>FOR EACH ROW SET NEW.ven_state = Upper(NEW.vend_state);
# 事务处理
- 事务处理
>1. 什么是事务
>- 可以用来维护数据库的完整性。保证成批的MySQL操作要么完全执行，要么完全不执行
>- ```InnoDB```引擎支持
>2. 相关术语
>- 事务（transaction）：指一组SQL语句；
>- 回滚（rollback）：撤销指定SQL语句的过程
>- 提交（commit）：将为存储的SQL语句结果写入数据库表
>- 保留点（savepoint）：指事务处理中设置的临时占位符（placeholder），可以对它发布回滚（与回滚整个事务处理不同）
- 开始事务```START TRANSACTION```
- 回滚事务```ROLLBACK```
>- 只能在一个事务处理内使用（在执行一条```START TRANSACTION```命令之后）
>- 事务处理可以用来管理```INSERT、UPDATE、DELETE```
>- 不能回滚```CREATE```和```DROP```操作
- 提交事务```COMMIT```
>- 数据库默认是自动提交的
>- 但在事务处理快中，不会自动提交，需要手动```COMMIT```
- 关闭事务：执行```COMMIT```和```ROLLBACK```后自动关闭
- 保留点```SAVEPOINT```：可以回滚到事务的某个部分
>1. 设置保留点：```SAVEPOINT point```
>2. 回滚到保留点：```ROLLBACK TO point```
>3. 释放保留点（执行完```ROLLBACK或COMMIT```后自动释放，或```RELEASE SAVEPOINT```明确释放）
- 更改系统默认的提交行为 ```SET autocommit=0```,0表示不自动提交。Mysql默认自动提交。
# 字符集
- 重要术语
>- 字符集：字母和符号的集合
>- 编码：某个字符集成员的内部表示
>- 校对：规定字符如何比较的指令
- 查看Mysql支持的字符集完整列表```SHOW CHARACTER SET```
>显示所有可用的字符集以及每个字符集的描述和默认校对
- 查看Mysql支持的校对完整列表```HOW COLLATION```
- 查看数据库默认的字符集```SHOW VARIABLES LIKE 'character%'```
- 查看数据库默认的校对：```SHOW VARIABLES LIKE 'collation%'```
- 在创建表时指定字符集和校对
>```
>CREATE TABLE mytable(
>	column1 INT,
>	column2 VARCHAR(10)
>) DEFAULT CHARACTER SET hebrew
>   COLLATE hebrew_general_ci;
>   ```
- MySQL使用字符集和校对规则
>- 明确指定两个，则使用
>- 只指定```CHARACTER SET```，则使用此字符集及其默认的校对
>- 都不知道，则使用数据库默认
- 列级别的字符和校对
> ```
> CRETE TABLE mytable(
> column3 VARCHAR(10) CHARACTER SET latin1 COLLATE latin1_general_ci
> ) DEFAULT CHARACTER ser HEBREW
>   COLLATRE hebrew_general_ci
> ```
# 用户相关
- 日常开发不应使用root用户
- 获取所有账号列表
>系统的```mysql```数据库存放了用户信息表```user```
>```
>USE mysql;
>SELECT user FORM user;
>```
- 创建用户```CREATE USER 用户名```
- 创建用户并指定密码```CREATE USER 用户名 IDENTIFIED BY '密码'```
- 重命名```RENAME USER 原名 TO 更改名```
- 设置/更新密码```SET PASSWORD```
>1. 设置指定用户密码，```Password（）```函数加密
>```
>SET PASSWORD FOR famel = Password('123456')
>```
>或设置当前登录用户
>```
>SET PASSWORD =Password('123456')
>```
- 删除账号```DROP USER 用户名```
- 设置访问权限
> 1.  新用户没有任何权限，只能登录MySQL
>2. 查看用户权限```SHOW GRANTS FOR 用户名```,如，一开始没有任何权限
>![在这里插入图片描述](https://img-blog.csdnimg.cn/20181202142417920.png)
>2. 授权```GRANT 权限 ON 数据库.表 TO 用户```
>如，赋予了```famle```用户关于数据库```crashcourse```下所有表的```SELECT```权限
>```
>GRANT SELECT ON crashcourse.* TO famel
>```
>4. 授权多次,权限用```,```分隔
>```
>GRANT SELECT, INSERT ON database.table TO famel;
>```
- 删除权限```REVOKE 权限 ON 表 FROM 用户```
> ```GRENT```和```REVOKE```可以在几个层次上控制访问权限
>- 整个服务器，```GRANT ALL```和```REVOKE ALL```
>- 整个数据库,```ON database.*```
>- 特定表,```ON database.table```
>- 特定列
>- 特定存储过程
>```
- 可用权限

|权 限| 说 明|
|--|--|
| ALL| 除GRANT OPTION外的所有权限|
| ALTER|使用ALTER TABLE |
| ALTER ROUTINE|使用ALTER PROCEDURE|DROP PROCEDURE |
|CREATE | 使用CREATE TABLE|
|CREATE ROUTINE |使用CREATE PROCEDURE |
| CREATE TEMPORARYTABLES|使用CREATE TEMPORARY TABLE |
|CREATE USER |使用CREATE USER、DROP USER、RENAME USER和REVOKE
ALL PRIVILEGES|
|CREATE VIEW | 使用CREATE VIEW|
|DELETE | 使用DELETE|
| DROP| 使用DROP TABLE|
| EXECUTE|使用CALL和存储过程 |
| FILE| 使用SELECT INTO OUTFILE和LOAD DATA INFILE|
| GRANT OPTION| 使用GRANT和REVOKE|
|INDEX |使用CREATE INDEX和DROP INDEX |
|INSERT | 使用INSERT|
|LOCK TABLES |使用LOCK TABLES |
|PROCESS | 使用SHOW FULL PROCESSLIST|
|RELOAD |使用FLUSH |
| REPLICATION CLIENT|服务器位置的访问 |
|REPLICATION SLAVE |由复制从属使用 |
|SELECT |使用SELECT |
| SHOW DATABASES|使用SHOW DATABASES |
| SHOW VIEW| 使用SHOW CREATE VIEW|
|SHUTDOWN | 使用mysqladmin shutdown（用来关闭MySQL）|
| SUPER| 使用CHANGE MASTER、KILL、LOGS、PURGE、MASTER
和SET GLOBAL。还允许mysqladmin调试登录|
|UPDATE |使用UPDATE |
|USAGE | 无访问权限|
# 数据维护
- 检查表键是否正确```ANALYZE TABLE 表```
- 检查表```CHECK TABLE tbl_name [, tbl_name][option]```.
>1. 针对许多问题对标进行检查，在MyISAM表上还对索引进行检查
>1. ```CHECK TABLE 表```：检查表
>2. 附加选项：
>- ```CHANGED ```:检查最后一次检查以来改动的表
>- ```EXTENDED```：执行最彻底的检查
>- ```FAST```：只检查未正常关闭的表
>```MEDIUM```：检查所有被三处的链接并进行键检查
>```QUICK```：快速扫描

# 数据类型
- 传数据类型

|数据类型| 说明 |
|--|--|
| CHAR | 1～255个字符的定长串。它的长度必须在创建时指定，否则MySQL假定为CHAR(1) |
| ENUM | 接受最多64 K个串组成的一个预定义集合的某个串 |
| LONGTEXT | 与TEXT相同，但最大长度为4 GB |
|MEDIUMTEXT  | 与TEXT相同，但最大长度为16 K |
| SET | 接受最多64个串组成的一个预定义集合的零个或多个串 |
| TEXT | 最大长度为64 K的变长文本 |
| TINYTEXT | 与TEXT相同，但最大长度为255字节 |
|VARCHAR  | 长度可变，最多不超过255字节。如果在创建时指定为VARCHAR(n)m则可存储0到n个字符的变长串（其中n≤255） |
- 数值数据类型
>MySQL中没有专门存储货币的数据类型，一般情况下使用```DECIMAL(8, 2)```

|数据类型  | 说 明 |
|--|--|
|BIT  |位字段，1～64位。（在MySQL 5之前，BIT在功能上等价于TINYINT  |
| BIGINT | 整数值，支持9223372036854775808～9223372036854775807（如果是UNSIGNED，为0～18446744073709551615）的数 |
|BOOLEAN（或BOOL）  |布尔标志，或者为0或者为1，主要用于开/关（on/off）标志  |
|DECIMAL（或DEC）  |精度可变的浮点值  |
|DOUBLE  |双精度浮点值  |
|FLOAT | 单精度浮点值 |
| INT（或INTEGER） |  整数值，支持2147483648～2147483647（如果是UNSIGNED，为0～4294967295）的数|
| MEDIUMINT |整数值，支持8388608～8388607（如果是UNSIGNED，为0～16777215）的数  |
|REAL  |4字节的浮点值  |
| SMALLINT | 整数值，支持32768～32767（如果是UNSIGNED，为0～65535）的数 |
|TINYINT  | 整数值，支持128～127（如果为UNSIGNED，为0～255）的数 |
- 日期和时间数据类型

| 数据类型 | 说 明 |
|--|--|
| DATE | 表示1000-01-01～9999-12-31的日期，格式为YYYY-MM-DD |
| DATETIME |DATE和TIME的组合  |
| TIMESTAMP | 功能和DATETIME相同（但范围较小） |
| TIME |格式为HH:MM:SS  |
| YEAR | 用2位数字表示，范围是70（1970年）～69（2069年），用4位数字表示，范围是1901年～2155年 |
- 二进制数据类型

|  数据类型| 说 明 |
|--|--|
| BLOB| Blob最大长度为64 KB |
| MEDIUMBLOB | Blob最大长度为16 MB |
| LONGBLOB | Blob最大长度为4 GB |
|TINYBLOB  | Blob最大长度为255字节 |

# 备份
- ```mysqldump```
>转储所有数据库内容到某个外部文件。在进行常规备份前这个实用程序应该正常运行，以便能正确地备份转储文件。
- ```mysqlhotcopy```
>从一个数据库复制所有数据（并非所有数据库引擎都支持这个实用程序）。
- ```BACKUP TABLE```或```或SELECT INTO OUTFILE```
>1. 转储所有数据到某个外部文件。这两条语句都接受将要创建的系统文件名，此系统文件必须不存在，否则会出错
>2. 数据可以用```RESTORE TABLE```来复原。
- ```FLUSH TABLES```
>为了保证所有数据被写到磁盘（包括索引数据），可能需要在进行备份前使用
# 待研究
- ```INFORMATION_SCHEMA```:mysql5新增，用来获取和过滤模式信息。
# 实例应用
- 找出列中最高值,通过```ORDER BY```与```LIMIT```组合使用
>```
>SELECT prod_price
>FROM products
>ORDER BY prod_price DESC
>LIMIT 1
>```
- 检索某段时间内的数据,如，检测出2005年9月份的所有数据
>
>1. 方法一
>```
>SELECT cust_id, order_num
>FROM orders
>WHERE Date(order_date) BETWEEN '2005-09-01' AND '2005-09-30'
>```
>2. 方法二：不需要知道有一个月多少天
>```
>SELECT cust_id, order_num
>FROM orders
>WHERE Year(order_date) = 2005 AND Month(order_date) = 9
>```