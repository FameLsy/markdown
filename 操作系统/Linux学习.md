# 用户相关
- 添加新用户:
```
useradd
```
>对象: 添加用户,如 ```useradd test_user```
>指令:
>-u：指定uid
>-g：指定主组
>-d：指定家目录
>-c：添加秒速信息
>-s：指定shell信息
>-G：指定附加组
>如```useradd -u 1024 -g root -d /test_user/ -c 'test' -s /bin/bash test_user```
- 删除用户:```userdel```
>-r ：删除主目录和邮件池，如```userdel -r famel```
- 修改用户：```usermod 选项 用户名```(与添加用户选项类似)
- 修改用户密码：```passwd```
>默认：修改当前登录用户密码
>对向：修改指定用户密码，如```passwd famel```
>选项：
> -- stdin:允许接受参数作为密码(centos使用) 一次性修改密码方式: ```echo 123456 | passwd --stdin famel```
- 修改\初始化root用户密码：```sudo passwd```
- 修改用户密码(ubuntu有效): ```echo famel:123456 | chpasswd```
- 查看用户信息:```id 用户名```
>![在这里插入图片描述](https://img-blog.csdnimg.cn/20181121111339355.png)
>uid: 用户id（1000开始计数）
>gid 主组id
>groups：主组组和附加组，不指定主组的情况下，用户必会在以其用户名为组名的一个主组里

- 查看所有用户登录的终端：```who```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181120201418898.png)
- 查看当前用户登录的终端: ```tty```，下图当前用户登录的终端未pts/2
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181120201440802.png)
- 查看当前用户： ```whoami```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181120201353641.png)
- 切换用户: ```su - username```
- 退出用户: ```exit```
[LINUX 模拟创建用户流程](https://blog.csdn.net/MASORL/article/details/84329314)
___
# 组相关
- 添加组:```groupadd 组名```
- 删除组:```groupdel 组名```
- 修改组:```groupmod```
>选项
>-n:修改组名,如```groupmod -n new_name group_1```
>-g: 修改组id,如```grioupmod -g 1032 goup_1```
___
# 权限相关
- 权限解释
>r：对应数字4，可读权限，如```ls```需要read权限
>&emsp;&emsp;对于文件：能看内容
>&emsp;&emsp;对于目录：浏览目录下的子目录名，子文件名
>w：对应数字2，可写权限，如```rm```,对于目录，删除添加内容相当于对目录进行写的操作，但是想要删除某一目录下的文件，还需x权限（进得去才能删）
>&emsp;&emsp;对于文件：修改内容
>&emsp;&emsp;对于目录：创建，重命名，删除子目录名、子文件名
>x：对应数字1，可执行权限，如```./a.txt```，./加文件就是执行文件（**想要执行文件，还需read权限，因为不光要执行，还要读取文件的命令**）,再如```cd```,就只需要x权限
>&emsp;&emsp;对于文件：执行文件
>&emsp;&emsp;对于目录：可以cd切换进入
>数字表示：如rwx用数字表示就是7


首先通过命令```ls -dl /home/famel```来查看famel目录的详细信息
```linux
root@famel-virtual-machine:~# ls -dl /home/famel
drwxr-xr-x 16 famel famel 4096 11月 21 13:45 /home/famel
```
>第一段：文件类型
>```d```，表示目录
>```-```，普通文件
>```l```，快捷方式
>```p```，管道文件
>```c```，字符设备
>```b```，磁盘文件
>第二段：```rwx```，属主的权限
>第三段：```r-x```，属组的权限
>第四段：```r-x```，除属主和属组之外的其他用户权限
>第五段：```famel```，属主
>第六段：```famel```，属组
>第七段：```4096```，已经占用大小
- 修改权限```chmod```
>u，属主；g，属组；o，其他用户
>=：赋值方式操作，如```chmod u=rw a.txt```，将a.txt的rw权限赋给属主(u=的话就表示没有任何权限)
>-：加减方式操作，如```chmod g-r a.txt```，删除属组的read权限
>数字方式操作：如```chomd 774 a.txt```,属主权限rwx,属组权限rwx,其他用户权限r
>一次性修改：```chomd  u=rw,g=rwx,o= a.txt```;
>*:当前目录下所有文件夹,如```chmod 777 *```,即对当前目录下所有的文件夹权限开放

[为LINUX用户添加sudo操作权限点这](https://blog.csdn.net/MASORL/article/details/84719988)
___
# 系统相关
- 查看内存信息```vmstat```
- 查看io信息```iostat```
- 查看网络信息```netstat -tunalp```
- 实时查看系统信息```top```
>按1可以切换cpu
```linux
top - 21:56:49 up(开机时间)  2:48（运行时间）,  3 users（使用人数）,  load average: 0.00, 0.00, 0.00（平均负载：1分钟，5分钟，15分钟）
Tasks（任务）: 157 total（当前任务）,   1 running（正在运行的任务）, 156 sleeping,   0 stopped（等待）,（暂停）   0 zombie（僵尸进程）
Cpu(s):  0.0%us,  0.3%sy,  0.0%ni, 99.7%id,  0.0%wa,  0.0%hi,  0.0%si,  0.0%st
Mem（内存）:   1003020k total,   852632k used,   150388k free,    72608k buffers
Swap:  2097148k total,        0k used,  2097148k free,   472880k cached

   PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND                                                                                                  
     1 root      20   0 19344 1572 1244 S  0.0  0.2   0:01.60 init                                                                                                      
     2 root      20   0     0    0    0 S  0.0  0.0   0:00.00 kthreadd                                                                                                  
     3 root      RT   0     0    0    0 S  0.0  0.0   0:00.00 migration/0                                                                                               
     4 root      20   0     0    0    0 S  0.0  0.0   0:00.00 ksoftirqd/0                                                                                               
     5 root      RT   0     0    0    0 S  0.0  0.0   0:00.00 stopper/0                                                                                                 
     6 root      RT   0     0    0    0 S  0.0  0.0   0:00.01 watchdog/0                                                                                                
     7 root      20   0     0    0    0 S  0.0  0.0   0:05.11 events/0       
```

- 保存cpu信息的文件```/proc/cpuinfo```
- 查看命令所在路径```which```,如```which ls```
[命令的执行过程详解](https://blog.csdn.net/MASORL/article/details/84433184)
- 关机: ```init 0```
- 立即关机: ```shutdown -h now```
- 重启: ```init 6```
- 多用户终端: ```ctrl + alt + f1~f6(文本界面) \F7 (图形界面)```
- 查看日期：```date```
- 查看日历：```cal```
>+ 年: 查看某年的日历，如```cal 1990```
>+ 月 年:查看某年的某月的日历,如```cal 3 1998```
- 帮助文档：```指令 --help```或 ```man 指令```
- 分页查看（上下）：```less```
>如：```ls --help | less```
>help的信息少量会显示出来
>通过键盘上下键翻阅
- 百分比查看：```more```
>百分比查看信息
>通过回车翻页

- 查看历史：```history```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181121110744980.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01BU09STA==,size_16,color_FFFFFF,t_70)
- 使用历史命令:```! 历史编号```
>历史编号即为history查出来的编号
- 查看系统当前版本信息```lsb_release -a```
- 查看linux系统内核信息```uname```
>描述：
>```
>print system information
>输出系统信息
>```
>选项:
>-a :输出所有信息,如```uname -a```
>```linux
>Linux famel-virtual-machine  主机名
>4.4.0-21-generic #37-Ubuntu 
>SMP Mon Apr 18 18:33:37 UTC 2016 时间
> x86_64 x86_64 x86_64 GNU/Linux
>```

___
# 目录相关
**目录结构**
/: 根目录
/boot：启动相关
/bin：常用命令相关
/etc: 配置文件相关
/sbin: 系统命令相关 
/media或/mnt: 挂载相关
/home: 家目录相关
/dev : 设备文件相关

**目录操作**
 - 进入目录（需要x权限）：```cd```
 > 对象：进入目录，如```cd /a/b```
 > 选项：
 > . .：进入上级目录,如```cd..```
 > -: 进入上次所在的目录,如```cd - ```
 > ~: 进入当前用户的家目录,如```cd ~```


- 添加目录（需要wx权限）：```mkdir```
> 对象：添加目录，且所有目录必须存在,如```mkdir /a/b/c```,其中a和b目录必须存在，否则无法创建c目录
> 选项：
>-p :递归添加目录，即使目录不存在，也可以一一创建，如```mkdir -p /a/b/c```
- 删除目录（需要wx权限）: ```rm```
> 目录：删除目录，如果目录下有子文件，子目录的情况下，是无法删除的，如```rm /a/b```
> 选项：
> -rf 目录：递归方式删除(r)，无提示(f):,如```rm -rf /a/b```

删除目录推荐做法: ```mv  删除的目录 /tmp```
>将要删除的目录移动到tmp文件夹，这样就可以找回！
- 重命名目录:```mv 目录A  目录B```
>如果A,B在同一目录下时，就是重命名
>不同目录下时，即将A的内容转移到B
- 查看当前所在目录：```pwd```
- 查看目录（需要r权限: ```ls```
>默认：查看当前目录内容
>对象：查看指定目录内容,如```ls /a/b```
>选项：
>-d :列出目录本身，而不是查看它的内容,如```ls -d```
>-l: 查看目录详细信息.如```ls -l```，简写```ll```
>-a：查看当前目录所有内容(包括隐藏文件).如```ls -a```
- 复制目录：```cp 目录A 目的地```
- 更改目录属主和属组:```chown```
>如```chown famel.famel /home/famel```，将/home/famel目录的属主和属组改为famel
>选项：
>-R：以递归方式操作文件和目录，如```chown -R famel.famel /home/famel```，将/home/famel 下所有的目录的属主和属组都改为famel
- 计算目录文件大小```du```
>-s：每个参数只显示一个总数
>-h：输出大小
>如 ```du -sh /a```,计算/a文件大小
___
# 文件相关
- 添加文件: ```touch 文件名```
- 修改文件名: ```mv 文件A 文件B```
>如果A,B在同一目录下时，就是重命名
>不同目录下，就是重命名加移动
- 删除文件:```rm 文件```
- 查看文件: ```ls 文件```(没啥用)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181120214136946.png)
- 复制文件：```cp 文件 目的地```
___
# 文件内容相关
- 查看文件内容:```cat 文件```
- 分页查看文件内容:```less 文件```
- 百分比查看文件内容：```more 文件```
- 查看文件头部内容: ```head```
>默认：查看前10行
>选项：
> -n: 查看前n行，如```head -n 3 a.txt```,表示查看a.txt文件的前3行
- 查看文件尾部内容:```tail -n 行 文件```
> 默认：查看尾部10行
> 选项
> -n:查看尾部n行, 如```head -n 3 a.txt```，表示查看a.txt文件的尾部3行
> -f: 文件内容追踪，如```tail -f test.log```,用于实时读取日志文件新增内容
 [tail失效情况，涉及到inode,以后再看](https://blog.csdn.net/earthchinagl/article/details/80699578)
 
- 覆盖方式改写文件: ```> ```
>如：```echo 123456 > a.txt```,直接讲123456覆盖到a.txt中
>如果用tail -f 指令追踪，会出现文件截断
>tail: Untitle.txt: file truncated
- 追加方式改写文件: ```>>```
> 如```echo 123456 >> a.txt```,会将123456添加到a.txt末尾
___
# 系统文件详情介绍
- passwd文件(用户信息文件)：```cat /etc/passwd | less```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181121133848397.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01BU09STA==,size_16,color_FFFFFF,t_70)
以```famel:x:1000:1000:Famel,,,:/home/famel:/bin/bash```为例
>第一段：```famel```,用户名
>第二段：```x```,密码的占位符
>第三段：```1000```,uid
>第四段：```1000```,gid
>第五段：```Famel,,,```,用户描述信息
>第六段：```/home/famel```,用户家目录
>第七段：shell命令
>```/bin/bash```: 用户登录shell（可登录）
>```/sbin/nologin```: 用户不可登录（ftp可登录）
>```/bin/false```: 一切服务均不可登录，最严格

- shadow(用户密码文件)文件:```cat /ect/shadow | less```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181121134744413.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01BU09STA==,size_16,color_FFFFFF,t_70)
以如下行为例```famel:$6$7Xe...:17855:0:99999:7:::```
>第一段：```famel```,用户名
>第二段：```$6$7Xe...```,加密后的密码
>第三段：```17855```
>第四段：```0```
>第五段：```99999```
>第六段：```7```

