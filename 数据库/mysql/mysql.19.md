---
title: 全文本搜索
tags: 
- MySQL
- 数据库
categories: 
- Database
---


# 全文本搜索

搜索text数据（MyISAM支持，InnoDB不支持）

启用全文本搜索FULLTEXT
- Mysql会根据FULLTEXT（）的指示对它进行索引，自动维护该索引。在增加、更新或删除行时，索引随之自动跟新。
- 可以在创建表时指定FULLTEXT（不推荐），也可以稍后指定。
- 先导入数据，在定义FULLTEXT，有助于更快导入索引（更新索引需要时间）

```
CREATE TABLE productnotes
(
	note_id int NOT NULL ATUO_INCREMENT,
	prod_id char(10) NOT NULL,
	note_date datetime NOT NULL,
	note_nect text NULL,
	PRIMARY KEY(note_id),
	FULLTEXT(note_text)
) ENGINE = MyISAM
```

进行全文本搜索Match()和Against()
- Match()指定所搜的列
- Against()指定搜索表达式

```java
//如，将会搜索包含rabbit的文本,不区分大小写

SELECT note_text
FROM productnotes
WHERE Match(note_text) Against('rabbit')
```

与LIKE的区别
- LIKE以不特别有用的顺序返回数据

- 全文本搜索返回以文本匹配的良好程度排序的顺序(如rabbit作为第3个词的行等级比作为第20个词的行高)

查询扩展WITH QUERY EXPANSION
- 用来放宽返回的全文本搜索结果的范围
- 首先进行基本的全文本搜索，找出匹配的行，
- 其次，检查这些匹配行并选择所有有用的词，
最后，再次使用有用的词进行一次全文本上搜索。
- 
```
SELECT note_text
FROM productnotes
WHERE Match(note_text) Against('anvils' WITH QUERY EXPANSION)
```

布尔文本搜索IN BOLEAN MODE，可以提供内容细节
- 要匹配的词
- 要排斥的词（如果某行包含这个词，则不反回改行。即使包含匹配的词）
- 排列提示（指定某些词比其他词更重要）
- 表达式分组
- 另外一些内容
```java
//匹配heavy,但排除任何以rope开始的词或行

SELECT note_text
FROM productnotes
WHERE Match(note_text) Against ('heavy -rope*' IN BOOLEAN MODE)
```

全文本布尔操作符
```
+：包含，词必须存在
-：排除，词必须不出现
：包含，而且增加等级
<：包含，而且减少等级
()：把词组成子表达式（允许子表达式作为一个组被包含、排除、排列）
~：取消一个词的排序值
*：词尾通配符
""：定义一个短语（与单个词的列表不一样，它匹配整个短语以便包含或排除这个短语）
```
示例1
```java
//包含rabbit和bait的行

SELECT note_text
FROM productnotes
WHERE Match(note_text) Against('+rabbit +bait' IN BOOLEAN MODE)
```
示例2:
```java
//至少包含rabbit和bait其中一个的行

SELECT note_text
FROM productnotes
WHERE Match(note_text) Against('rabbit bait' IN BOOLEAN MODE)
```
示例3:
```java
//匹配短语rabbit bait而不是匹配两个词

SELECT note_text
FROM productnotes
WHERE Match(note_text) Against('"rabbit bait"' IN BOOLEAN MODE)
```
示例4:
```java
//至少包含rabbit和bait其中一个的行，增加rabbit的等级，降低bait的等级

SELECT note_text
FROM productnotes
WHERE Match(note_text) Against('rabbit <bait' IN BOOLEAN MODE)
```
示例5
```java
//至少包含safe 和combination其中一个的行，降低combination的等级

SELECT note_text
FROM productnotes
WHERE Match(note_text) Against('+safe +(<combination)' IN BOOLEAN MODE)
```

全文本搜索使用说明
- 在索引全文本数据时，短词被忽略且从索引中排除。（短词：3个或3个一下字符的词）
- MySQL带有一个内建的非用词列表，这些词在索引时被忽略
- 高频词汇会被当做非用词（一个词出现在50%以上的行,该规则不用于IN BOOLEAN MODE）
- 如果表中的行数少于3行，则全文本搜索不反回结果(要么没有，要么至少出现在50%行中)
- 忽略此种的单引号。如don't索引为dont
- 不具有此分隔符的语言不能恰当地返回全文本搜索结果（汉语、日语）
- 仅在MyISAM引擎中支持全文本搜索