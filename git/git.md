---
title: git
tags: 
- git
categories: 
- git
---
# 起步GIt
## 相关概念了解
**Git存储数据的方式**：对当时的全部文件制作一个快照并保存这个*快照*的索引。 为了高效，如果文件没有修改，Git 不再重新存储该文件，而是只保留一个链接指向之前存储的文件。  
**Git保证数据完整性**：所有数据在存储前都计算校验和，然后以校验和来引用  
**Git 计算校验和的机制**：SHA-1 散列，这由 40 个十六进制字符（0-9 和 a-f）组成字符串  
**Git的三种状态**：已提交（committed）、已修改（modified）和已暂存（staged）  
**Git的三种工作区域**：
>1. Git 仓库（.git directory）：Git 用来保存项目的元数据和对象数据库的地方
>2. 工作目录(Working Directory)：对项目的某个版本独立提取出来的内容
>3. 暂存区域(Stagign Area)：保存了下次将提交的文件列表信息

![git三种工作区域](https://raw.githubusercontent.com/FameLsy/Images/master/git/areas.png    )

**基本的 Git 工作流程**
>1. 在工作目录中修改文件。
>2. 暂存文件，将文件的快照放入暂存区域。
>3. 提交更新，找到暂存区域的文件，将快照永久性存储到 Git 仓库目录
## 安装  
**centos**:   
```linux
sudo yum install git
```
**unbuntu**
```linux
sudo apt-get install git
```
# 初次运行前的git配置

## git config 工具
*Git* 自带一个 *git config* 的工具来帮助设置控制 *Git* 外观和行为的配置变量。  
1. */etc/gitconfig*文件：包含系统上每一个用户及他们仓库的通用配置。 如果使用带有 *--system* 选项的 *git config* 时，它会从此文件读写配置变量。  
2. *~/.gitconfig*或 *~/.config/git/config*文件：只针对当前用户。 可以传递 *--global* 选项让 *Git* 读写此文件。  
3. *.git/config* 文件：针对该仓库。  

每一个级别覆盖上一级别的配置，所以 *.git/config* 的配置变量会覆盖 */etc/gitconfig* 中的配置变量。  
也就是说 *.git/config*  > */etc/gitconfig* > */etc/gitconfig*

## 用户信息
当安装完 Git 应该做的第一件事就是设置用户名称与邮件地址。每一个 Git 的提交都会使用这些信息，并且它会写入到你的每一次提交中，不可更改：
```git
 #针对当前用户的操作
 git config --global user.name "Fame Lee"
 git config --global user.email famelee@example.com
 #如果对特定项目使用不同的用户名称与邮件地址，可以在哪个项目下执行不带--global的操作
 git config user.name "Fame Lee"
 git config user.email famelee@example.com
```
## 检查用户配置信息
如果需要检查配置，则输入
```git
git config --list
```
可能会看到重复变量名，因为有3个配置文件。  
如果想要查看某一个指定的变量，则输入*git config < key >*
```git
git config user.name
```

## 文本编辑器
配置默认编辑器，在 *git* 输入信息时会掉用。没有配置的话会使用系统默认的文本编辑器（通常是 *vim* ）,如果想要设置，例如 *Emacs* 编辑器，则输入以下命令  
```git
git config --global core.editor emacs
```


## 获取帮助
*git* 有三种方式获取帮助
```git 
#git help <verb>
#git <verb> --help
#man git -<verb>
git help config
```
# 获取Git仓库
一共有两种方法获取git项目的仓库。
1. 在现有项目或目录下导入所有文件到 Git 中；
2.  第二种是从一个服务器克隆一个现有的 Git 仓库

## 在现有目录中初始化仓库
初始化操作
```git
git init
```
该命令会创建一个 *.git* 子目录(存放 *git* 核心文件)。  

## 克隆现有仓库
克隆仓库的命令为 *git clone [url] [name]*
```git
# 克隆仓库到当前目录，仓库名默认
git clone https://github.com/demo/demo
# 自定义本地仓库名字
git clone https://github.com/demo/demo mydemo
```
执行完后有当前目录会有两个文件夹
1. demo文件夹：项目文件都会在这
2. .git文件夹：存放远程仓库的数据，从中读取最新版本的文件的拷贝

# 文件更新
## 文件的状态
在 *git* **工作目录**下的文件一共有两种状态：
1. 已跟踪：指那些被纳入了版本控制的文件，在上一次快照中有它们的记录，在工作一段时间后，它们的状态可能处于未修改，已修改或已放入暂存区。
2. 未跟踪：除已跟踪文件以外的所有其它文件都属于未跟踪文件，它们既不存在于上次快照的记录中，也没有放入暂存区
3. 初次克隆某个仓库的时候，工作目录中的所有文件都属于已跟踪文件，并处于未修改状态

**查看文件状态**
```git
git status
```
**紧凑输出文件状态**
```git
git status -s
#或者
git status --short
```
紧凑输出结果如下
```
 M README  #右M：文件被修改了但是还没放入暂存区
MM Rakefile #MM:在工作区被修改并提交到暂存区后又在工作区中被修改
A lib/git.rb #A 新添加到缓存区的文件
M lib/simplegit.rb  #左M：文件被修改了并放入了暂存区
?? LICENSE.txt #??：新添加但为加入缓存区的文件
```

## git的生命周期
如下图，可以看到
1. 处于 *Untrcked(未跟踪)* 的文件经过添*Add the file(追加文件)*, 追加的文件状态变为 *staged(暂存)*
2. 处于 *Unmodeifed(未更改)* 的文件经过 *Edit the file(修改文件)* 后，状态变为 *Modeified(已修改)* 
3. 处于*Modeified(已修改)*,经过*stage the file(暂存操作)*，会存入到暂存区，此时文件状态变为 *staged(暂存)*
3. 文件经过 *commit(提交后)*，变为*Unmodeifed(未更改)*  

![git生命周期](https://raw.githubusercontent.com/FameLsy/Images/master/git/lifecycle.png)
## 暂存文件

**添加内容到下一次提交中** : *git add*  
```git
# 参数为file
git add README
# 参数为path
git add /home/git/
```
经过该命令后，文件将会被跟踪且放入暂存区。所以该命令使用的效果有
1. 将单个文件变为跟踪状态，并放入暂存区
2. 将路径下所有文件递归设置成跟踪状态，并放入暂存区
3. 对于已经是跟踪状态的已经修改文件，放入暂存区  

**查看尚未暂存的文件更新**  
```git
git diff
```
**查看已暂存的将要添加到下次提交里的内容**
```git
git diff --cached
#或者，git16.1版本后适用
git diff --staged
```
注意，*git diff*比较的是工作目录文件的内容和暂存区文件的内容。
## 忽略文件
如日志文件之类的文件，无需纳入git管理。这种情况下，创建一个名为  *.gitignore* 的文件,列出要忽略的文件模式.
```
# 忽略以.a或.o为结尾的文件
*.[oa]
# 忽略以～结尾的文件
x~
```
文件 .gitignore 的格式规范：
1. 所有空行或者以 # 开头的行都会被 Git 忽略。
2. 可以使用标准的 glob 模式匹配。
3. 匹配模式可以以(/)开头防止递归。
4. 匹配模式可以以(/)结尾指定目录。
5. 要忽略指定模式以外的文件或目录,可以在模式前加上惊叹号(!)取反。

glob模式：
1. 星号(*)匹配零个或多个任意字符;
2. [abc] 匹配任何一个列在方括号中的字符(这个例子要么匹配一个 a,要么匹配一个 b,要么匹配一个 c);
3. 问号(?)只匹配一个任意字符;
4. 如果在方括号中使用短划线分隔两个字符,表示所有在这两个字符范围内的都可以匹配
(比如 [0-9] 表示匹配所有 0 到 9 的数字)。 
5. 使用两个星号(*) 表示匹配任意中间目录,比如`a/**/z` 可以匹配 a/z, a/b/z 或 `a/b/c/z`等。

[官方针对各种语言的忽略文件](https://github.com/github/gitignore)
## 提交更新
所谓的提交，实际上相当于本地的保存记录。  
在提交更新前，最好先运行一下 *git status*命令，查看是不是还有没暂存的文件
```git
git status
```
**提交命令**  
*git commit*之后，会启动文本编辑器以便输入本次提交的说明。  
```git
git commit
```
**提交命令，显示修改信息**  
*git commit*之后，会启动文本编辑器以便输入本次提交的说明。  
```
git commit -v
```

**将提交信息与命令放在同一行**
```git
git commit -m "提交信息"
```
**自动暂存追踪文件并提交**  
有时候使用暂存区域的方式有些繁琐，使用以下命令， *Git* 就会自动把所有已经跟踪过的文件暂存起来一并提交,从而跳过 git add 步骤
```git
git commit -a
```
## 移除文件
**删除跟踪状态，且同时删除工作区的的文件（未暂存）。**
```git
git rm file
```
**强制删除已暂存的文件**
```git
git rm -f file
```
**删除暂存区中的文件，不删除工作区的文件**
```git
git rm --cached file
```
**删除目录，使用blog模式**
```git
#删除 log/ 目录下扩展名为 .log 的所有文件(注意"/")
git rm log/\*.log
#删除以 ~ 结尾的所有文件
git rm \*~
```
## 移动文件
**移动文件**
```
git mv file path
```
**重命名**  
```git
#对文件进行重命名
git mv README.md README
```
实际上相当于运行了
```git
mv README.md README
git rm README.md
git add README
```
>注意：
>如果出现了not under version control,只是因为你的文件不是追踪文件而已，执行 git add file就能用了

## 查看提交历史
**基本信息**：每个提交的 *SHA-1 校验和*、*作者的名字*和*电子邮件地址*、*提交时间*以及*提交说明*  
查看提交历史适用命令（显示基本信息）,该命令会按提交时间列出所有的更新,最近的更新排在最上面。  
```git 
git log
```
**git log 的常用选项**  

|选项|说明|
|--|--|
|-p|按补丁格式显示每个更新之间的差异。|
|--stat|显示每次更新的文件修改统计信息。|
|--shortstat|只显示 --stat 中最后的行数修改添加移除统计。|
|--name-only|仅在提交信息后显示已修改的文件清单。|
|--name-status|显示新增、修改、删除的文件清单。|
|--abbrev-commit|仅显示 SHA-1 的前几个字符，而非所有的 40 个字符。|
|--relative-date|使用较短的相对时间显示（比如，“2 weeks ago”）。|
|--graph|显示 ASCII 图形表示的分支合并历史。|
|--pretty|使用其他格式显示历史提交信息。可用的选项包括 oneline，short，full，fuller 和 format（后跟指定格式）。|


如，使用 *-p* 来显示每次提交的内容差异（基本信息+diff）。
```git
#显示最近的两次提交的内容差异 
git log -p 
```
适用 *--stat*查看每次提交的简略统计信息（基本信息+简略改动,如插入几行，几个文件修改，删除几行）
```git
git log --stat
```
**不同格式显示历史**

使用不同于默认格式的方式展示提交历史 *git log --pretty=[ option ]*
```
# oneline显示一行，包含SHA-1校验值和提交说明
git log --pretty=oneline
# short 包含SHA-1校验值、作者名字和邮件、提交说明
git log --pretty=short
# full 包含SHA-1校验值、作者名字和邮件、提交说明、提交者名字和邮件
git log --pretty=full
# fuller 包含SHA-1校验值、作者名字和邮件、提交说明、提交者名字和邮件、提交时间
git log --pretty=fuller
# format 自定义输出格式
git log --pretty=format:"%h - %an, %ar : %s"
```
**自定义输出格式**
|选项|说明|
|--|--|
|%H|提交对象（commit）的完整哈希字串|
|%h|提交对象的简短哈希字串|
|%T|树对象（tree）的完整哈希字串|
|%t|树对象的简短哈希字串|
|%P|父对象（parent）的完整哈希字串|
|%p|父对象的简短哈希字串|
|%an|作者（author）的名字|
|%ae|作者的电子邮件地址|
|%ad|作者修订日期（可以用 --date= 选项定制格式）|
|%ar|作者修订日期，按多久以前的方式显示|
|%cn|提交者（committer）的名字|
|%ce|交者的电子邮件地址|
|%cd|提交日期|
|%cr|提交日期，按多久以前的方式显示|
|%s|提交说明|



**限制输出长度**

|选项|说明|
|--|--|
|-(n)|仅显示最近的 n 条提交|
|--since, --after|仅显示指定时间之后的提交。|
|--until, --before|仅显示指定时间之前的提交。|
|--author|仅显示指定作者相关的提交。|
|--committer|仅显示指定提交者相关的提交。|
|--grep|仅显示含指定关键字的提交|
|-S|仅显示添加或移除了某个关键字的提交|

如，列出最佳两周提交
```
git log --since=2.weeks
```

## 图形化方式查看git提交
```
#当前分支
gitk 
#所有分支
gitk --all
```

## 变更操作

**重新提交**  
实为再次提交暂存区的文件，并重写覆盖上一次的提交信息(commit对象会变)
```git
git commit --amend
```

**修改老旧commit**
如果要更改老旧commit的提交信息，请看变基操作

**整合多个commit提交**
也看变基操作


**取消暂存的文件**  
```git
git reset HEAD <file-name>
```
**撤销对文件的修改**  
该命令会将文件还原成上次提交时的样子
```git
git checkout -- <file-name>
```
# 远程仓库的使用
远程仓库是指托管在因特网或其他网络中的你的项目的版本库。
## 查看远程仓库
**查看已经配置的远程仓库简写**  
对于使用 *git clone*过来的仓库，默认名为*origin*
```git
git remote
```
**查看已经配置的远程仓库简写与其对应的 URL**
```git
git remote -v
```
**列出远程仓库的 URL 与跟踪分支的信息**
```git
# git remote show [remote-name] 
```
## 添加远程仓库
**添加远程仓库，同时指定简写** 
```git
git remote add <remote-name> <url>
```
现在，可以用 *< remote-name >* 来代替整个URL.  
## 从远程仓库抓取
**拉取远程仓库数据**  
拉取远程仓库中有而本地仓库没有的信息.执行完成后，将会拥有那个远程仓库中所有分支的引用，可以随时合并或查看。
```git
#git fetch <remote-name>
git fetch pb
```
抓取之后，，本地不会自动生成一份可编辑的副本，需要进行合并操作  
**克隆远程仓库**  
对于 *git clone*命令添加的远程仓库会以 *orgin* 为简写。它自动设置本地 master 分支跟踪克隆的远程仓库的 master 分支
```
git clone [url]
```
**自动的抓取然后合并远程分支到当前分支**
```
git pull <remote-name>
```
## 推送到远程仓库
**推送到远程仓库**
```git
# git push [remote-name] [branch-name]
```
 *git push*命令生效，还需要两个条件
 1. 拥有所克隆服务器的写入权限
 2. 之前没有人推送过

 如果在推送之前，有人已经推送过。那必须先进行抓取操作，合并后才能推送。
 

## 远程仓库的移除与重命名
**重命名远程仓库**  
修改远程仓库的简写名,同时也会改变远程分支名
```git
# git remote rename <old-remote-name> <new-remote-name>
```
**移除远程仓库**
```git
# git remote rm <remote-name>
```

# 标签
Git 使用两种主要类型的标签：轻量标签（lightweight）与附注标签（annotated）。  
1. 轻量标签：很像一个不会改变的分支 - 它只是一个特定提交的引用
2. 附注标签：存储在 Git 数据库中的一个完整对象。 它们是可以被校验的；其中包含打标签者的名字、电子邮件地址、日期时间；还有一个标签信息；并且可以使用 GNU Privacy Guard （GPG）签名与验证。

## 查看标签
**在git中列出已有标签**
```git
git tag
```
**特定模式列出标签**
```git
# git tag -l '关键字'
```
**查看标签信息与对应的提交信息**
```git
# git show <tag-name>
```
## 附注标签
**创建附注标签**
```git
# git tag -a <tag-name>
```
**创建附注标签并指定存储在标签中的信息**  
```
# git tag -a <tag-name> -m '标签中的信息'
```
## 轻量标签
**创建轻量标签**  
创建轻量标签，只需要提供标签名字
```git
$ git tag <tag-name>
```
## 后期打标签
**后期打标签**  
对过去的提交打标签(在命令的末尾指定提交的校验和（或部分校验和）)
```git
# git tag -a <tag-name> 校验和,校验和可以通过git log 命令查看
git tag -a v1.2 9fceb02
```

## 共享标签
默认情况下，git push 命令并不会传送标签到远程仓库服务器上  
**显示推送标签**
```git
#  git push <remote-name> [tagname]
```
**一次性推送很多标签**  
这将会把所有不在远程仓库服务器上的标签全部传送到那里
```git
git push origin --tags
```

## 检出标签
标签并不会像分支一样完全移动，但想要工作目录与仓库中特定的标签版本完全一样，可以在特定的标签上创建一个新分支
```git
#git checkout -b [branchname] [tagname] 
```
不过当该分支提交后，标签和分支又会稍微有些不一样了
# Git 别名
通过 git config 文件来轻松地为每一个命令设置一个别名
```git
$ git config --global alias.co checkout
$ git config --global alias.br branch
$ git config --global alias.ci commit
$ git config --global alias.st status
```
如，想要输入 *git commit* 时，只需要输入 *git ci*  
又如
```git
# 向 Git 中添加取消暂存别名
git config --global alias.unstage 'reset HEAD --'
```
下面两个命令等价
```git
git unstage fileA
git reset HEAD -- fileA
```
想要执行外部命令，而不是一个 Git 子命令。 如果是那样的话，可以在命令前面加入 ! 符号
```git
git config --global alias.visual '!gitk'
```

# Git分支
分支意味着你可以把你的工作从开发主线上分离开来，以免影响开发主线
## Git数据保存的方式
在进行提交操作时，Git 会保存一个提交对象（commit object）。且该提交对象会保存  
1. 包含一个指向暂存内容快照的指针
2. 包含了作者的姓名和邮箱、提交时输入的信息
3. 指向它的父对象的指针
4. 一个保存校验和的树对象的指针

从下图（3个文件的提交）可以看出，对于一个提交对象，里面保存了一个*tree*的指针,而*tree*中有保存了文件的校验和
![commit-and-tree](https://raw.githubusercontent.com/FameLsy/Images/master/git/commit-and-tree.png)

而对于每一个的提交，除第一次外都有指向父对象的指针
![commits-and-parents.png](https://raw.githubusercontent.com/FameLsy/Images/master/git/commits-and-parents.png))

## 创建分支
**master分支**  
 Git 的默认分支（注意：Git 的 “master” 分支并不是一个特殊分支。 它就跟其它分支完全没有区别，只是默认创建了这个分支而已）  
**创建分支** ： *git branch < branch-name >*
```git
#创建了testing分支
git branch testing
```
创建一个分支(实际上只是在当前所在的提交对象上创建一个指针)，相当于如图所示  
![two-branches.png](https://raw.githubusercontent.com/FameLsy/Images/master/git/two-branches.png)

 **特殊指针HEAD**  
作用：指向当前所在的本地分支，即Git用它来确定当前所在的分支。  

## 分支切换
切换分支其实相当于改变HEAD的指针，同时会改变你工作目录中的文件  
**切换到分支**： *git checkout < branch-name >*
```
git checkout testing
```
**新建分支同时切换到该分支**： *git checkout -b < branch-name >*
```
git checkout -b testing
```
相当于执行了如下两条命令
```
git branch testing
git checkout testing
```
## 查看分支
**查看各个分支所指的提交对象**  
 提供该功能的选项是 --decorate
```
git log --oneline --decorate
```
**查看项目分叉历史**  
```
git log --oneline --decorate --graph --all
```
**查看当前所有分支**
```
#输出结果前*表示当前所在分支
git branch
```
**要查看每一个分支的最后一次提交**
```
git branch -v
```
**查看哪些分支已经合并/未合并到当前分支**
```
#合并的分支
git branch --merged
#未合并的分支
git branch --no-merged 
```
## 合并分支
**合并分支** : *git merge < branch-name >*
```
git merge hotfix
```
## 合并冲突
**遇到冲突时的分支合并**  
在两个不同的分支中，对同一个文件的同一个部分进行了不同的修改，需要手动去处理冲突。  
使用 *git status*查看那些因包含合并冲突而处于未合并（unmerged）状态的文件  
解决了所有文件里的冲突之后，对每个文件使用 *git add* 命令来将其标记为冲突已解决  
**使用图形化工具解决冲突**  
运行图形化工具
```
git mergetoo
```
## 删除分支
**删除分支** ： *git branch -d < branch-name >*
```
git branch -d hotfix
```
该命令无法删除当前分支未合并的分支，如果一定要删除，需要适用强制删除  
**强制删除** ： *git branch -D < branch-name >*
```
git branch -D hotfix
``` 
## 远程分支
**远程引用的完整列表** : *git ls-remote < remote-name >*
```
git ls-remote htofix
```
**获得远程分支信息** : *git remote show < remote-name >*
```
git remote show origin
```

**远程跟踪分支**  
1. 远程分支状态的引用，上次连接到远程仓库时，那些分支所处状态的书签.
2. 以 (remote)/(branch) 形式命名
3. 无法移动的本地引用，当你做任何网络通信操作时，它们会自动移动
4. 其实就相当于一个记录远程仓库分支位置的书签

## 跟踪分支
1. 从一个远程跟踪分支检出一个本地分支会自动创建一个叫做 “跟踪分支”（有时候也叫做 “上游分支”）。 
2. 跟踪分支是与远程分支有直接关系的本地分支。 
3. 如果在一个跟踪分支上输入 *git pull*，Git 能自动地识别去哪个服务器上抓取、合并到哪个分支。


## 变基
```
git rebase -i 提交的hashcode
```

在编辑之后，会弹出一个文本,里面告诉你该如何用命令
```
pick 5e358bb [添加]添加了MyBaits相关笔记
pick 9c77fd9 [.]

# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup <commit> = like "squash", but discard this commit's log message
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
# .       message (or the oneline, if no original merge commit was
# .       specified). Use -c <commit> to reword the commit message.
```

想要修改提交信息，就使用r这个命令
```
pick 5e358bb [添加]添加了MyBaits相关笔记
pick 9c77fd9 [.]
# 修改5e358bb的提交信息
r 5e358bb [添加]添加了MyBaits相关笔记
```

想要合成多个commit，则要使用s命令

从变基的位置开始，所有的commit对象都改变了其地址。
所以，当项目已经push上了，千万别用，在本地用用就可以了。

# 服务器上的Git
**裸仓库（bare repository）**  
即为一个没有当前工作目录的仓库。 因为该仓库仅仅作为合作媒介，不需要从磁碟检查快照；存放的只有 Git 的资料  
**协议**  
Git 可以使用四种主要的协议来传输资料：*本地协议（Local）*，*HTTP 协议*，*SSH（Secure Shell）协议*及 *Git 协议*。  
## 本地协议
其中的远程版本库就是硬盘内的另一个目录,常见于团队每一个成员都对一个共享的文件系统（例如一个挂载的 NFS）拥有访问权，或者比较少见的多人共用同一台电脑的情况。  
**克隆一个本地版本库**
```
git clone /opt/git/project.git
# 或者,file://传输效率较低
git clone file:///opt/git/project.git
```
**增加一个本地版本库到现有的 Git 项目**
```
git remote add local_proj /opt/git/project.git
```
## HTTP 协议
Git 通过 HTTP 通信有两种模式:“智能” HTTP 协议和“哑” HTTP 协议  
**智能（Smart） HTTP 协议**  
运行在标准的 HTTP/S 端口上并且可以使用各种 HTTP 验证机制  
**哑（Dumb） HTTP 协议**  
web 服务器仅把裸版本库当作普通文件来对待，提供文件服务。 哑 HTTP 协议的优美之处在于设置起来简单。 基本上，只需要把一个裸版本库放在 HTTP 根目录，设置一个叫做 post-update 的挂钩就可以了
```
cd /var/www/htdocs/
git clone --bare /path/to/git_project gitproject.git
cd gitproject.git
mv hooks/post-update.sample hooks/post-update
chmod a+x hooks/post-update
```
**SSH 协议**  
用 SSH 协议作为传输协议  
通过 SSH 协议克隆版本库，你可以指定一个 ssh:// 的 URL：  
```
git clone ssh://user@server/project.git
```
或者使用一个简短的 scp 式的写法：  
```
git clone user@server:project.git
``` 
**Git 协议**  
包含在 Git 里的一个特殊的守护进程；它监听在一个特定的端口（9418），类似于 SSH 服务，但是访问无需任何授权  
要让版本库支持 Git 协议，需要先创建一个 git-daemon-export-ok 文件

## 在服务器上搭建 Git
在开始架设 Git 服务器前，需要把现有仓库导出为裸仓库  
```
$ git clone --bare my_project my_project.git
```
把裸仓库放到服务器上并设置协议
```
scp -r my_project.git user@git.example.com:/opt/git
``` 

## 生成SSH公钥
**查看是否有密钥**  
执行以下命令查看是否拥有
```
cd ~/.ssh
ls
```
如果没有这个目录,或者有目录，但没有*id_dsa*和*id_rsa*文件，说明你没有公钥。  
**创建**  
```
ssh-keygen
```
(创建过程中会让你输入密钥口令，可以直接按空格不输入)  
现在，在目录下，起码有两个文件存在了
```
-rw-------  1 famel famel 1675 12月 17 11:48 id_rsa
-rw-r--r--  1 famel famel  409 12月 17 11:48 id_rsa.pub
```
以 *.pub* 为扩展名则为公钥，另一个则为私钥。