- group文件(组文件)：```cat /etc/group```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181121141006499.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01BU09STA==,size_16,color_FFFFFF,t_70)
以```famel:X:1000:```为例
>第一段：```famel```,组名
>第二段：```x```,组密码占位符
>第三段：```1000```,组id
>第四段：组员(famel 用户肯定在famel，所以这里是空的)，这里表示的是附加组

- gshadow文件(组密码文件,了解):```cat /etc/gshadow```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181121141809870.png)

___
# 归档相关
- 打包文件:```tar```
- 选项：
>-c：创建文件
>-f：指定归档文件
>-v：显示信息,如```tar -cvf test.tar a.txt b.txt c.txt ```,打包3个txt文件为test.tar,并显示打包信息
>-t：查看归档文件,如```tar -tf /a/test.rar```,查看test.tar文件
>-x：解压归档文件
>-C：指定解压位置, 如```tar -xf test.tar  -C /b```，解包test.tar到/b目录下
>-z：归档后使用gzip进行压缩,如```tar -cvzf test.tar.gz a.txt b.txt c.txt ```, 打包3个文件并且压缩为test.tar.gz
>-j：归档后使用bzip2进行压缩,如```tar -cvjf test.tar.bz2 a.txt b.txt c.txt ```,打包3个文件并压缩为test.tar.bz2
>**注意**:
>对于test.rar.gz和test.rar.bz2,直接使用tar -xf 就可以解包成原文件，但不能解用gzip和bzip2压缩的文件
>如果想要打包而不包括目录本身，可以进入目录，再打包，如打包etc目录,先```cd /etc```,在```tar -cvzf test.tar.gz *```



