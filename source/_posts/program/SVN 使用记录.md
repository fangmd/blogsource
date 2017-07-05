---
title: SVN 使用记录
date: 2016-11-18 13:18:12
tags: [svn]
category: Android

---

# SVN  生命周期

## 创建版本库

版本库相当于一个集中的空间

## 检出

Checkout：操作从版本库创建一个工作副本。工作副本是正常开发的地方，修改后提交到版本库中。


## 更新

update：操作从版本库中下载代码，将工作副本与版本库进行同步

## 执行变更

commit：提交，将工作副本中的代码提交到版本库中。

rename：操作，更改文件／目录的名字

## 复查变化

update，checkout，操作后工作副本就和版本库中的数据相同了。

status：操作，列出工作副本中所进行的变动，变动列表

diff：操作显示变动详细信息

## 修复错误

当工作副本中做了许多修改，但是不想要这个修改的时候，

revert：操作重置对工作副本的修改。可以重置部分修改

## 解决冲突

合并的时候会发生冲突

merge：操作可以处理部分冲突，不能处理的冲突需要用户自己处理

resolver：操作可以找出需要用户解决的冲突

## 提交更改

commit：操作将工作副本的内容发送到版本库中

# SVN 启动模式

默认端口：`3690`


方式一：-r直接指定到版本库(称之为单库svnserve方式):
```
svnserve -d -r /opt/svn/runoob
```

方式二：指定到版本库的上级目录(称之为多库svnserve方式):
```
svnserve -d -r /opt/svn
```

多库启动 svn 的时候，当 svn 目录下有两个版本库：`runoob`,`runoob01`

runoob01 svn 地址：`svn://192.168.0.1/runoob01`

# Mac 下安装和使用

## install svn server 服务器端

```
$ brew options subversion
$ brew install (OPTIONS) subversion
```

## 创建版本库

```
svnadmin create /opt/svn/runoob01
```

版本库配置文件位置:`/opt/svn/runoob01/conf`:

1. svnserve.conf: svn 服务配置文件
    
    去掉下面的注释
    ```
    # anon-access = read
    # auth-access = write

    # password-db = passwd

    # authz-db = authz
    ```
    anon-access = read代表匿名访问的时候是只读的，若改为anon-access = none代表禁止匿名访问，需要帐号密码才能访问
2. passwd: 用户名口令
    
    ```
    #<用户名> = <口令>

    [users]
    fang = 123456
    fang02 = 123456
    ```

3. authz: 权限配置文件 

    ```
    # 定义组
    [groups]
    @g_admin = fang,fang02

    # [<版本库名>:<路径>]
    [runoob01:/]
    @g_admin=rw
    ```


## mac 下 svn 的一些基本操作

### 从本地导入代码到服务器(第一次初始化导入)

```
svn import /Users/apple/webSite svn://localhost/mycode/webSite --username=mj --password=123 -m "初始化导入"
```

这种方法将本地文件上传到 svn 版本库中，本地文件不会和 svn 关联（之后不能使用 commit）

### 从服务器端下载代码到客户端本地

```
svn checkout svn://localhost/mycode --username=mj --password=123 /Users/apple/svnwebSite
```

这种方法会将本地代码和版本库代码关联（之后修改后可以直接 commit）

### 提交更改过的代码到服务器

```
svn commit -m "修改了webSite文件"
```


### 更新服务器端的代码到客户端

```
svn update
```



# svn add .

[http://tiankonguse.com/record/record.php?id=690](http://tiankonguse.com/record/record.php?id=690)

	svn: warning: W150002: '目录路径' is already under version control
	svn: E200009: Could not add all targets because some targets are already versioned
	svn: E200009: Illegal target for the requested operation

解决方案：

	svn add 目录名 --force

# svn commit -m'.....'


# 删除文件夹后点commit提交，但是报错，报错内容如下：
提示 "svn: Commit failed (details follow): svn: `'/***/xxx.c' is scheduled for addition, but is missing "`

原因：之前用SVN提交过的文件，被标记为"add"状态，等待被加入到仓库。若此时你把这个文件删除了，SVN提交的时候还是会尝试提交这个文件，虽然它的状态已经是 "missing"了。

解决：

    svn revert xxx.c --depth infinity


参考：

[https://cnbin.github.io/blog/2015/06/13/mac-da-jian-svn-fu-wu-qi-huan-jing/](https://cnbin.github.io/blog/2015/06/13/mac-da-jian-svn-fu-wu-qi-huan-jing/)
