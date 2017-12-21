---
title: Android 组件化开发
date: 2017-08-02 12:18:12
tags: [Android 架构]
category: android

---

# 模块独立的实现

在 Module 的 build.gradle 中加入下面代码：

```
if(isBuildModule.toBoolean()){
    apply plugin: 'com.android.application'
}else{
    apply plugin: 'com.android.library'
}
```

isBuildModule 在项目根目录的 gradle.properties 中定义:

```
isBuildModule=false
```

同样 Manifest.xml 也需要有两套：

```
sourceSets {
   main {
       if (isBuildModule.toBoolean()) {
           manifest.srcFile 'src/main/debug/AndroidManifest.xml'
       } else {
           manifest.srcFile 'src/main/release/AndroidManifest.xml'
       }
   }
}
```

debug 模式下的 AndroidManifest.xml :

```
<application
   ...
   >
   <activity
       android:name="com.baronzhang.android.newhouse.NewHouseMainActivity"
       android:label="@string/new_house_label_home_page">
       <intent-filter>
           <action android:name="android.intent.action.MAIN" />
           <category android:name="android.intent.category.LAUNCHER" />
       </intent-filter>
   </activity>
</application>
```


realease 模式下的 AndroidManifest.xml :

```
<application
   ...
   >
   <activity
       android:name="com.baronzhang.android.newhouse.NewHouseMainActivity"
       android:label="@string/new_house_label_home_page">
       <intent-filter>
           <category android:name="android.intent.category.DEFAULT" />
           <category android:name="android.intent.category.BROWSABLE" />
           <action android:name="android.intent.action.VIEW" />
           <data android:host="com.baronzhang.android.newhouse"
               android:scheme="router" />
       </intent-filter>
   </activity>
</application>
```


模块设计规则：

1. Business Module Layer: 业务模块之间不能相互依赖，它们之间的跳转通过 路由框架 Router 实现


# 资源名冲突处理

对于多个 Bussines Module 中资源名冲突的问题，可以通过在 build.gradle 定义前缀的方式解决：

```
defaultConfig {
   ...
   resourcePrefix "new_house_"
   ...
}
```

# 依赖

```
    if (isModuleIsApplication.toBoolean()) {
        implementation project(':baselibrary')
    } else {
        implementation project(':touchhandler')
    }
```

# 减少编译项 

`settings.gradle`: 将不需要使用到的模块排除

```
include ':touchhandler', ':baselibrary', ':youtubeview'

//根据是否模块开发，是否引入app 模块
if (!isModuleIsApplication.toBoolean()) {
    include ':app'
}
```

# 路由实现

使用 [ARouter](https://github.com/alibaba/ARouter) 实现跳转

## 0 集成 ARouter


## 坑1

在 Module 之前跳转的时候需要让 moduleA 依赖 moduleB



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


参考：[http://baronzhang.com/blog/Framework/Android-%E6%A8%A1%E5%9D%97%E5%8C%96%E6%8E%A2%E7%B4%A2%E4%B8%8E%E5%AE%9E%E8%B7%B5/](http://baronzhang.com/blog/Framework/Android-%E6%A8%A1%E5%9D%97%E5%8C%96%E6%8E%A2%E7%B4%A2%E4%B8%8E%E5%AE%9E%E8%B7%B5/)

