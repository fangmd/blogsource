---
title: Git 使用方法
date: 2016-03-28 08:00:35
tags: git
categories: program

---

# git delete remote branch

```
git push -d <remote_name> <branch_name>
```

# git tag 使用

查看 tag：

```
git tag

git tag -l 'v1.4.2.*'
```

添加一个 Tag：

```
git tag -a v1.4 -m 'my version 1.4'
```

push a tag:

>默认情况下，git push 并不会把标签传送到远端服务器上，只有通过显式命令才能分享标签到远端仓库。

```
git push origin [tagname]

git push origin --tags

git push --tags
```

delete a tag:

```
git push --delete origin [tag_name]

git tag -d [tagName]
```

# git fork 过来的代码如何同步作者的代码

```
git remote add upstream [作者的 git 仓库地址]

git fetch upstream

git checkout master

git merge upstream/master

git push origin master

```

# 如何把已经加入到 git 的文件设置成 ignore

```
1. 在 ignore 文件中添加忽略
2. git rm -r --cached 要忽略的文件
3. git add .
4. git commit -m'...'
5. git push ...
```

# ssh-add 未开启

```
Error connecting to agent: Connection refused
```

处理：

```
eval `ssh-agent -s`
ssh-add
```

# git 强制 push 本地文件覆盖 remote 所有文件，以及历史版本信息

```
git push <remote> <branch> --force-with-lease
```

# git 强制 pull 远程文件 覆盖 本地文件

```
git fetch --all
git reset --hard origin/master
```

# git 取消单前的所有修改（未 add，commit）

```
git checkout -- .

// 恢复单个文件
git checkout -- <FILE>
```

# git 将本地的一个分支 push 到远程，并在远程创建同名分支


例子：直接推送，如果远程没有就会创建新的分支

```
git push origin v1.1
```

# git 各个状态之间转换

