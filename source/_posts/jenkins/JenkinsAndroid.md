---
title: Jenkins Android使用记录
date: 2017-12-15 12:18:12
tags: [Jenkins]
category: Jenkins

---


# 安装插件

- Gradle plugin：用于构建
- Android Emulator Plugin
- Git Parameter Plug-In
- Android Lint Plugin
- build-name-setter: 用于修改 构建名

`系统管理` -> `插件管理`



# 创建任务

`创建一个新任务，选择构建一个自由风格的软件项目`

The specified build file '/Users/Shared/Jenkins/Home/workspace/android-first/AndroidDemo/app/build.gradle' does not exist.


## General


### 设置参数

勾选 `参数化构建过程`

在 `构建`-》`高级` 中勾选 `Pass all job parameters as Project properties`: 可以让 参数 注入到 gradle 中和在 `gradle.properties` 中设置参数一个效果


#### 设置 release／debug：

![build-type](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/Jenkins/build-type.png)

构建命令可以改成 `clean assemble${BUILD_TYPE}`

#### 动态设置 keystore 地址

![IS-Jenkins](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/Jenkins/IS-Jenkins.png)

`gradle.properties`:

```
IS_JENKINS=true
```


`app/build.gradle`:

```
signingConfigs {        
        signingConfig {                
            if("true".equals(IS_JENKINS)){                
                storeFile file("服务器上KeyStore的路径")            
            }else {                
                storeFile file(STORE_FILE_PATH)            
            }            
            keyAlias KEY_ALIAS            
            keyPassword KEY_PASSWORD            
            storePassword STORE_FILE_PASSWORD        
        }    
}
```


#### Jenkins 使用Git Parameter打包

![git-parameter](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/Jenkins/git-parameter.png)

## 构建环境

### 设置构建命名：

需要安装插件 `build-name-setter`


- `${GIT_BRANCH}`: 获取 git 分支状态 例子：`origin/maste`

## 构建

>设置 构建命令

勾选 `Use Gradle Wrapper`

- Tasks: 表示要执行的命令, 设置成 `clean assumblerelease`


## 构建后操作

>归档 apk 文件

![archive-the-artifacts](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/Jenkins/archive-the-artifacts.png)




# 问题1

```
* What went wrong:
A problem occurred configuring project ':app'.
> SDK location not found. Define location with sdk.dir in the local.properties file or with an ANDROID_HOME environment variable.
```

处理方法：

1. Go to Jenkins > Manage Jenkins > Configure System
2. Check "Environment variables"
3. add name: ANDROID_HOME, value -> your android sdk dir
4. click "add"
5. SCROLL DOWN CLICK SAVE

例子：ANDROID_HOME, `/Users/nerc/Library/Android/sdk`


# 问题2 

```
The SDK directory '/Users/nerc/Library/Android/sdk' does not exist.
```

如果路径是对的，那问题就出在 没有权限 

开启权限：

```
chmod -R 777 /Users/nerc/Library/Android

// 或者
sudo chmod -R 755 /Users/nerc/Library/Android
```

chmod -R 755  /Users/nerc/Documents/AndroidProject/AndroidKeyStores/test.Keystore

如果还不行：(下面语句过程漫长，可以中途强制停止掉（ctrl+c），不需要更改所有文件的权限)

```
chmod -R 777 /Users/nerc/Library/
```

# 问题3

```
file not found
```

权限问题，使用 `brew` 安装 jenkins 可以解决。


# github hook log polling has not run yet


