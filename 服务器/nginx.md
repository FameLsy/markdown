---
title: Nginx笔记
tags: 
- 服务器
categories: 
- Server
---

# 什么是Nginx
## 了解Nginx
nginx会以daemon的方式在后台运行。后台进程包含一个master进程和多个worker进程。  

**master**进程：  
主要用来管理worker进程，包含：接收来自外界的信号，向各worker进程发送信号，监控worker进程的运行状态，当worker进程退出后(异常情况下)，会自动重新启动新的worker进程  

**worker**进程：  
处理基本的网络事件，各进程互相之间是独立的。worker进程的个数，一般与机器cpu核数一致.  
## Nginx 的进程模型
从进程模型图可以看出，我们想要操作nginx，只需要与master通信即可。

![nginx进程模型](https://raw.githubusercontent.com/FameLsy/Images/master/nginx/chapter-2-1.PNG)

master进程在接受信号是怎么做的呢？
输入如下命令行
```nginx
kill -HUP pid
```
这个命令是优雅地重启nginx。当master进程收到信号后，因为是优雅地重启，所以服务器不会中断。而是重新加载配置文件，然后新开worker进程，再向旧的worker进程发送关闭指令。这种直接发送信号给master进程的操作方式已经显得有些老了，在0.8版本后，引入了一系列命令行参数。
如
```nginx
nginx -s reload
```
这是一个重新加载配置的命令。在执行命令时，它会向master进程发送信号，同样也是启动一个新的worker进程，再关闭掉旧的worker进程。  

worker 进程又是怎么做的??  

首先，在master进程里，比方说一个80端口的http服务，会建立好需要listen的sokcet(listenfd),然后再fork出多个worker进程。在新的请求到来时，所有worker进程的listenfd变为可读，然后开始抢锁，抢到互斥锁的那个进程注册listenfd读事件，在读事件里调用accept接受该连接,开始读取请求，解析请求，处理请求，产生数据后，再返回给客户端。一个请求，完全由worker进程来处理，而且只在一个worker进程中处理。  

好处：独立进程，不需要加锁，所以省掉了锁带来的开销。其次，worker进程间互相之间不会影响。
## nginx事件模型
对于一个基本的web服务器来说，事件通常有三种类型，**网络事件、信号、定时器**。  

**网络事件**  

nginx采用了异步非阻塞的方式来处理请求，可以同时处理成千上万个请求。  

请求的完整过程：请求过来，建立连接，然后再接收数据，接收数据后，再发送数据。具体到系统底层，就是读写事件，而当读写事件没有准备好时，必然不可操作。  

|网络事件模型|说明|
|--|--|
|阻塞式|事件没准备好，等。在等待中，cpu会让出去。<br/>那么，会造成，网络事件一多，大家都在等，cpu都让出去了但是没人用，cpu使用率都不高,还谈高并发？？？<br/>尤其是单线程的worker了，更不合适。|
|非阻塞式|事件没准备好，马上返回**EAGAIN**，然后间断性再次访问，直到事件准备完成。<br/>好处是确实可以做更多其他的事情，但开销也增加了。|
|异步非阻塞|对于事件，它是阻塞的。但是，它不是盲目的等待，而是设定了一段超时时间。<br>在超时时间内事件准备好了，就返回|

以**epoll**(一个异步非阻塞机制的函数)为例：  
>1 当事件没准备好时，放到epoll里面；  
>2 事件准备好了，就去读写；  
>3 当读写返回EAGAIN时，将它再次加入到epoll里面；  
>4 线程只有一个，所以只能处理一个请求。但可以在请求里切换，再多的并发数，zhi会占用更多的内存而已；  

所以说，worker数等于cpu数的原因。就是因为如此，反而事更多的worker数，只会导致cpu资源的竞争，从而带来不必要的上下文切换。  

**信号**  

信号会中断掉程序当前的运行，在改变状态后，继续执行。如果是系统调用，则可能会导致系统调用的失败，需要重入。关于信号的处理，大家可以学习一些专业书籍，这里不多说。对于nginx来说，如果nginx正在等待事件（epoll_wait时），如果程序收到信号，在信号处理函数处理完后，epoll_wait会返回错误，然后程序可再次进入epoll_wait调用。  


**Nginx定时器**  

由于epoll_wait等函数在调用的时候是可以设置一个超时时间的，所以nginx借助这个超时时间来实现定时器。nginx里面的定时器事件是放在一颗维护定时器的红黑树里面，每次在进入epoll_wait前，先从该红黑树里面拿到所有定时器事件的最小时间，在计算出epoll_wait的超时时间后进入epoll_wait。所以，当没有事件产生，也没有中断信号时，epoll_wait会超时，也就是说，定时器事件到了。这时，nginx会检查所有的超时事件，将他们的状态设置为超时，然后再去处理网络事件。由此可以看出，当我们写nginx代码时，在处理网络事件的回调函数时，通常做的第一个事情就是判断超时，然后再去处理网络事件。  

**Nginx事件处理伪代码**  
看！不！懂！
```java
while (true) {
    for t in run_tasks:
        t.handler();
    update_time(&now);
    timeout = ETERNITY;
    for t in wait_tasks: /* sorted already */
        if (t.time <= now) {
            t.timeout_handler();
        } else {
            timeout = t.time - now;
            break;
        }
    nevents = poll_function(events, timeout);
    for i in nevents:
        task t;
        if (events[i].type == READ) {
            t.handler = read_handler;
        } else { /* events[i].type == WRITE */
            t.handler = write_handler;
        }
        run_tasks_add(t);
}
```
# Connection
在nginx中connection就是对tcp连接的封装，其中包括连接的socket，读事件，写事件。

# 安装
centos：
- 从操作系统(OS)进行安装
>这种方法比较快捷，但包可能过时
>1. 添加epel库：```sudo yum install epel-release```
>2. 更新库：```sudo yum update```
>3. 安装： ```sudo yum -y install nginx```
>4. 验证安装：```sudo nginx -v```
- 从官方存储库安装
>这种方法保证最新
>1. 添加源文件```sudo vi /etc/yum.repos.d/nginx.repo```
>2. 写入信息
>- /mainline:指向最新主线版本，删除它可以获取最新的稳定版本
>- < OS >：根据系统替换成```centos```或```rhel```
>< OSRELEASE >：替换成版本号（6，6.x，7，7.x等等）
>```
>[nginx]
>name=nginx repo
>baseurl=https://nginx.org/packages/mainline/<OS>/<OSRELEASE>/$basearch/
>gpgcheck=0
>enabled=1
>```
>3. 更新库```yum -y update```
>4. 安装```yum -y install nginx```
>5. 启动```sudo nginx```
>5. 验证查看版本:```sudo nginx -v```
>6. 验证是否启动并允许```curl -I 127.0.0.1```输出 ```HTTP/1.1 302 OKServer: nginx...```

Ubuntu：
- 从操作系统进行安装:```sudo apt-get install nginx```
- 从官方存储库安装
>1. 下载软件包和存储卡的密钥```sudo wget https://nginx.org/keys/nginx_signing.key```
>2.  添加密钥```sudo apt-key add nginx_signing.key```
>3.  打开源编辑文件```sudo vim /etc/apt/sources.list```
>4.  添加以下内容
>- /mainline:路径名中的元素指向NGINX开源的最新主线版本; 删除它以获得最新的稳定版本
>- < CODENAME >： 是Ubuntu版本的代号
>```
>deb https://nginx.org/packages/mainline/ubuntu/ <CODENAME> nginx
>deb-src https://nginx.org/packages/mainline/ubuntu/ <CODENAME> nginx
>推荐输入：
>deb https://nginx.org/packages/ubuntu/ trusty nginx
>deb-src https://nginx.org/packages/ubuntu/ trusty nginx
>```
>5. 安装
>```
>sudo apt-get remove nginx-common
>sudo apt-get update
>sudo apt-get install nginx
>```
>6. 启动```nginx```
# 配置文件
- 打开配置文件```vim /etc/nginx/nginx.conf ```
- 配置文件详解
```LINUX
#用户
user  nginx;
#进程数（有几个CPU就执行几个进程） 
worker_processes  1;
#错误日志文件
error_log  /var/log/nginx/error.log warn;
#pid文件
pid        /var/run/nginx.pid;


events {
#连接数，即每个进程开多少个线程
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    
    default_type  application/octet-stream;
	#日志格式
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
	#访问日志，使用main日志格式（main:上面的log_format定义了main这种日志格式）
    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}
```
# 参考文献
[《Nginx从入门到精通》](http://tengine.taobao.org/book/index.html)