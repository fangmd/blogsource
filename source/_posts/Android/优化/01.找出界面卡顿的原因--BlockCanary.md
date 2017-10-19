---
title: 找出Android界面卡顿的原因－－BlockCanary
date: 2016-08-28 13:18:12
tags: [Android, 优化]
category: Android

---

## 使用

1. 配置`build.gradle`

        compile 'com.github.moduth:blockcanary-android:1.2.1'
        // 仅在debug包启用BlockCanary进行卡顿监控和提示的话，可以这么用
        debugCompile 'com.github.moduth:blockcanary-android:1.2.1'
        releaseCompile 'com.github.moduth:blockcanary-no-op:1.2.1'

2. 新建一个类,继承自BlockCanaryContext，实现自己的监控上下文，代码示意如下

        public class AppBlockCanaryContext extends BlockCanaryContext {
            //设置卡顿判断的阙值
            public int getConfigBlockThreshold() {
                return 500;
            }

            //是否需要显示卡顿的信息
            public boolean isNeedDisplay() {
                return BuildConfig.DEBUG;
            ｝

            //设置log保存在sd卡的目录位置
            public String getLogPath() {
                return "/blockcanary/performance";
            }

         }
3. 在自定义Application中初始化

        BlockCanary.install(this, new AppBlockCanaryContext());