- 压缩：```gzip```
>对象: 压缩对象,如```gzip a.txt```,得到a.txt.gz文件
>选项：
>-d：解压缩,如```gzip -d a.txt.gz```,得到a.txt文件
>**注意**
>压缩后原文件不会存在，且文件大小降低
>gzip只能对一个对象压缩，```gzip a.txt b.txt ```,会出现a.txt.gz和b.txt.gz两个文件
>所以要压缩多个文件，需要先归档，然后压缩归档文件```gzip test.rar```

- 压缩2：```bzip2```
>使用方法与gzip类似
>对象: 压缩对象, 如```bizp2 a.txt```,得到a.txt.bz2文件
>-d: 解压缩，如```bzip2 -d a.txt.bz2 ```,得到a.txt

- bizp2 对比 gizp
> bzip2 压缩时间久，压缩率高
> gzip压缩时间短，压缩效率没bzip2好
___
# 编辑器相关
- 编辑器```vim```
>对象: 打开文件，并进入命令行模式,如```vim a.txt```
>命令行模式：
>&emsp;&emsp;键位a：进入编辑模式,光标会相对原处后移一位
>&emsp;&emsp;键位i：进入编辑模式，光标不动
>&emsp;&emsp;键位o：进入编辑模式，直接下一行，且新建成空行
>&emsp;&emsp;键位冒号 :进入扩展模式 
>&emsp;&emsp;键位$: 光标跑到当前行尾部
>&emsp;&emsp;键位0：光标跑到当前行头部
>&emsp;&emsp;键位dd：剪切当前行
>&emsp;&emsp;键位p:粘贴剪切板内容到光标的下一行（通过dd和yy操作的内容）
>&emsp;&emsp;键位P:粘贴剪切板内容到光标的上一行（通过dd和yy操作的内容）
>&emsp;&emsp;键位ndd:n为数字,如```3dd```，表示从光标位置开始三行进行剪切操作（包括光标位置的行）
>&emsp;&emsp;键位yy:复制当前行
>&emsp;&emsp;键位nyy:n为数字,如```3yy```，表示从光标位置开始三行进行复制操作（包括光标位置的行）
>&emsp;&emsp;键位u：撤销操作
>&emsp;&emsp;键位ctrl+r：重复操作
>&emsp;&emsp;键位G：光标移动到最后一行
>&emsp;&emsp;键位nG：光标移动到第n行，从1开始算
>&emsp;&emsp;键位gg：光标移动到第一行，相当于1G
>&emsp;&emsp;键位H：跳到屏幕的开头
>&emsp;&emsp;键位M：跳到屏幕的中间
>&emsp;&emsp;键位L：跳到屏幕的结尾
>&emsp;&emsp;/+字符串：查找关键字
>编辑模式：
>&emsp;&emsp;键位ESC:进入命令行模式
>&emsp;&emsp;键位
>扩展模式：
>&emsp;&emsp;w：保存
>&emsp;&emsp;q：退出
>&emsp;&emsp;！：强制操作

