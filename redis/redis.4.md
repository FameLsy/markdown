<!-- Redis事务 -->

简介
- Redis的事务是通过MULTI，EXEC，DISCARD和WATCH这四个命令来完成的。
- Redis的单个命令都是原子性的，所以这里确保事务性的对象是命令集合。
- Redis将命令集合序列化并确保处于同一事务的命令集合连续且不被打断的执行（保存到一个Queue中）
- Redis不支持回滚操作

# 相关命令

标记事务块的开始(即开启事务)
```
#Redis会将后续的命令逐个放入队列中，然后使用EXEC命令原子化地执行这个命令序列
MULTI
```

执行事务
```
# 在一个事务中执行所有先前放入队列的命令,然后恢复正常的连接状态
EXEC
```

结束事务
```
# 清除所有先前在一个事务中放入队列的命令，然后恢复正常的连接状态
DISCARD
```

设置key为受监控的状态
```
# 当某个事务需要按条件执行时，就要使用这个命令将给定的键设置为受监控的状态
# 可以使用该命令实现Redis的乐观锁
WATCH key [key…]

# 如下示例，如果S1被改变，那么事务将不会执行
WATCH S1

MULTI
SET S2 00
SET S3 01
EXEC

```

清除所有的受监控状态
```
清除所有先前为一个事务监控的键
UNWATCH
```