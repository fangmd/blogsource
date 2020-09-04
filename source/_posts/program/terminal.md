---
title: 终端使用效率相关
date: 2020-09-04 13:18:12
tags: [Terminal]
category: program

---

# ssh 连接服务器快捷链接

- [https://scotch.io/tutorials/how-to-create-an-ssh-shortcut](https://scotch.io/tutorials/how-to-create-an-ssh-shortcut)

```
cd ~/.ssh
vim config

Host scotch
    HostName scotch.io
    User nick

Host example3
    HostName 64.233.160.0
    User userxyz123
    Port 56000


ssh scotch
ssh example3
```