___
# 磁盘相关
- 测试硬盘速度```dd```
>选项
>if=FILE,从FILE转换和读取文件
> of=FILE，写入文件
> bs=BYTES,读写的字节数
>count,执行读写的次数
> 如```dd if=/dev/sdb1 of=/a.txt bs=500M count=2```,从/dev/sbd1读数据写到a.txt,大小为500M每次，次数为2次
> ```linux
> famel@famel-virtual-machine:~$ sudo dd if=/dev/sdb1 of=/a.txt bs=500M count=2
>2+0 records in
>2+0 records out
>1048576000 bytes (1.0 GB, 1000 MiB) copied, 19.2122 s, 54.6 MB/s
>```
- 查看磁盘信息```df```
>-T: 查看磁盘系统信息,如```df -T```
>-h：查看磁盘大小和使用情况,如```df -h```
>-i：查看磁盘inode相关信息,如```df -i```
```linux
root@famel-virtual-machine:~# df -Th
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  973M     0  973M   0% /dev
tmpfs          tmpfs     199M  6.4M  193M   4% /run
/dev/sda6      ext4       23G  4.6G   17G  22% /
tmpfs          tmpfs     992M  196K  992M   1% /dev/shm
tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
tmpfs          tmpfs     992M     0  992M   0% /sys/fs/cgroup
/dev/sda5      ext4      453M   56M  370M  14% /boot
tmpfs          tmpfs     199M   52K  199M   1% /run/user/1000
/dev/sdb1      ext4      2.0G  3.0M  1.8G   1% /sdb1
```
- 挂载点(Mounted on)：
>个人理解：磁盘的入口，对磁盘进行存储删除等操作的通道
- 文件系统
>ext3、ext4：日志文件系统(写数据，先缓存，再写入到硬盘)
>xfs: 好像很牛逼的文件系统
>有兴趣再研究吧~~~
- 磁盘分区```fdisk```
>对象：为磁盘进行分区,如```fdisk /dev/sdb```,将为sdb这个磁盘进行分区
>-l：查看分区信息,如```fdisk -l /dev/sda ```

