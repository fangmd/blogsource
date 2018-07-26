

# 介绍

[官方地址](https://x5.tencent.com/tbs/index.html)

简单理解：使用 X5内核 的 WebView

主要作用：

1. 兼容好：无系统内核的碎片化问题，更少的兼容性问题；
2. 速度快：相比系统webview的网页打开速度有30+%的提升；

额外功能：

1. 视频播放
2. 文件查看：pdf，office，音乐，压缩文件
3. 下载能力

# 内核

X5内核运行对手机的要求：

>1)手机ROM版本高于或等于2.2版本
>2)手机RAM大于500M，该RAM值通过手机 /proc/meminfo 文件的MemTotal动态获取
>注：如果不满足上述条件，SDK会自动切换到系统WebView，SDK使用者不用关心该切换过程。

关于 X5内核：

1. 大小：23M左右，只在 wifi 条件下会触发下载
2. 手机中所有需要 X5内核 的应用共用一个 内核文件
3. 其他腾讯系应用（微信，qq，qq空间，qq游览器）如果有用到游览器功能都会加载 X5内核，所以大多数手机都已经安装 X5内核

## 内核静态导入方法

[官方文档](https://x5.tencent.com/tbs/technical.html#/detail/sdk/1/8a591c85-337e-4c60-a45a-cf0839819f63)

出现的问题：

1. 静态导入的 jar 的版本低（3.5, 官方最新3.6）
2. 包体积会增大：包体积改变：3.5M -> 29.9M （可以根据具体应用精简一部分，比如不需要游戏性能的去除游戏相关 so 包)
3. 静态导入模式下，部分机型 可能出现无法共享手机中已有的内核（也就是 无法使用 X5WebView 内核）
4. 首次启动还是有可能无法启动 X5WebView 内核

>静态导入模式只适合特定应用场景，比如不允许安装腾讯系应用的定制机


## 64位手机加载 X5内核

[https://x5.tencent.com/tbs/technical.html#/detail/sdk/1/34cf1488-7dc2-41ca-a77f-0014112bcab7](https://x5.tencent.com/tbs/technical.html#/detail/sdk/1/34cf1488-7dc2-41ca-a77f-0014112bcab7)

项目中加入 armeabi so 文件 `liblbs.so`

```
        ndk {
            abiFilters "armeabi"
        }
```

# 混淆

sdk 中的 jar 包中的代码已经混淆

# 坑

1. 应用第一次开启有一定几率不能够启动 X5内核
2. 手机重启后应用有一定几率不能启动 X5内核




# 工具

TbsSuiteNew.apk：检测手机中是否已经有 X5内核。

# 使用

## 基本配置，内核加载

自定义 Application 中：

```
    private void initX5WebView() {
        QbSdk.PreInitCallback cb = new QbSdk.PreInitCallback() {

            @Override
            public void onViewInitFinished(boolean arg0) {
                LoggerUtils.d("WebView", "X5WebView onViewInitFinished init finished:" + arg0);
            }

            @Override
            public void onCoreInitFinished() {

            }
        };
        QbSdk.initX5Environment(getApplicationContext(), cb);
    }
```


>其他使用方式和WebView无异，具体见官方文档

