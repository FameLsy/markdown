!-- Redis数据类型 --

Redis中存储数据是通过key-value格式存储数据的，其中value可以定义五种数据类型
- String（字符类型）
- Hash（散列类型）
- List（列表类型）
- Set（集合类型）
- SortedSet（有序集合类型，简称zset）

在redis中的命令语句中，命令是忽略大小写的，而key是不忽略大小写的

# String类型


## 设置值
```
# SET id 123
set key value

# 仅当不存在时赋值
# 如果key已经存在，则无法复制，SETNX id 123
SETNX key value

# 同时设置多个键值.
MSET key value []key value …]
```

## 读取值
```
# 如, GET id
GET key


# 同时获取多个键值
MGET key [key …]
```

## 取值并赋值
```
# 相当于读取当前value，然后再赋值一个新的vlaue
GETSET key value
```

## 判断key是否已存在值
```
# 如果存在，则显示1，不存在显示0
EXISTS key
```

 String 数值增减必须是 *整数数据*，它是原子操作

## 递增\递减数字

```
#递增
# 如，INCR id,相当于给id+1,如果没有设置key，那么默认0
INCR key

# 递减
DECR key
```

## 增加/减少指定的整数
```
# increment:增加量
# 如INCRBY id 2, 相当于id+2
INCRBY key increment

# 减少指定的整数
DECRBY key decrement
```

## 向尾部追加值 
```
#键不存在则将该键的值设置为value,返回值是追加后字符串的总长度
APPEND key value
```

## 获取字符串长度
```
# 如果key不存在，则返回0
STRLEN key
```

# Hash类型

hash叫散列类型，它提供了字段和字段值的映射。字段值只能是 *字符串类型*，不支持散列类型、集合类型等其它类型

![redis1](https://raw.githubusercontent.com/FameLsy/Images/master/redis/redis1.png)

## 设置值

HSET命令不区分插入和更新操作，当执行插入操作时HSET命令返回1，当执行更新操作时返回0。

```
#field: 字段
# 设置一个字段值
HSET key field value

# 设置多个字段值
HSET key field value [field value]

# 当字段不存在时赋值
HSETNX key field value
```

## 取值 

```
# 获取一个字段值
HGET key field	

# 获取多个字段值
HMGET key field [field ...]

# 获取所有字段值
HGETALL key
```

## 删除字段

可以删除一个或多个字段，返回值是被删除的字段个数

```
# 删除多个
HDEL key field [field ...]
```

## 增加数字

```
HINCRBY key field increment
```

## 判断字段是否存在

```
HEXISTS key field
```

## 只获取字段名或字段值

```
# 只获取字段名
HKEYS key
# 只获取字段值
HVALS key
```

## 获取字段数量

```
HLEN key
```

## 获取所有字段
```
HETALL key
```

# List类型

Redis 使用的是双向列表

## 向列表两端增加元素

```
# 向列表左边增加元素 
LPUSH key value [value ...]
# 向列表右边增加元素 
RPUSH key value [value ...]
```

## 从列表两端弹出元素

```
# 从列表左边弹出元素，返回该弹出元素
LPOP key
# 从列表右边弹出元素,返回该弹出元素
RPOP key
```

## 查看列表

```
#从[start, stop]范围内的所有列表，包含两端，从0开始编号，可以使用负数，表示倒数第n个
LRANGE key start stop
```

## 获取列表中元素的个数
```
LLEN key
```

## 删除列表中指定个数的值

```
#删除列表中前count个值为value的元素，返回实际删除的元素个数
# 当count>0时， LREM会从列表左边开始删除。 
# 当count<0时， LREM会从列表后边开始删除。 
# 当count=0时， LREM删除所有值为value的元素
LREM key count value
```

## 获得/设置指定索引的元素值

```
# 获得指定索引的元素值
LINDEX key index

# 设置指定索引的元素值
LSET key index value
```

## 只保留列表指定片段

```
# 包含头尾
LTRIM key start stop
```

## 向列表中插入元素

```
# 首先会在列表中从左到右查找值为pivot的元素
# 根据第二个参数是BEFORE还是AFTER来决定将value插入到该元素的前面还是后面

LINSERT key BEFORE|AFTER pivot value
```

## 将元素从一个列表转移到另一个列表中

```
# source 原列表
# destination 目标列表
RPOPLPUSH source-key destination-key
```

# Set类型


## 增加元素

```
SADD key member [member ...]
```

## 删除元素
```
SREM key member [member ...]
```

## 获得集合中的所有元素

```
SMEMBERS ke
```

## 判断元素是否在集合中
```
SISMEMBER key member
```

## 集合运算

```
# 差集运算，返回属于key1但不属于[key2 ...]的元素
SDIFF key1 [key2 ...]

# 交际运算，返回属于key1且属于[key2 ...]的元素
SINTER key1 [key2 ...]


# 并集运算
SUNION key [key ...]
```

## 获得集合中元素的个数 

```
SCARD key
```

## 从集合中弹出一个元素
```
# SPOP命令会从集合中随机选择一个元素弹出
SPOP key
```

# Zset类型(有序集合)

特点
- 有序
- 可以获得某一范围的元素
- 使用散列表实现，所有即使读取位于中间部分的数据也很快
- 能简单的调整某个元素的位置
- 比列表类型更耗内存

## 增加元素

```
# member:元素，score:分数，整型
#将一个或多个member元素及其score值加入到有序集key当中,如果该元素score值已存在，则覆盖，返回新加入集合的元素个数
ZADD key score member [score member ...]
```

## 获得排名在某个范围的元素列表

```
# 按照元素score值从小到大的顺序返回索引从start到stop之间的所有元素（包含两端的元素）
# 加上[WITHSCORES]可以获取到score值	
ZRANGE key start stop [WITHSCORES]

# 按元素score值从大到小的顺序返回索引从start到stop之间的所有元素（包含两端的元素）
ZREVRANGE key start stop [WITHSCORES]
```

## 获取元素的score值

```
ZSCORE key member
```

## 删除元素
```
# 不存在的member将会被忽略
ZREM key member [member ...]
```

## 获得指定score范围的元素
```
# [LIMIT offset count]用于限制条数
ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT offset count]
```

## 增加某个元素的score

```
# increment: 增加的值
ZINCRBY  key increment member
```

## 获得集合中元素的数量
```
ZCARD key
```

## 获得指定score范围内的元素个数
```
ZCOUNT key min max
```

## 按照排序范围删除元素

```
ZREMRANGEBYRANK key start stop
```

## 按照score范围删除元素
```
ZREMRANGEBYSCORE key min max
```

## 获取元素的排序
```
# 从小到大
ZRANK key member

# 从大到小
ZREVRANK key member
```