[http://www.netpi.me/uncategorized/gitrevoke/](http://www.netpi.me/uncategorized/gitrevoke/)

![http://ok455n4km.bkt.clouddn.com/2017-01-21-lifecycle.png](http://ok455n4km.bkt.clouddn.com/2017-01-21-lifecycle.png)


基本状态标识

- A- = untracked 未跟踪
- A = tracked 已跟踪未修改
- A+ = modified - 已修改未暂存
- B = staged - 已暂存未提交
- C = committed - 已提交未PUSH


各状态之间变化

- A- -> B : git add <FILE>
- B -> A- : git rm --cached <FILE>
- B -> 删除不保留文件 : git rm -f <FILE>
- A -> A- : git rm --cached <FILE>
- A -> A+ : 修改文件
- A+ -> A : git checkout -- <FILE> (git checkout -f <FILE> )
- A+ -> B : git add <FILE>
- B -> A+ : git reset HEAD <FILE>
- B -> C : git commit
- C -> B : git reset --soft HEAD^
修改最后一次提交:git commit --amend


# 更新本地代码的正确方式

参考：[http://blog.csdn.net/u012150179/article/details/17172211](http://blog.csdn.net/u012150179/article/details/17172211)

1. 查看远程仓库

```
$ git remote -v
eoecn   https://github.com/eoecn/android-app.git (fetch)
eoecn   https://github.com/eoecn/android-app.git (push)
origin  https://github.com/com360/android-app.git (fetch)
origin  https://github.com/com360/android-app.git (push)
su@SUCHANGLI /e/eoe_client/android-app (master)
```

2. 从远程获取最新版本到本地

```
$ git fetch origin master:temp
From https://github.com/com360/android-app
 * [new branch]      master     -> temp
su@SUCHANGLI /e/eoe_client/android-app (master)
```

从远程的origin仓库的master分支下载到本地并新建一个分支temp


3. 比较本地的仓库和远程参考的区别

```
$ git diff temp
su@SUCHANGLI /e/eoe_client/android-app (master)
```

4. 合并temp分支到master分支

```
$ git merge temp
Already up-to-date.
su@SUCHANGLI /e/eoe_client/android-app (master)
```

5. 如果不想要temp分支了，可以删除此分支

```
$ git branch -d temp
Deleted branch temp (was d6d48cc).
su@SUCHANGLI /e/eoe_client/android-app (master)
```



# 合并 commit

比如:
```
DoubledeMacBook-Pro:temp double$ git log
commit a9d2a39eebac2871beba9b6503f1a4807be5a134
Author: Double <fangmingdong0224@gmail.com>
Date:   Mon Feb 6 16:05:12 2017 +0800

    4

commit 03249d24d0ed5993f1db55d88bba86c116f3ef70
Author: Double <fangmingdong0224@gmail.com>
Date:   Mon Feb 6 16:01:15 2017 +0800

    This is a combination of and 3

commit f5ae5cdc5b95d6f0ac2e29913b7084ff3be1f748
Author: Double <fangmingdong0224@gmail.com>
Date:   Mon Feb 6 16:00:45 2017 +0800

    first commit
```


要合并前两个 commit:
```
git rebase -i f5ae5cdc5b95d6f0ac2e29913b7084ff3be1f748
```

出现的界面：
```
  1 pick 03249d2 This is a combination of and 3
  2 pick a9d2a39 4
  3
  4 # Rebase f5ae5cd..a9d2a39 onto f5ae5cd (2 command(s))
  5 #
  6 # Commands:
  7 # p, pick = use commit
  8 # r, reword = use commit, but edit the commit message
  9 # e, edit = use commit, but stop for amending
 10 # s, squash = use commit, but meld into previous commit
 11 # f, fixup = like "squash", but discard this commit's log message
 12 # x, exec = run command (the rest of the line) using shell
 13 # d, drop = remove commit
 14 #
 15 # These lines can be re-ordered; they are executed from top to bottom.
 16 #
 17 # If you remove a line here THAT COMMIT WILL BE LOST.
 18 #
 19 # However, if you remove everything, the rebase will be aborted.
 20 #
 21 # Note that empty commits are commented out
```

说明：

- pick 的意思是要会执行这个 commit
- squash 的意思是这个 commit 会被合并到前一个commit

前两行修改成：表示将第二条commit合并到第一条中, `wq`保存退出
```
  1 pick 03249d2 This is a combination of and 3
  2 s a9d2a39 4
```

出现编辑 commit 信息的界面：
```
  2 # The first commit's message is:
  3
  4 This is a combination of and 3
  5
  6 # This is the 2nd commit message:
  7
  8 4
  9
 10 # Please enter the commit message for your changes. Lines starting
 11 # with '#' will be ignored, and an empty message aborts the commit.
 12 #
 13 # Date:      Mon Feb 6 16:01:15 2017 +0800
 14 #
 15 # interactive rebase in progress; onto f5ae5cd
 16 # Last commands done (2 commands done):
 17 #    pick 03249d2 This is a combination of and 3
 18 #    s a9d2a39 4
 19 # No commands remaining.
 20 # You are currently editing a commit while rebasing branch 'master' on 'f5ae    5cd'.
 21 #
 22 # Changes to be committed:
 23 #       modified:   temp
 24 #
```

编辑commit 信息：
```
     new commit message .....
  2 # The first commit's message is:
  3
  4 # This is a combination of and 3
  5
  6 # This is the 2nd commit message:
  7
  8 # 4
  9
 10 # Please enter the commit message for your changes. Lines starting
 11 # with '#' will be ignored, and an empty message aborts the commit.
 12 #
 13 # Date:      Mon Feb 6 16:01:15 2017 +0800
 14 #
 15 # interactive rebase in progress; onto f5ae5cd
 16 # Last commands done (2 commands done):
 17 #    pick 03249d2 This is a combination of and 3
 18 #    s a9d2a39 4
 19 # No commands remaining.
 20 # You are currently editing a commit while rebasing branch 'master' on 'f5ae    5cd'.
 21 #
 22 # Changes to be committed:
 23 #       modified:   temp
 24 #
```
`wq` 保存退出

最后查看 git log：
```
DoubledeMacBook-Pro:temp double$ git log
commit 87664e5ca21369ebc5bb171292b59489dc0c9d89
Author: Double <fangmingdong0224@gmail.com>
Date:   Mon Feb 6 16:01:15 2017 +0800

    new commit message...

commit f5ae5cdc5b95d6f0ac2e29913b7084ff3be1f748
Author: Double <fangmingdong0224@gmail.com>
Date:   Mon Feb 6 16:00:45 2017 +0800

    first commit
```


# 基本使用流程

1.如果需要保留本地修改，直接 pull 网络代码(不commit)
git stash -> git pull -> git stash pop -> 解决冲突 -> 继续编辑 ->（如果有warning提示未解决冲突,则git add -u ） -> git commit (需要提交的话) -> git push

2.如果需要直接提交 Commit
git commit -> git pull -> 解决冲突 -> （如果有warning提示未解决冲突,则git add -u ）-> git commit -> git push


# rebase

        A---B---C topic
        /
    D---E---F---G master

                A*--B*--C* topic
                /
    D---E---F---G master

    git checkout topic
    git rebase master
    < make edits to resolve conflicts >
    git add .
    git rebase --continue

注意这里不要执行 commit

如果不小心执行了可以取消 commit：

    git reset HEAD~
