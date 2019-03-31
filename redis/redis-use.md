# String类型的应用

## 自增主键

- 需求：商品编号、订单号采用INCR命令生成 
- 设计：key命名要有一定的设计
- 实现：定义商品编号key：items:id

```
127.0.0.1:6379> INCR items:id
(integer) 1
127.0.0.1:6379> GET items:id
"1"
127.0.0.1:6379> INCR items:id
(integer) 2
127.0.0.1:6379> GET items:id
"2"
```

# Hash类型的应用

## 存储商品信息

- 商品信息字段
【商品id、商品名称、商品描述、商品库存、商品好评】

- 定义商品信息的key
商品ID为1001的信息在 Redis中的key为：[items:1001]

```
127.0.0.1:6379> HMSET items:1001 id 3 name apple price 999.9
OK
127.0.0.1:6379> HGETALL items:1001
1) "id"
2) "3"
3) "name"
4) "apple"
5) "price"
6) "999.9"
```

# List类型的应用

## 商品评论列表

- 需求1：用户针对某一商品发布评论，一个商品会被不同的用户进行评论，存储商品评论时，要按时间顺序排序。
- 需求2：用户在前端页面查询该商品的评论，需要安装时间顺序降序排序
- 思路：  
    使用list存储商品评论信息，KEY是该商品的ID，VALUE是商品评论信息列表  
    商品编号为1001的商品评论key【items: comment:1001】

# Zset类型的应用

## 商品销售排行榜

- 需求：根据商品销售量对商品进行排行显示
- 思路：定义商品销售排行榜（sorted set集合），Key为items:sellsort，分数为商品销售量

```
# 商品编号1001的销量是9，商品编号1002的销量是10
127.0.0.1:6379> ZADD items:sellsort 9 1001 10 1002
(integer) 2
# 商品编号1001的销量加1
127.0.0.1:6379>  ZINCRBY items:sellsort 1 1001
"10"
# 商品销量前10名
127.0.0.1:6379> ZRANGE items:sellsort 0 9 withscores
1) "1001"
2) "10"
3) "1002"
4) "10"
```