- 格式化磁盘文件系统```mkfs```
>.ext4 对象:将磁盘格式化为ext4系统,如```mkfs.ext4 /dev/sdb1```
>.xfs 对象:将磁盘格式化为xfs 系统,如```mkfs.xfs /dev/sdb1```
- 磁盘分区信息解析
>执行后```fdisk -l /dev/sda ```																																																																																																																										
```linux 
Disk /dev/sda: 25 GiB, 26843545600 bytes, 52428800 sectors
```
>```Disk /dev/sda```：表明 /dev/sda是一个磁盘
>```25 GiB```：磁盘大小为25GB
>```26843545600 bytes```,磁盘大小字节为26843545600
>```52428800 sectors```：有52428800 个扇区

```linux
Units: sectors of 1 * 512 = 512 bytes
```
>每一个扇区单位是512字节
```linux
Sector size (logical/physical): 512 bytes / 512 bytes
```
>扇区大小(逻辑/物理):512字节/512字节
```linux
I/O size (minimum/optimal): 512 bytes / 512 bytes
```
>I/O大小(最小/最佳):512字节/512字节
```linux
Disklabel type: dos
```
>磁盘标签类型:dos
>**dos的分区方式磁盘必须< 2T**
>若要对>2T的磁盘分区，需要使用命令```parted```,磁盘标签类型为gpt(了解)
```linux
Disk identifier: 0x60356c74
```
>磁盘标识符:0x60356c74

