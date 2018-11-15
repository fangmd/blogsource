---
title: Android App 安全
date: 2018-11-06 08:00:35 
tags: [安全]
categories: Android

---

# 四大组件安全

>这里提到的组件访问权限主要是指跨应用的进程间通信（IPC，Inter-Process Communication）

组件安全主要是为了防止外部应用恶意访问。


设置权限开放属性

```
android:exported=["true" | "false"]
```

## Application 相关设置

1. debugable 属性设置成 false, 默认就是 false
2. allowBackup: 设置成 false, 

## 使用 LocalBroadcastManager

使用本地广播

# WebView 安全

1. Android4.2 以下有 JS 任意代码执行的漏洞
2. 使用 Https
3. file 协议安全漏洞，如果 app 不需要支持 file 协议可以禁止

```
//若不需支持，则直接禁止 file 协议
setAllowFileAccess(false);
setAllowFileAccessFromFileURLs(false);
setAllowUniversalAccessFromFileURLs(false);
```

3. 密码明文保存漏洞

WebView 默认是开启密码保存功能的，密码会明文保存在 `/data/data/com.package.name/databases/webview.db` 中，应该禁止密码保存。

```
WebSettings.setSavePassword(false)
```

4. 开启安全游览模式

```
<manifest>
    <meta-data android:name="android.webkit.WebView.EnableSafeBrowsing" 
               android:value="true" /> 
    <application> 
     ... 
    </application> 
</manifest>
```

启用安全浏览模式后，WebView 将参考安全浏览的恶意软件和钓鱼网站数据库检查访问的 URL ，在用户打开之前给予危险提示，体验类似以于Chrome浏览器。


# 数据存储安全

1. 密钥类信息不要硬编码，可以写在 JNI 中
2. SharePreferences 不能存放敏感信息，并且在使用的时候设置权限为 MODE_PRIVATE
3. 签名配置信息不要加入到版本管理里面

# 数据传输安全

1. HTTPS 协议
2. Android 网络安全性配置

可以设置应用只信任某个 CA 证书。[网络安全配置](https://developer.android.com/training/articles/security-config?hl=zh-cn#CertificatePinning)

# 其他

1. 日志输出

正式版本要关闭日志

2. 代码混淆和加固
3. 漏洞检查工具



参考：

- [https://zhuanlan.zhihu.com/p/35100057](https://zhuanlan.zhihu.com/p/35100057)