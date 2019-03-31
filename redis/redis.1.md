<!-- Redis安装与启动 -->
* 官网地址：http://redis.io/
* 中文官网地址：http://www.redis.cn/
* 下载地址: http://download.redis.io/releases/ 

# Redis 安装

第一步：从下载地址中选择合适的版本，复制下载链接
```
wget http://download.redis.io/releases/redis-4.0.10.tar.gz
```

第二步：(如果没安装)安装C语言需要的GCC环境
```java
yum install gcc-c++
```

第三步：解压缩Redis源码压缩包
```
tar -zxf redis-x.x.x.tar.gz
```

第四步：进入到解压后的目录，进行源码的编译
```
make
```

第五步：进入src目录,然后指定目录进行安装
```java
make install PREFIX=/usr/local/redis
```

# Redis 启动(后端启动)

第一步：进入Redis源码解压包,复制配置文件到安装目录下
```
cp redis.conf /usr/local/redis/bin/
```

第二步：修改配置文件
```
vim redis.conf

# bind 127.0.0.1 改为 bind 本机ip
# daemonize 改为yes
```

第三步：加载配置文件并启动Redis
```
./redis-server redis.conf
```

# Redis 关闭

```
./redis-cli shutdown
```

# Redis bin目录
```
-rwxr-xr-x. 1 root root 2452152 3月  31 12:09 redis-benchmark
-rwxr-xr-x. 1 root root 5769584 3月  31 12:09 redis-check-aof
-rwxr-xr-x. 1 root root 5769584 3月  31 12:09 redis-check-rdb
-rwxr-xr-x. 1 root root 2618176 3月  31 12:09 redis-cli
-rw-r--r--. 1 root root   58792 3月  31 12:21 redis.conf
lrwxrwxrwx. 1 root root      12 3月  31 12:09 redis-sentinel -> redis-server
-rwxr-xr-x. 1 root root 5769584 3月  31 12:09 redis-server
```

解释
```
redis-benchmark： 性能测试的工具
redis-check-aof：aof文件进行检查的工具
redis-check-rdb：rdb文件进行检查的工具
redis-cli ：进入redis命令客户端
redis-sentinel：启动哨兵监控服务
redis-server：启动redis服务
```