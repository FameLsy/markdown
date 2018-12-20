---
title: git命令大全
tags: 
- git
categories: 
- git
---
# 安装命令
**centos**:   
```linux
sudo yum install git
```
**unbuntu**
```linux
sudo apt-get install git
```
# 配置相关命令
```
git config
```
**系统级别的配置**（文件位于 */etc/gitconfig*)  
使用 *--system* 选项
```
 git config --system user.name "Fame Lee"
 git config --system user.email famelee@example.com
```

**当前用户级别的配置**（文件位于 *~/.gitconfig或 ~/.config/git/config* )  
使用 *--global*选项
```
 git config --global user.name "Fame Lee"
 git config --global user.email famelee@example.com
```

**当前仓库级别的配置**（文件位于 *.git/config* )
```
 git config user.name "Fame Lee"
 git config user.email famelee@example.com
 ```

**查看配置列表**  
使用选项 *--list*
```
git config --list
```
**查看特定配置**
```
git config user.name
```
**修改文本编辑器**
```
git config --global core.editor vim
```
# 帮助文档
```
 git help <verb>
 git <verb> --help
 man git-<verb>
```
# 初始化仓库
**在现有目录中初始化仓库**
```
git init
```
# 文件相关
## 文件状态/修改信息查看
**查看文件状态**
```
git status
```
**紧凑输出文件状态**
```git
git status -s
#或者
git status --short
```

注意：具体可以查看git-status解析文章  


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
## 文件暂存

```
git add [file-name]
git add [path]
```
## 文件提交
**提交更新**
```
git commit
```
**提交命令，并在提交更新文档显示git diff信息**  
```
git commit -v
```
**提交更新，并输入提交信息**
```git
git commit -m "提交信息"
```
**自动暂存追踪文件并提交**  
```
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
# 查看提交历史
```
git log
```
# 撤销操作
**重新提交**  
```git
git commit --amend
```
**取消暂存的文件(还原到暂存前的状态)**  
```
git reset HEAD <file-name>
```
**撤销对文件的修改(还原成上次提交时的内容)**  
```git
git checkout -- <file-name>
```
# 远程仓库
## 查看远程仓库
**查看已经配置的远程仓库简写**  
```g
git remote
```
**查看已经配置的远程仓库简写与其对应的 URL**
```git
git remote -v
```
## 添加远程仓库
**添加远程仓库，同时指定简写** 
```git
git remote add <remote-name> <url>
```
**克隆远程仓库**
```
# 克隆仓库到当前目录，仓库名默认
git clone url
# 自定义本地仓库名字
git clone url <repo-name>
```
## 从远程仓库抓取
**拉取远程仓库数据**  
```
git fetch <remote-name>
```
**自动的抓取然后合并远程分支到当前分支**
```
git pull <remote-name>
```

## 推送到远程仓库
```
git push [remote-name] [branch-name]
```
## 移除远程仓库
```
git remote rm <remote-name>
```
## 重命名远程仓库
```
# git remote rename <old-remote-name> <new-remote-name>
```
# 标签
## 查看标签
**在git中列出已有标签**
```git
git tag
```
**特定模式列出标签**
```git
git tag -l '关键字'
```
**查看标签信息与对应的提交信息**
```git
git show <tag-name>
```
## 创建标签
**创建附注标签**
```
git tag -a <tag-name>
```
**创建附注标签并指定存储在标签中的信息**  
```
git tag -a <tag-name> -m '标签中的信息'
```
**创建轻量标签**  
```
git tag <tag-name>
```
## 后期打标签
```
git tag -a <tag-name> 校验和
```
## 推送标签
**显式推送标签**
```
git push <remote-name> [tagname]
```
**推送多个标签**
```git
git push origin --tags
```
## 检出标签
**在特定的标签上创建一个新分支**
```
git checkout -b [branchname] [tagname] 
```
# Git 别名
```
 git config --global alias.[alias-name] [替代的内容]
```
# git分支
## 创建分支
```
git branch [branch-name]
```
## 分支切换
```
git checkout [branch-name]
```
**新建分支同时切换到该分支**：
```
git checkout -b [branch-name]
```
## 查看分支
**查看各个分支所指的提交对象**  
```
git log --oneline --decorate
```
**查看项目分叉历史**  
```
git log --oneline --decorate --graph --all
```
**查看当前所有分支**
```
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
**合并分支** 
```
git merge < branch-name >
```
## 删除分支
**删除已合并分支**
```
git branch -d < branch-name >
```
**强制删除未合并分支**
```
git branch -D < branch-name >
```
## 远程分支
**推送远程分支**
```
git push [remote-name] [branch-name]
```