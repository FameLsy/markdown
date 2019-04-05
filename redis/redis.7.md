<!-- Redis的主从复制 -->

主从复制：持久化保证了即使redis服务重启也不会丢失数据（redis服务重启后会将硬盘上持久化的数据恢复到内存中），当redis服务器的硬盘损坏了可能会导致数据丢失，此时可以通过主从复制机制就可以避免这种单点故障

说明
- 主redis中的数据有两个副本（replication）即从redis1和从redis2，即使一台redis服务器宕机其它两台redis服务也可以继续提供服务。
- 主redis中的数据和从redis上的数据保持实时同步，当主redis写入数据时通过主从复制机制会复制到两个从redis服务上。
- 只有一个主redis，可以有多个从redis。
- 主从复制不会阻塞master，在同步数据时，master 可以继续处理client 请求

![redis2](https://raw.githubusercontent.com/FameLsy/Images/master/redis/redis2.png)

此外，一个redis可以即是主又是从

![redis3](https://raw.githubusercontent.com/FameLsy/Images/master/redis/redis3.png)

# 主从配置

主redis无需特殊配置

## 从redis配置

```
# redis.conf
# masterip:主Redis ip
# masterport： 主Redis 端口 
slaveof masterip masterport
```

## 实现原理

- Redis的主从同步，分为全量同步和增量同步。
- 只有从机第一次连接上主机是全量同步
- 断线重连有可能触发全量同步也有可能是增量同步（master判断runid是否一致）
- 除此之外的情况都是增量同步

![redis4](https://raw.githubusercontent.com/FameLsy/Images/master/redis/redis4.png)

### 全量同步

Redis的全量同步过程主要分三个阶段
- 同步快照阶段：Master创建并发送快照给Slave,Slave载入并解析快照。Master同时将此阶段所产生的新的写命令存储到缓冲区。
- 同步写缓冲阶段：Master向Slave同步存储在缓冲区的写操作命令。
- 同步增量阶段：Master向Slave同步写操作命令

![redis5](https://raw.githubusercontent.com/FameLsy/Images/master/redis/redis5.png)

### 增量同步

Redis增量同步主要指Slave完成初始化后开始正常工作时，Master发生的写操作同步到Slave的过程。通常情况下，Master每执行一个写命令就会向Slave发送相同的写命令，然后Slave接收并执行。


