如下图的文件的四种状态

![git生命周期](https://raw.githubusercontent.com/FameLsy/Images/master/git/lifecycle.png)

# 实验一 验证Untracker状态
**创建一个新的文件**
```
#创建一个名为git的初始化仓库
mkdir git && cd git && git init
#新建文件并追踪文件
touch libai.txt
```
**查看状态**
```
git status
```
**结果**  
 *Untracked files* :该语句下的为处于 *Untracked*，没有追踪的文件
```
famel@famel-virtual-machine:~/git$ git status
On branch master

Initial commit

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	libai.txt

nothing added to commit but untracked files present (use "git add" to track)
```
# 实验二 验证Staged状态
**追踪新文件**  
(*libai.txt* 为第一个实验的创建文件)
```
git add libai.txt
```
**查看状态**
```
git status
```
**结果**  
*Changes to be committed:* 该语句下的为处于 *Staged* ，需要提交的文件
```
famel@famel-virtual-machine:~/git$ git status
On branch master

Initial commit

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

	new file:   libai.txt
```

# 实验三 验证Modefied状态 
**提交文件**
(继第二个实验的文件,将 *libai.txt* 文件*commmit*  )
```
git commit libai.txt
```
提交过后，文件会处于 *Unmodefide* 状态  
**修改文件内容**
```
vim libai.txt
```
**查看状态**
```
git status
```
**结果**  
Untracked files:该语句下的为处于 *Untracked*，没有追踪的文件
```
famel@famel-virtual-machine:~/git$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   libai.txt

no changes added to commit (use "git add" and/or "git commit -a")
```
# 实验五 验证git status -s的各种状态
**紧凑模式下的4种状态**

1. 右M：文件被修改了但是还没放入暂存区
2. 左M：文件被修改了并放入了暂存区
3. A：新添加到缓存区的文件
4. ??：新添加但为加入缓存区的文件

**创建文件**
```
# 创建rightM文件(右M)
touch rightM.txt
git add rightM.txt
git commit
# 创建leftM文件(左M)
touch leftM.txt
git add leftM.txt
git commit
vim letfM.txt
git add leftM.txt
# 创建staged文件(A)
touch staged.txt
git add staged.txt 
# 创建untracked文件(??)
touch untracked.txt
```
**查看紧凑型输出**
```
git stauts -s
```
**结果**

```
M  leftM.txt
 M rightM
A  staged.txt
?? untrack.txt
```
**对比正常输出**
```
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	modified:   leftM.txt
	new file:   staged.txt

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   rightM

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	untrack.txt
```