---
title: Terminal 设置代理
date: 2016-11-07 11:38:14
tags: [Proxy, Terminal]
categories: program

---


	$ export http_proxy='http://localhost:8087'
	$ export https_proxy='http://localhost:8087'


	export http_proxy=http://127.0.0.1:1080/
	export https_proxy=http://127.0.0.1:1080/


PS：别用 ping 对以上代理进行测试，不管用。

以上 需要 查看 SS 是否自带 Http 代理端口。


# 设置 Shadowsocks 代理

![http://droidyue.com/blog/2016/04/04/set-shadowsocks-proxy-for-terminal/index.html](http://droidyue.com/blog/2016/04/04/set-shadowsocks-proxy-for-terminal/index.html)

## mac

```
brew install polipo
```

### 修改配置:

设置每次登陆启动polipo

```
ln -sfv /usr/local/opt/polipo/*.plist ~/Library/LaunchAgents
```

修改文件/usr/local/opt/polipo/homebrew.mxcl.polipo.plist设置parentProxy:
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>Label</key>
    <string>homebrew.mxcl.polipo</string>
    <key>RunAtLoad</key>
    <true/>
    <key>KeepAlive</key>
    <true/>
    <key>ProgramArguments</key>
    <array>
        <string>/usr/local/opt/polipo/bin/polipo</string>
        <string>socksParentProxy=localhost:1080</string>
    </array>
    <!-- Set `ulimit -n 20480`. The default OS X limit is 256, that's
         not enough for Polipo (displays 'too many files open' errors).
         It seems like you have no reason to lower this limit
         (and unlikely will want to raise it). -->
    <key>SoftResourceLimits</key>
    <dict>
      <key>NumberOfFiles</key>
      <integer>20480</integer>
    </dict>
  </dict>
</plist>
```

修改的地方是增加了`<string>socksParentProxy=localhost:1080</string>`

### 启动(Mac):
```
launchctl unload ~/Library/LaunchAgents/homebrew.mxcl.polipo.plist
launchctl load ~/Library/LaunchAgents/homebrew.mxcl.polipo.plis

// or
launchctl unload /usr/local/opt/polipo/homebrew.mxcl.polipo.plist
launchctl load /usr/local/opt/polipo/homebrew.mxcl.polipo.plist
```

### 验证及使用:

```
07:56:24-androidyue/var/log$ curl ip.gs
当前 IP：125.39.112.15 来自：中国天津天津 联通
08:09:23-androidyue/var/log$ http_proxy=http://localhost:8123 curl ip.gs
当前 IP：210.140.193.128 来自：日本日本
```

### 设置别名:

bash中有一个很好的东西，就是别名alias. Linux用户修改~/.bashrc，Mac用户修改~/.bash_profile文件，增加如下设置:

```
alias hp="http_proxy=http://localhost:8123"
```

然后Linux用户执行source ~/.bashrc，Mac用户执行source ~/.bash_profile

使用：
```
20:39:39-androidyue~$ curl ip.gs
当前 IP：125.39.112.14 来自：中国天津天津 联通
20:39:44-androidyue~$ hp curl ip.gs
当前 IP：210.140.193.128 来自：日本日本 
20:39:48-androidyue~$ 
```

### 当前会话全局设置

```
export http_proxy=http://localhost:8123


unset http_proxy
```

如果想要更长久的设置代理，可以将export http_proxy=http://localhost:8123加入.bashrc或者.bash_profile文件

