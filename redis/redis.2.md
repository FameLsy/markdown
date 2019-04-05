!-- Redis客户端 --

# 自带命令行客户端

命令格式
```
# 默认方式，默认ip 127.0.0.1,默认端口 6379,相当于登录本地的redis
./redis-cli

# ip: bind 绑定的ip才能访问redis,访问远程redis
./redis-cli -h ip -p port
```

解决ip绑定问题,修改配置文件
```
# 注释掉 bind
# protected-mode该为no，关闭保护模式
```

# 多数据库支持

Redis默认支持16个数据库,每个数据库之间是相互隔离

修改数据库数量
```
# redis.conf
databases 16
```

切换数据库
```
# number: (0-15)编号
select number
```

# 通用命令

返回满足给定pattern 的所有key
```
# 如， keys mylist*，将会返回所有与mylist相关的key
keys pattern
```

删除key
```
DEL key
```

确认key是否存在
```
EXISTS key
```

设置key的生存时间（单位：秒）
```
EXPIRE key seconds
```

查看key剩余的生存时间
```
TTL key 
```

清除生存时间
```
PERSIST key
```

生存时间设置单位为：毫秒
```
PEXPIRE key milliseconds
```

重命名key
```
rename  oldkey  newkey
```

显示指定key的数据类型
```
TYPE key
```
