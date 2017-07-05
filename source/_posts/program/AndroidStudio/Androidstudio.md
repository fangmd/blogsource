---
title: android studio 
date: 2016-04-05 11:13:12
tags: AndroidStudio
category: program

---


# android studio 

## 插件

### SelectorChapek

## 记报错

### 0 

     retrofit 使用的使用，报错：ArrayIndexOutOfBoundsException
     
解决办法:android studio 打开设置-->build,Execution,Deployment-->instant run-->去掉所有的勾

![](http://7xr8nu.com1.z0.glb.clouddn.com/androidstudioinstantrun.png)

### 1 注: 某些输入文件使用或覆盖了已过时的 API。

    allprojects {
        gradle.projectsEvaluated {
            tasks.withType(JavaCompile) {
                options.compilerArgs << "-Xlint:unchecked" << "-Xlint:deprecation"
            }
        }
    }

gradle中加入上面的设置，可以查看详细内容。

### 2 android studio 引入 org.apache.http包

	android {
	    useLibrary 'org.apache.http.legacy'
	}

## 自定义快捷键

## post fix
![](http://i.imgur.com/W23MP5s.png)

## 定义类的注释
![](http://i.imgur.com/8hWMTxQ.png)



