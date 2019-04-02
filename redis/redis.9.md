<!-- Redis Cluster集群 -->

redis3.0以后推出的redis cluster 集群方案，redis cluster集群保证了高可用、高性能、高可扩展性

# redis-cluster架构

架构细节:
- 所有的redis节点彼此互联(PING-PONG机制),内部使用二进制协议优化传输速度和带宽.
- 节点的fail是通过集群中超过半数的节点检测失效时才生效.
- 客户端与redis节点直连,不需要中间proxy层.客户端不需要连接集群所有节点,连接集群中任何一个可用节点即可
- redis-cluster把所有的物理节点映射到[0-16383]slot上,cluster 负责维护node<->slot<->value

![redis6](https://raw.githubusercontent.com/FameLsy/Images/master/redis/redis6.png)

>Redis 集群中内置了 16384 个哈希槽，当需要在 Redis 集群中放置一个 key-value 时，redis 先对 key 使用 crc16 算法算出一个结果，然后把结果对 16384 求余数，这样每个 key 都会对应一个编号在 0-16383 之间的哈希槽，redis 会根据节点数量大致均等的将哈希槽映射到不同的节点

# redis-cluster投票:容错

节点失效判断：
- 集群中所有master参与投票,如果半数以上master节点与其中一个master节点通信超过(cluster-node-timeout),认为该master节点挂掉.

集群失效判断:什么时候整个集群不可用(cluster_state:fail)? 
- 如果集群任意master挂掉,且当前master没有slave，则集群进入fail状态。也可以理解成集群的[0-16383]slot映射不完全时进入fail状态。
- 如果集群超过半数以上master挂掉，无论是否有slave，集群进入fail状态。

# Redis集群（RedisCluster）安装

## ruby的安装

redis集群需要使用集群管理脚本redis-trib.rb，它的执行相应依赖ruby环境；所以需要线安装ruby

第一步:安装ruby
```
yum install ruby
yum install rubygems
```

第二步：安装ruby和redis的接口程序
```
gem install redis -V 3.2.2
```

第三步:复制redis-trib.rb文件到/usr/local/redis目录
```
cp redis-3.2.9/src/redis-trib.rb /usr/local/redis-cluster/ -r
```

## 安装Redis集群（RedisCluster）

Redis集群最少需要三台主服务器，三台从服务器,


# 相关命令

客户端连接集群
```
# -c表示以集群方式连接
./redis-cli –h ip –p port –c
```

查看集群状态    
```
cluster info
```

查看集群中的节点
```
cluster nodes
```

# 维护节点

集群创建成功后可以继续向集群中添加节点

## 添加主节点
```
./redis-trib.rb add-node new-nodei-p:port masterip:port
```

# hash槽重新分配（数据迁移）

添加完主节点需要对主节点进行hash槽分配，这样该主节才可以存储数据

第一步：连接上集群
```
# 随便连入一个节点
./redis-trib.rb reshard node-ip:port
```

第二步：输入要分配的槽数量(连入后要求输入，如3000，表示分配3000给新节点)

第三步：输入接收槽的节点id(输入槽数量后接下来输入节点ip,通过cluster nodes查看节点id)

第四步：输入源节点id(将从原节点取槽)
```
# 从所有节点从取
all
#  取消分配
done
```

第五步：输入yes

## 添加从节点
```
./redis-trib.rb add-node --slave --master-id  <master-node-ip>  <new-node-ip>:<new-node-port>   <old-node-ip>:<old-node-port>
```

注意：如果原来该结点在集群中的配置信息已经生成到cluster-config-file指定的配置文件中（如果cluster-config-file没有指定则默认为nodes.conf），这时可能会报错：
```
[ERR] Node XXXXXX is not empty. Either the node already knows other nodes (check with CLUSTER NODES) or contains some key in database 0
```
解决方法是删除生成的配置文件nodes.conf，删除后再执行./redis-trib.rb add-node指令

## 删除结点

无法删除占槽的节点，所以要先将槽分配出去

```
./redis-trib.rb del-node <node-ip:port> <node-id>
```


