---
title: Android 组件化开发 -- 老项目改善
date: 2017-08-02 12:18:12
tags: 组件化
category: android

---


# 背景


# 路由实现

使用 [ARouter](https://github.com/alibaba/ARouter) 实现跳转

## 0 集成 ARouter



# 创建一个新的工程

## 导入旧代码

在新工程中创建一个新的 module

将老代码中的代码复制到新工程的 module 文件中

### 需要注意的点

1. build.gradle 不要直接替换文件，应该把老项目中的 build.gradle 中特殊的配置一个个复制过来
2. ActivityManifest merge 问题处理：在主工程 AndroidManifest 中添加

    ```
    xmlns:tools="http://schemas.android.com/tools"

    <application
        //...
        tools:replace="android:name, android:icon, android:theme">
    //....
    ```

    根据实际情况 添加： `android:name`, `android:icon` 等选项

3. 处理新项目和老项目资源 id 冲突问题：

    ```
    resourcePrefix "oldapp_"
    ```
4. `Error:Execution failed for task ':app:mergeDebugResources'.`

    主工程 build.gradle 中添加：

    ```
    android{
        //...
        aaptOptions.cruncherEnabled = false
        aaptOptions.useNewCruncher = false
    }    
    ```

5. 不能使用 switch-case 访问资源ID: 使用 Android Studio 快捷修复按钮修复即可

# 关于依赖

1. 主工程依赖所有的 module ？？



