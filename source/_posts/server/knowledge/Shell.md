---
title: Shell
date: 2019-09-27 13:18:12
tags: [shell]
category: Server

---


脚本添加执行权限:

```
chmod u+rx filename
```

文件执行工具声明:

```
#!/bin/bash
```

执行脚本:

```
./temp.sh

./temp.sh

.temp.sh

bash ./temp.sh
```

>不同执行方式，执行过程中有差异(影响当前目录位置)


# 管道 符号：|

将第一个程序的输出作为第二个程序的输入

比如

```
ps -ax | grep ss
```

