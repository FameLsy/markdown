对查看文件详细修改信息的指令,一共有两种  
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
# 实验一：git diff 与 git diff --staged对比
**准备工作**
```
#创建一个文件
touch diff
#添加到暂存
git add diff
#修改文件
vim diff
```
**使用git diff**
```
git diff
git diff --staged
```
**结果**  
*git diff*：显示修改内容  
*git diff --staged*：没有显示任何内容
```
famel@famel-virtual-machine:~/git$ git diff
diff --git a/diff b/diff
index e69de29..083eef7 100644
--- a/diff
+++ b/diff
@@ -0,0 +1,7 @@
+
+添加的内容
+添加的内容
+添加的内容
+添加的内容
+添加的内容
+添加的内容
````
**第二次准备**
```
#将文件加入到暂存区
git add diff
```
**结果**  
*git diff*：没有显示任何内容  
*git diff --staged*：显示修改内容  
```
famel@famel-virtual-machine:~/git$ git diff --staged
diff --git a/diff b/diff
index e69de29..f960909 100644
--- a/diff
+++ b/diff
@@ -0,0 +1,6 @@
+
+添加的内容
+添加的内容
+添加的内容
+添加的内容
+添加的内容
```
