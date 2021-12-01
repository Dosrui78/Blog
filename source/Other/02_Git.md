#### 2.2 Git教程

> **Git是目前世界上最先进的分布式版本控制系统（没有之一）。**
>
> ![2.2.jpg](https://github.com/Dosrui78/Blog-Images/blob/master/2.2.jpg?raw=true)

---

##### 2.21 初始设置

###### 安装完成设置

```
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```

###### 初始化

把这目录变成Git可以管理的仓库。

```
$ git init
```

---

##### 2.22 提交设置

###### 把大象放到冰箱

把文件添加到Git仓库 

```
$git add readme.txt
```

###### 提交并添加描述

```
$git commit -m xxxx(xxx为描述)
```

###### 查看状态

```
$git status
```

###### 查看改动了哪里（先-后+）

```
$git diff
```

查看工作区和版本库里面最新版本的区别：

```
$git diff HEAD -- readme.txt
```

---

##### 2.23 后悔药（回退版本）

###### 回退至最近一次版本

```
$git reset --hard HEAD^ 
$git reset --hard 1024a(1024a为commitid号，可通过git log查看)
```

###### 记录每一次命令

```
$git reflog
```

----------

##### 2.24 撤销操作

###### 丢掉工作区的修改

就是回到近一次git commit或git add时的状态，下面两种写法发都可：

```
$git restore -- readme.txt
$git checkout -- readme.txt
```

> 若是已经把修改添加到了暂缓区（unstage），提交了就改不回去了
>
> 撤销掉把暂存区（unstage）的修改，重新放回工作区

 ```
 $git reset HEAD  <file>
 ```

> 最后再用git checkout -- readme.txt 来撤销工作区的修改。

---

##### 2.25 删除文件操作

###### 删除工作区

```
$rm test.txt
```

> 这时候用$git status查看会告诉你那些文件被删了
>
> 删除版本库，然后提交一次，文件就从版本库被删除了

```
$git rm test.txt
$git commit -m  "remove test.txt"
```

> 如果删错了 版本库里还有恢复可用(原理是用版本库替换工作区的版本)

```
$git checkout -- test.txt
```

---

##### 2.26 远程仓库

>  本地Git仓库和Github仓库是靠SSH加密的

###### 创建SSH Key:

```
$ssh-keygen -t rsa -C"youremail@example.com"
```

从Github克隆出新的仓库，也可以把一个已有的本地仓库与之关联，然后把本地仓库的内容推送到GitHub仓库

```
$git remote add origin git@github.com:Dosrui78/learngit.git
```

###### 第一次推送

```
$git push -u origin master
```

只要本地做了提交就可以通过：

```
$git push origin master
```

###### 删除远程库

###### 查看远程库信息：

```
$git remote -v
```

###### 删除操作

```
$git remote rm origin
```

###### 从远程库克隆

```
$git clone …… 
$git checkout -b dev创建新分支dev，效果等同于
$git branch dev 
$git checkout dev
```

-----

##### 2.27 创建合并分支

[廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/896043488029600/900003767775424)有图解

###### 新建分支（以下两个相同）

```
$git checkout -b dev
$git switch -c dev
```

等同于

```
$git branch dev
$git checkout dev
```

*一开始的时候，master分支是一条线，Git用master指向最新的提交，再用HEAD指向master，就能确定当前分支，以及当前分支的提交点：*

*master**随提交次数越来愈长，创建新分支时**HEAD**会指向新的分支，此时修改提交就是移动新的分支了，**master不动。*

查看当前分支

```
$git branch
```

###### 切换分支（以下两个相同）

```
$git checkout dev
$git switch dev
```

###### 把dev分支的工作成果合并到master分支

```
$git merge dev
```

###### 查看日志

```
git log --graph --pretty=oneline --abbrev--commit
```

---

##### 2.28 分支冲突

分支可视化，可以看到分支合并情况

只加graph

![2.28.png](https://github.com/Dosrui78/Blog-Images/blob/master/2.28.png?raw=true)

加graph和pretty=online

![2.28-1.png](https://github.com/Dosrui78/Blog-Images/blob/master/2.28-1.png?raw=true)

加–graph –pretty==online –abbrev-commit(abbrev缩写的意思，意思是缩写commit)

![2.28-2.png](https://github.com/Dosrui78/Blog-Images/blob/master/2.28-2.png?raw=true)

----

##### 2.29 分支管理策略

通常，合并分支时都会用Fast forward 模式，这种模式下删除分支后会丢掉分支信息，如果要强制禁用Fast forward模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。

合并时禁用Fast forward:

```
$git merge --no-ff -m "merge with no-ff" dev
```

----

##### 2.30 团队协作

> 现在，你的小伙伴要在dev分支上开发，就必须创建远程origin的dev分支到本地，于是他用这个命令创建本地dev分支：

```
$ git checkout -b dev origin/dev
```

因此，多人协作的工作模式通常是这样：

- 首先，可以试图用git push origin 推送自己的修改；

- 如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；

- 如果合并有冲突，则解决冲突，并在本地提交；

- 没有冲突或者解决掉冲突后，再用git push origin 推送就能成功！

- 如果git pull提示no tracking information，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream-to <branch-name> origin/<branch-name>`。

  这就是多人协作的工作模式，一旦熟悉了，就非常简单。

这就是多人协作的工作模式，一旦熟悉了，就非常简单。

---

##### 2.31 小结

> 查看远程库信息，使用git remote -v；
>
> 本地新建的分支如果不推送到远程，对其他人就是不可见的；
>
> 从本地推送分支，使用git push origin branch-name，如果推送失败，先用git pull抓取远程的新提交；
>
> 在本地创建和远程分支对应的分支，使用git checkout -b branch-name origin/branch-name，本地和远程分支的名称最好一致；
>
> 建立本地分支和远程分支的关联，使用git branch --set-upstream branch-name origin/branch-name；
>
> 从远程抓取分支，使用git pull，如果有冲突，要先处理冲突。