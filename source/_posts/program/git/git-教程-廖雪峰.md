---

title: git 教程 廖雪峰

date: 2016-03-30 09:13:12

tags: [git]

category: Android


---


## git 教程 廖雪峰

参考：[http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)

### 创建版本库
>git 只能跟踪文本文件的改动；
>windows下不要使用自带的文本编辑器编辑文件，它在文本开始加入了十六进制的字符；
>使用UTF-8

	mkdir learngit
	cd learngit
	pwd

	git init
在learngit文件夹下创建`README.md`,把他加入到仓库中：

	git add README.md
	git commit -m"wrote a readme file"

### 时光机穿梭
对`README.md`文件做一些修改，后执行下面的命令

	git status
	git diff README.md
	
	git add README.md
	git status
	git commit -m"add something"
	git status

#### 版本回退
再次修改`README.md`文件，并提交
	
	git add README.md
	git commit -m"append GPL"
	git log
	git log --pretty=oneline
	git reset --hard HEAD^
	git reset --hard [版本号]
	git reflog

#### 工作区和暂存区

- 工作区 Working Directory:`learngit`文件目录
- 版本库 Repository:`.git`

#### 管理修改
>git 跟踪并管理的是修改，而非文件
>git 每次修改，如果不`add`到暂存区，就不会加入到`commit`中

- 当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令git checkout -- file
- 当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令git reset HEAD file，就回到了场景1，第二步按场景1操作。
- 已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。


#### 删除文件

	git add test.md
	git commit -m"add test.md"
	rm test.md
	git status
	git rm test.md

恢复错删的文件

	git checkout --test.md

### 远程仓库
建立rsa秘钥：

	ssh-keygen -t rsa -C "email...."

#### 添加远程仓库
首先在github上建立一个空的仓库，然后：

	git remote add origin git@github.com:fangmd/learngit.git

远程仓库的名字是：origin

	git push -u origin master

`-u`表示推送并关联远程仓库的`master`分支，之后提交只需要：

	git push origin master

#### 从远程库克隆

	git clone git@github.com:fangmd/text.git

### 分支管理

#### 创建与合并分支
	
	git checkout -b dev

`-b`:表示创建并切换，相当于下面两条命令：

	git branch dev
	git checkout dev

	git branch
在`dev`分支中对文件进行更改并提交后：

	git checkout master
	git merge dev
	git branch -d dev
	git branch
#### 解决冲突

	git checkout -b feature1

修改文件并提交后：
	
	git checkout master
对同一个文件进行修改，并提交后：

	git merge feature1
	git status
解决冲突后并提交后：

	git log
	git branch -d feature1
	git log --graph

#### 分支管理策略

- `master`分支用作稳定发布版本的分支
- `dev`分支用作平时开发的主分支，不稳定
- `custom`：分支，每个人自己开发的时候从`dev`中拉出分支开发，并合并到`dev`中

#### Bug分支

当一个功能开发到一半还不能提交，但是要先做一个临时任务：bug修复：

	git stash // 把工作现场储存起来
	git status // 看到干净的

开始修复bug：

	git checkout master
	git checkout -b issue-101

修复完成后提交，并切换到`master`，合并，删除bug分支

	git checkout master
	git merge --no-ff -m"merge bug fix 101" issue-101
	git branch -d issue-101

继续之前的工作：

	git checkout dev
	git status
	git stash list

恢复现场：

	git stash apply // 恢复不删除stash内容

	git stash pop // 恢复删除stash内容

多次stash后：恢复

	git stash apply stash@{0}

#### Feature 分支
每次开发一个功能的时候，建立一个feature分支，完成后合并，删除；

	git checkout -b feature-vulcan
	git add .
	git commit -m"add feature vulcan"
	git checkout dev
	git branch -d feature-vulcan // git 提示未合并
	git branch -D feature-vulcan // 强制删除

#### 多人协作

	git remote
	git remote -v // 看到 fetch 和 push（如果过有push权限的化）

推送分支：

	git push origin master
	git push origin dev // 将本地的分支推送到远程，公开

- master 分支为主分支，需要时刻和远程同步
- dev：开发分支，需要同步
- bug：不用
- feature：取决于是否多人开发

抓取分支：

	git checkout -b dev origin/dev

开发完成后推送：

	git push origin dev

多人处理同一条分支的时候，需要先pull：

	git pull
	git branch --set-upstream dev origin/dev // 可能需要指定本地分支到远程的链接
	git pull

可能有冲突，解决完成后提交，推送：

	git push origin dev