```linux
Device     Boot   Start      End  Sectors  Size Id Type
/dev/sda1  *       2048  3999743  3997696  1.9G 82 Linux swap / Solaris
/dev/sda2       4001790 52426751 48424962 23.1G  5 Extended
/dev/sda5       4001792  4976639   974848  476M 83 Linux
/dev/sda6       4978688 52426751 47448064 22.6G 83 Linux
```
- 磁盘主分区、扩展分区和逻辑分区
> 主分区：只能创建4个，且一旦创建了4个主分区后无法创建扩展分区（即使磁盘有剩余）
> 扩展分区：
> &emsp;&emsp;一般分完主分区后剩下的容量来创建扩展分区
> &emsp;&emsp;创建完扩展分区后，可以创建数个逻辑分区（非无限制建，跟磁盘有关）
> 逻辑分区：对扩展分区再次分区就是逻辑分区
- 磁盘分区操作
1. 执行命令```fdisk /dev/sdb ```，进行分区操作
>![在这里插入图片描述](https://img-blog.csdnimg.cn/20181122124139889.png)
> &emsp;欢迎使用fdisk（util-linux 2.27.1）。
> &emsp;更改将仅保留在内存中，直到您决定编写它们。
> &emsp;使用write命令前要小心。
> &emsp;设备不包含已识别的分区表。
> &emsp;创建了一个新的DOS磁盘标签，磁盘标识符为0x75af599d。
> &emsp;命令（输入m获取帮助）

2. 输入```m```可以查看帮助
>![在这里插入图片描述](https://img-blog.csdnimg.cn/20181122124428200.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01BU09STA==,size_16,color_FFFFFF,t_70)
3. 输入```p```可查看分区表信息
>![在这里插入图片描述](https://img-blog.csdnimg.cn/20181122124706447.png)
4. 输入```n```新建分区
>![在这里插入图片描述](https://img-blog.csdnimg.cn/20181122125016699.png)
>```0 primary, 0 extended,4free```：表名该磁盘有0个主分区，0个扩展分区，可以创建4个主分区
></br>
>输入```p```创建主分区
>```linux
>Partition number (1-4, default 1):            设置分区编号（默认1）
>First sector (2048-41943039, default 2048):  设置起始扇区（默认从2048开始）
>Last sector, +sectors or +size{K,M,G,T,P} (2048-41943039, default 41943039): +2G （设置扇区末尾，+2G表示2G大小的分区）
>Created a new partition 1 of type 'Linux' and of size 2 GiB. 创建了一个2G大小的分区
>```
>输入```e```创建扩展分区
>输入```l```创建逻辑分区（对扩展分区进行进一步分区）
>有4个主分区后（个人把扩展分区当成一个唯一的特殊可在分的主分区），再分区时直接进入创建逻辑分区
5. 输入```q```，退出分区操作且不保存
6. 输入```d```.删除分区
> ```linux
> Partition number (1,2, default 2):  通过分区编号进行删除，输入p可以查看分区编号
>```
7. 输入```w```,保存分区操作
8. 分区结束后，想要使用磁盘，还需要为磁盘添加挂载点

- 为磁盘添加挂载点```mount```
> 对象:为磁盘对象添加挂载点,如```mount /dev/sdb1 /sdb1```,把/sdb1设置为/dev/sdb1磁盘的挂载点
> 前提：
> 先创建一个/sdb1文件夹```mkdir```
> 对/dev/sdb1进行格式化系统。```mkfs```

- 卸载挂载点```umount```
>对象:为磁盘对象卸载挂载点,如```umount /dev/sdb1```
>-l：强制卸载(如磁盘正在使用时无法卸载)，如：```umount -l /dev/sdb1```
___
# 文件系统
- ext*文件系统命令:```tune2fs```
>-l：查看系统文件信息,如```tune2fs -l /sdb/sdb1```.假设sdb1是ext4文件系统
- 文件系统信息分析(执行```tune2fs -l /sdb/sdb1```)
>以下为截取的部分信息
>```inux
>Last mounted on:  上一次的挂载点
>Filesystem features: 文件系统特征
>Inode count: inode数量(每个文件都有Inode号，当inode用完了，即使磁盘有剩余空间也无法再添加文件)
>Block count:  块数量
*（操作系统实际上就是操作Block）*
- ```superblock```(ext4特有块)
>作用：管理其他块的状态
>当往磁盘写入数据时，首先通过superblock,确定哪些block状态为free

- ```inode block```
>用于存放文件的信息
>存放文件的inode号、文件类型、文件名、权限、属主、属组、指针等信息
>inode号：```ls -i```(查文件)或```ls -di```（查目录）可以查
>指针：指向directory blcok

- ```directory block ```
>对于文件，就是存放具体内容
>对于目录，就是存放目录名和指针名

- 例：```cat /a.txt```是如何执行的
> 1.通过根"/"的inode号，找到对应inode block
> 2.根据inode block的信息，判断权限，成功，则查看指针指向的directory block
> 3.因为根"/"是目录，查看directory中的a.txt是否存在，存在，则查看a.txt的inode号，找到对应的inode block
> 4.根据a.txt的inode block的信息，判断权限，成功，则查看指针指向的directory block
> 5.因为a.txt是文件，所以查看其内容

- 例:```rm -r /a.txt```是如何执行的
>a.txt对应的inode block状态变为free
>根目录对应的directory block中关于a.txt和对应inode的信息删除
>保存a.txt内容的block内容其实还在.所以明明删除了，其实还是有数据恢复的可能
---
# 内存
- 启动swap分区 ```swapon```
> 命令描述
> ```
>  enable devices and files for paging and swapping
>  启用设备和文件用于分页和交换
>```
> 选项：
> -s：显示交换设备相关信息,如```swapon -s```
> -a：激活swap分区,如```swapon -a /dev/sdb7```,假设/sdb7已经制作成swap分区了
-  禁用swap```swapoff ```
>命令描述
>```
>Disable devices and files for paging and swapping.
>禁用设备和文件进行分页和交换。
>```
>对象：禁用swap分区,如```swapoff /dev/sdb7```
>
- 制作swap分区```mkswap```
> 命令描述：
> ```
> set up a Linux swap area
> 设置Linux交换区域
>```
> 对象: 将磁盘制作成swap分区,如```mkswap /dev/sdb7```

- 显示内存信息:```free```
>命令描述:
>```
>Display amount of free and used memory in the system
>显示系统中空闲和已用内存的数量
>```
>
>选项：
>-m：以兆为单位输出信息.如```free -m```
>-k：以kb为单位输出信息,如```free -k```
>-w: 以宽屏的方式显示数据，此模式下buffer和cache会独立显示.如```free -wm```
- 保存内存信息的文件```/proc/meminfo```
- 信息解析
>![在这里插入图片描述](https://img-blog.csdnimg.cn/20181123151247772.png)
>
>```total```:总内存
>```used```:已使用内存
>```free```: 剩余内存
>```shared```：共享内存
>```buffer```:内核缓冲区使用的内存(Buffers in /proc/meminfo),用于存放要写入disk(块设备)的数据
>```cache```:页面缓存和slabs使用的内存(Cached and Slab in /proc/meminfo)，用于存放从disk读出的数据
>```avaliable```:预计还可以使用的内存
---
# 进程管理
- 查找进程```ps```
> 描述信息：
> ```
> report a snapshot of the current processes.
> 报告当前进程
> ```
> 使用BSD语法查看系统进程:
> ```ps au```或```ps aux```，后者显示进程更加完全

-过滤进程 ```grep```
>描述：
> ```
> lines matching a pattern
> 行模式匹配
> ```
> 如```ps aux | grep mysql```,表示查找与mysql相关的进程

-杀死进程 ```kill```
> ```
>  send a signal to a process
>向进程发送信号
>```
>-9: 强制删除信号,如```kill -9 3444```,表示发出强制杀死pid=3444进程的信号

-后台运行 ```&```
> 后台运行，如```firefox &```,那么firefox程序将会在后台运行

-查询后台运行进程 ```jobs```
>查看自己启动的后台进程(通过```&```方式启动的后台进程)
>杀死该进程方式,```kill -9 %1```,1对应jobs查询的进程信息
>```linux
>famel@famel-virtual-machine:~$ jobs
>[1]+  Stopped                 man
>```

- 以进程树方式显示进程```pstree```
>命令描述
>```
>display a tree of processes
>显示进程树
>```
>默认：查询所有,如```pstree```

- 查询pid```pgrep```
>命令描述:
>```
>look up  based on name and other attributes
>根据名称和其他属性查找（进程）
>```
>-d：只查询进程的pid(也是默认选项),如```pgrep mysql```,将会找出mysql的进程pid
---
# 包管理相关
centos:
- 安装二级制软件包```rpm```
>命令描述
>```
>RPM Package Manager
>RPM包管理工具
>```
>-i：安装rpm包
>-v：显示详细信息
>-h：显示安装进度条
>如```rpm -ivh xxx.rpm```
>-q: 显示安装软件的信息.如```rpm -qi mysql```，会显示安装的详细信息
>&emsp;&emsp;默认：查询是否安装了某个包，如```rpm -q mysql```
>&emsp;&emsp;选项:
>&emsp;&emsp;-l：显示软件包安装后的文件列表如```rpm -ql mysql```
>&emsp;&emsp;-a：显示所有包,如```rpm -qa mysql```
>&emsp;&emsp;-f：通过文件找其软件如```rpm -qf /usr/mysql/```
>-e：卸载安装的软件.如```rpm -e mysql```
- 技巧：卸载查询出来的软件```rpm -e `	rpm -qa | grep mysql` ```
>反引号`作用：将结果放到当前位置 
- 软件包的依赖
>例：随便找了个包安装
>```
>[root@famel Packages]# rpm -vih libreoffice-langpack-uk-4.3.7.2-2.el6_9.2.x86_64.rpm 
>error: Failed dependencies:
>	hunspell-uk is needed by libreoffice-langpack-uk-1:4.3.7.2-2.el6_9.2.x86_64
>	mythes-uk is needed by libreoffice-langpack-uk-1:4.3.7.2-2.el6_9.2.x86_64
>	hyphen-uk is needed by libreoffice-langpack-uk-1:4.3.7.2-2.el6_9.2.x86_64
>```
>可以看到，安装该包，需要```hunspell-uk、mythes-uk、hyphen-uk```三个包
>解决办法：只能一个个安装所需的依赖包，或使用```yum```安装

- ```yum```
>选要一个软件源，执行时会自动从源中寻找依赖
>yum自带的源：
>```
>[root@famel etc]# ls /etc/yum.repos.d/
>CentOS-Base.repo  CentOS-Debuginfo.repo  CentOS-fasttrack.repo  CentOS-Media.repo  CentOS->Vault.repo
>```
>clean all:清楚缓存```yum clean all```
>-y install: 安装软件包,如```yum -y install libreoffice-langpack-uk```,自动解决依赖性
>-y earse :卸载软件包，如```yum -y earselibreoffice-langpack-uk```,依赖包不卸载
>-y makecache:建立缓存，如```yum -y makecache```,提高装软件包速度（不用检索源）
>-y reinstall:重新覆盖安装```yum -y reinstall libreoffice-langpack-uk```
>-y update:更新源，```yum -y update ```
>list：查看安装的软件、

[LIUNX 回滚YUM软件操作，可完全卸载软件和依赖](https://blog.csdn.net/MASORL/article/details/84725243)
- yum源文件（/etc/yum.repos.d/xxx.repo）
>```linux
>name=   :源名
>baseurl=：源URl
>     本地：file:// + url,如file:///dev表示本地的/dev为源路径
>enable=1; 1表示开启yum源
>gpgcheck=0；不检测
>
- yum 配置文件(/etc/yum.conf)
>```linux
>[main]
>cachedir=/var/cache/yum/$basearch/$releasever 缓存路径
>keepcache=0 1表示保存缓存
>debuglevel=2
>logfile=/var/log/yum.log
>exactarch=1
>obsoletes=1
>gpgcheck=1
>plugins=1
>installonly_limit=5
>bugtracker_url=http://bugs.centos.org/set_project.php?project_id=19&ref=http://bugs.centos.org/bug_report_page.php?category=yum
>distroverpkg=centos-release
>```
- 自定义yum源```createrepo```
> 如```createrepo /a/d```,在通过.repo文件的baseurl指向该源就可以了

- 简单的自定义源操作：
>1. 建立文件夹，通过```createrpo```将该文件夹设置成源仓库
>2. 进入```etc/yum.repos.d/```目录，创建```xxx.repo```文件,指向刚刚建立的源仓库```baseurl = ```
>3. 清除下缓存```yum clean all```

ubuntu:
- deb包安装：```dpgk```
> 命令描述
> ```linux
> package manager for Debian
> Debian的包管理
> ```
> -i: 安装包,```dpkg -i mysql.deb```
> -r:删除包,```dpkg -r mysql```
> -p:删除包，并删除配置文件,```dpkg -p mysql```
> -L:列出关联文件，```dpkg -L mysql```
> -unpack:解压deb包,```dpkg -unpack mysql.deb```
> -S:搜书所属的包内容,```dpkg -S mysql```,就会查询mysql相关的文件
> -l:列出当前已安装的包,```dpkg -l```
> -c:列出deb包的内容，```dpkg -c mysql.deb```
> -configure ：配置包,```dpkg -configure mysql```

-```apt-get```
>命令描述
>```linux
>apt-get is a command line interface for retrieval of packages and information
> bout them from authenticated sources and >for installation, upgrade and 
> removal of packages together with their ependencies.
> apt-get是一个命令行界面，用于从经过身份验证的源检索包和信息，以及安装，升级和删除包及其依赖项。
>```
>选项
>install :安装,```apt-get install mysql```
>remove:卸载,```apt-get remove mysql```
> purge：卸载软件并删除配置
>update:更新软件源信息,```apt-get update```
>upgrade:系统升级，```apt-get upgrade```
>serch:搜索软件包,```apt-get search```
>clean:删除```var/cache/apt/archives```目录下的deb包（apt-get安装或升级时会把包下到这个路径）
>autoclean:删除```var/cache/apt/archives```目录下，已经不能从仓库中下载的deb包
>autoremove:删除不需要的依赖包
>