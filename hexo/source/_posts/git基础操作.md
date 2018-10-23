title: git基础操作
categories: 
  - 版本控制
tag:
  - git
  - 操作
  - log
  - 分支
---

# 配置
---
配置文件的目录以及对应的级别：

|配置文件名称 |配置文件目录                         |对应修改命令            |优先级|
| ------          | ---------                    |------------           |------|
|版本库      |~\.git\config                       |git config --local     |  1    |
|全局        |C:\Users\username\gitconfig        |git config --global    |  2     
|系统        |\%Git%\mingw64\etc\gitconfig        |git config --system    |  3    

 
```C++
git config --system core.editor <path of specified editor> //设置配置项
git config --system --list //查看配置列表(如果没有指定第一个参数，默认为--global)
git config --show-origin core.editor(e.g.)  //查看某个配置项所在配置文件的源路径
```

# 文件操作
---
## 新增文件
```C++
git add [file]   //将文件增加到本地版本库
git pull       //从远程获取最新版本并合并到本地
git commit     //将修改从暂存区提交到本地
git push       //从本地版本库的分支推送到远程服务器上对应的分支
```
## 删除文件
```C++
git rm file    //删除本地库的文件
git pull
git commit
git push
```
## 修改文件
```C++
git status   //检查修改文件列表
git add -u   //添加修改过的文件到修改库
git status   //再次检测
git commit   //将修改从暂存区提交到本地版本库
git pull     //远程获取最新版本并合并到本地
git push     //从本地版本库的分支推送到远程服务器上对应的分支
```
> **注意**：git commit和git pull的顺序不影响提交的效果。

# log操作
---
## 基本命令
```C++
git log  //查看完整log
git log --pretty=oneline //每条只显示一行，内容是版本号和修改注释。git的版本号是SHA1计算出来的一长串数字，如附图。
git relog //显示执行的命令历史
```
## 其它命令
```C++
git log --oneline --decorate  //与git log --oneline效果相同
```


# 版本操作
---
## 回退版本
```C++
git reset --hard HEAD^        //回退到上一版本
git reset --hard HEAD^^       //回退到上上一个版本
git reset --hard HEAD~100     //往前回退100个版本
 
//假设查得一个版本号为64b47b2ed2492845c05c36f7bf93b6be313af855,回退到该版本的命令如下
git reset --hard 64b47b2 //git 会根据版本号前几位自动查找相应版本
git reset --soft [HASH]  //回退到某个版本，同时也保留本地的修改
```
## 文件前后对比
### 集成对比工具（kdiff3为例，beyondcompare同理）
```C++
git config --global diff.tool bc4
git config --global difftool.prompt false
git config --global --replace-all difftool.bc4.cmd '"F:\\ProgramFiles\\KDiff3\\kdiff3.exe" "$LOCAL" "$REMOTE"'

git config --global merge.tool bc4
git config --global mergetool.prompt false
git config --global --replace-all mergetool.bc4.cmd '"F:\\ProgramFiles\\KDiff3\\kdiff3.exe" "$LOCAL" "$REMOTE" "$BASE" "$MERGED"'
git config --global mergetool.bc4.trustexitcode true
```
### 对比相关操作
```C++
git difftool [file] //查看库上文件和本地文件的差异
git status          //查看是否有冲突，【unmerged paths下面都是冲突文件】
git mergetool       //依次打开所有冲突文件，供手动解决
```
# 本地文件操作
---
## 放弃本地修改
```C++
git stash      //所有未提交的修改暂存到stash里
git stash pop  //恢复stash里保存的修改
git checkout .
```
## 恢复本地删除的文件夹
```C++
//连续执行下面命令两次
git checkout -f -q -- vdUShape/   
```
## 忽略本地特定文件并上传
在根目录下的.gitignore文件定义需要忽略的文件。定义示例：
```C++
# data
grays.txt

# bin files
propsTest.exe

# images
result.jpg
```
# 分支操作

```C++
//分支的建立、切换、合并和删除
git checkout -b branchName1                   //建立分支，并切换到分支
git checkout -b branchName2                   //建立分支，并切换到分支
git checkout master                           //切换回主干
								             
git merge branchName1                         //将分支修改内容合并到主干上
git branch -d branchName1                     //branchName的内容已经合并到分支，没用了，删掉
								             
git branch branchName2                        //切换到分支2，继续修改。分支1的只合并到了主干，与分支2没有关系
git merge master                              //将提交到主干的分支1的内容合并到分支2上
								             
//分支信息查看                                
git branch -v                                 //查看分支最后一次提交
git --merged/--no-merged                      //查看已经合并到/未合并到当前分支的分支
git log $分支名/tag名/远程分支名                //查看分支log
git show-branch                               //查看分支log

//远程分支操作    
git push --delete origin branchName3          //删除远程分支
git branch -m branchName3  branchNewName3     //重命名本地分支
git push origin branchNewName3                //新命名分支提交到远程
//如遇冲突，查看github里setting里的默认分支是哪个

```

# 清理和重置
## 清理
```
//1. 查看但不删除untracked状态的文件
git clean -n
//2. 根据提示选项删除untracked状态的文件
git clean -i
```
## 重置
```C++
//1. 强制重置。相当于将本地文件全部清除，替换为库上最新提交版本。曾因为这个操作丢失了本地文件
git reset --hard HEAD

//2. 代码写到一半，忘记切换分支。这时，先提交到缓存区，再checkout一下
git stash & git checkout
```

# 问题

## 一、分支冲突
- 问题描述：
暂存区或工作目录里没有提交的修改可能会和即将切换（检出）的分支产生冲突，导致无法切换分支。
- 解决方法：
```C++
stashing
commit amending
```

## 二、HEAD游离
- 问题描述：
   checkout到某个分支（比如branch1）的时候，显示
```C++
error: Your local changes to the following files would be overwritten by checkout:
<文件列表>
Please commit your changes or stash them before you switch branches.
Aborting
```
查看分支列表，显示：
```C++
(HEAD detached at 12819b3)
master
branch1
```
- 解释
　　版本控制中一个很重要的问题是文件丢失，head游离可能导致文件丢失。
　　一般情况下，head指针指向当前分支的最新版本。如果试图checkout某一次提交（以id1表示），而不是某个分支，便发生head游离，即head指针不再像通常那样指向当前分支的最新版本。这时，当前分支是匿名的，以“head detached at id1”的形式命名。假设此后在匿名分支下作了修改，又由于某种操作（比如分支切换到master）离开了匿名分支，这些修改极有可能永久丢失。
　　如无必要，应尽量避免head游离。如必须使head游离，在恢复常态之前一定要做好已修改文件的备份或合并。
​
- 解决方法：
		1、新建分支temp;
		2、checkout到temp;
		3、将temp的修改commit到库上；
		4、checkout到branch1(之前想切换而切换失败的分支)；
		5、将temp合并到branch1;
		6、删除temp分支。
## 三、Unmerged paths（文件冲突）
- 问题描述
　　在commit的时候出现如下错误：
```C++
Unmerged paths:
  (use "git add <file>..." to mark resolution)
        both modified:   vmRectangle/vmRectangle.pro
        both modified:   vmRectangle/vmRectangle_p.cpp
```
　　查看本地文件，发现两个文件中有冲突部分。

- 解决方法
		1、git checkout head vmRectangle/vmRectangle.pro vmRectangle/vmRectangle_p.cpp
		2、用最新的正确文件替换两个冲突文件；
		3、执行之前需要执行的commit。这时，分支从master|MERGING状态变为正常的master状态。

