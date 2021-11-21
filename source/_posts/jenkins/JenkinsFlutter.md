---
title: Jenkins Flutter 实现自动化打包
date: 2020-07-07 12:18:12
tags: [Jenkins]
category: Jenkins

---

准备一台 mac

# 2. 安装需要的插件

1. Keychains and Provisioning Profiles Management（iOS证书管理）
2. Xcode integration（iOS专用）
3. Gradle plugin（Android专用）



keychains 和 provision 在系统中的路径。

```
keychain: /Users/zcating/Library/Keychains
provision: /Users/zcating/Library/MobileDevice/Provisioning Profiles
```


## Android 打包

1. 在项目中创建打包 sh:

`buildAndroid.sh`

```
#!/bin/bash

source ~/.zshrc

flutter build apk --release
```

2. 添加 sh 执行权限: 

```
chmod +x buildAndroid.sh
```

3. jenkins pipeline 脚本:

使用 macos 打包机打包，flutter 相关环境在打包机上提前配置好。

```
pipeline {
    agent { label 'mac16' }
    tools {nodejs "yarn-node16"}
    
    stages {
        stage('Pre(git)') {
            steps{
                script{
                    echo "git pull"
                    if(fileExists("sh_travel")) {
                        sh "cd sh_travel && git pull"
                    }else{
                        sh "git clone git@codeup.aliyun.com:6170ccd210204867ecfd4f8e/app/sh_travel.git"
                    }
                    
                    if(fileExists("flutter_lib")) {
                        sh "cd sh_travel && git pull"
                    }else{
                        sh "git clone git@codeup.aliyun.com:6170ccd210204867ecfd4f8e/app/flutter_lib.git"
                    }
                }                
            }
        }

        
        stage('build') {
            steps{
                script{
                    echo "start build"
                    sh """
                    cd sh_travel && ./buildAndroid.sh
                    """
                }
            }
        }
        
        stage('Archive') {
            steps{
                archiveArtifacts artifacts: 'sh_travel/build/app/outputs/apk/release/*.apk'
            }
        }

    }
 
}
```