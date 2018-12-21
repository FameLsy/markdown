本地仓库的提交命令，常用的有一下三种提交  
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
# 实验一：对比三种提交方式
**准备工作**
```
# 创建commit文件
touch commit
# 加入暂存区
git add commit
# 修改commit文件
vim commit
# 加入暂存区
git add commit 
```
**git commit 提交**

执行后会以文本编辑器打开一个文件，光标停留在空白行处，用于输入提交信息（提交信息必须填，否则提交失败）
```

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# On branch master
# Changes to be committed:
#       new file:   commit
#
```
**git commit -v 提交**  
比起 *git commit* 提交，提交信息编辑文件多出修改的内容（提交信息必须填，否则提交失败）
```
# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# On branch master
# Changes to be committed:
#       new file:   commit
#
# ------------------------ >8 ------------------------
# Do not touch the line above.
# Everything below will be removed.
diff --git a/commit b/commit
new file mode 100644
index 0000000..5b99794
--- /dev/null
+++ b/commit
@@ -0,0 +1,7 @@
+
+添加的内容
+添加的内容
+添加的内容
+添加的内容
+添加的内容
+添加的内容
```
**git commit -m "提交内容"**  
不会跳出提交信息编辑文件，直接提交成功
```
famel@famel-virtual-machine:~/git$ git commit -m "新的测试"
[master 77b59f2] 新的测试
 1 file changed, 7 insertions(+)
 create mode 100644 commit
```
**再次修改文件**  
（注意，只提交不暂存）
```
vim commit
```
**git commit -a 提交**  
自动暂存了所有已经追踪过的文件，且打开了提交信息编辑文件
```
# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# On branch master
# Changes to be committed:
#       modified:   commit
#
```

