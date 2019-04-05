<!-- centos安装 -->
# CentOS最小化安装后联网

选择最小化安装Centos后，Linux是没有联网的,可以使用以下命令查看是否分配了ip地址
```
ip ddr
```
成功分配ip地址显示如下
```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:0c:29:74:a2:d2 brd ff:ff:ff:ff:ff:ff
    inet 192.168.171.128/24 brd 192.168.171.255 scope global noprefixroute dynamic ens33
       valid_lft 1522sec preferred_lft 1522sec
    inet6 fe80::a3d7:62c5:cd2e:7350/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
```

如果没有成功的话，就需要输入以下命令来寻找网卡
```
cd /etc/sysconfig/network-scripts/
```

该目录下有两个 `ifcfg` 开头的文件
```
-rw-r--r--. 1 root root    52 Feb 11 10:03 ifcfg-eth0
-rw-r--r--. 1 root root   254 Mar 22  2017 ifcfg-lo
```

修改除`ifcfg-lo`的另一个文件
```
vi ifcfg-eth0

# 将 ONBOOT 改为 yes

```

保存后重启网卡服务
```
service network restart
```

再次执行 `ip addr`  查看是否分配了ip

# 常用工具的安装

安装 ifconfig
```
yum install net-tools
```

安装vim
```
yum install vim
```

# JDK 安装

[JDK 8官网下载](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

选择linux版本下载(这里我使用的是IDM提取的链接),然后使用 `wget` 工具进行下载
```
wget https://download.oracle.com/otn-pub/java/jdk/8u201-b09/42970487e3af4f5aa5bca3f542482c60/jdk-8u201-linux-x64.tar.gz?AuthParam=1554275167_27b6bf4971db78028f7ae08fac86c9ee
```

下载完成后，进行解压(有可能文件名不对，手动改为.tar.gz结尾)
```
sudo tar -zxvf jdk-8u201-linux-x64.tar.gz -C /usr/local/
```

配置环境变量
```
sudo vim /etc/profile

# 在末尾输入
export JAVA_HOME=/usr/local/jdk1.8.0_201
export PATH=$JAVA_HOME/bin:$PATH
```

立即更新环境变量
```
source /etc/profile
```

# MySQL安装


第一步，添加 [MySQL官方库](https://dev.mysql.com/downloads/repo/yum/),选择合适的rpm包
```
# 替换platform-and-version-specific-package-name.rpm
sudo yum localinstall platform-and-version-specific-package-name.rpm
```

第二步，选择合适的库
```
# 查看哪些库已启用
yum repolist all | grep mysql

# 可以看到当前是8.0 版本被启用
mysql55-community/x86_64           MySQL 5.5 Community Server    disabled
mysql55-community-source           MySQL 5.5 Community Server -  disabled
mysql56-community/x86_64           MySQL 5.6 Community Server    disabled
mysql56-community-source           MySQL 5.6 Community Server -  disabled
mysql57-community/x86_64           MySQL 5.7 Community Server    disabled
mysql57-community-source           MySQL 5.7 Community Server -  disabled
mysql80-community/x86_64           MySQL 8.0 Community Server    enabled:     82

```

禁用版本和选择版本,打开 `mysql-community.repo`文件
```
sudo vim /etc/yum.repos.d/mysql-community.repo

# 如要选择5.7版本，则将enabled 改为1，并将其他改为0表示禁用
# Enable to use MySQL 5.7
[mysql57-community]
name=MySQL 5.7 Community Server
baseurl=http://repo.mysql.com/yum/mysql-5.7-community/el/7/$basearch/
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql

```

通过以下指令验证是否正确禁用启用某些库
```
yum repolist enabled | grep mysql
```

第三步，安装
```
sudo yum install mysql-community-server
```

第四步，启动服务
```
sudo service mysqld start
```

检查服务器启动状态
```
sudo service mysqld status
```

服务器初次启动时，MySQL已经将root用户和密码保存在错误日志中,通过以下指令来查找密码
```
sudo grep 'temporary password' /var/log/mysqld.log
```

修改密码
```
# 登录
mysql -uroot -p
# 修改密码
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'NewPassword!';
```

# Tomcat 安装

[Tomcat 下载地址](https://tomcat.apache.org/download-90.cgi)

Tomcat 与 JDK的对应版本

![tomcat](https://raw.githubusercontent.com/FameLsy/Images/master/tomcat/tomcat.png)

下载Tomcat
```
wget http://mirrors.shu.edu.cn/apache/tomcat/tomcat-9/v9.0.17/bin/apache-tomcat-9.0.17.tar.gz
```

解压
```
sudo tar -zxvf apache-tomcat-9.0.17.tar.gz -C /usr/local/
```



开启
```
# 进入bin目录
./startup.sh
```




# Centos 7防火墙
CentOS 7.0默认使用的是firewall作为防火墙

开启/关闭
```
systemctl stop firewalld.service && systemctl disable firewalld.service

systemctl start firewalld.service && systemctl enable firewalld.service
```

查看指定端口是否已经打开
```
firewall-cmd --query-port=666/tcp
```

开放端口(开放完后需重载)
```
firewall-cmd --add-port=8080/tcp --permanent
```

防火墙重载
```
firewall-cmd --reload
```

查看防火墙运行状态
```
systemctl status firewalld
```