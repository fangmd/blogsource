---
title: NDK Hello
date: 2016-04-03 13:18:12
tags: [NDK]
category: android

---

# 创建一个Jni类

```
public class JniTest {
    public static native getStringFromNative();
}
```

# Make（构建）Module app生成字节码（*.class）文件

`build->make module'app'`

目的是生成 `.class` 文件，文件地址在：`NdkBuilderDemo/app/build/intermediates/classes/debug/com/adouble/ndkbuilderdemo/JniTest.class`

# 生成头文件（*.h）

1. 首先打开工具栏的Terminal工具
2. `cd app/src/main`
3. `javah -d jni -classpath ../../build/intermediates/classes/debug com.adouble.ndkbuilderdemo.JniTest`


- `javah` 执行javah命令生成头文件（*.h）
- `-d jni` 在当前目录创建一个文件，文件名称是jni
- `-classpath xxx/debug` 指定要生成头文件（.h）的字节码文件路径（class）

# 创建JniTest.c文件

方法JNICALL Java_com_adouble_ndkbuilderdemo_JniTest_getStringFromNative，这相当于是java的抽象方法，它只声明但是没有任何实现，一般情况下需要创建一个c或cpp文件去实现相应的方法。

1. 首先创建一个文件JniTest.c
2. 编辑JniTest.c，导入头文件com_sandwich_jelly_myapplication_JniTest.h
3. 实现头文件的抽象方法

```c
#include com_adouble_ndkbuilderdemo_JniTest.h


JNIEXPORT jstring JNICALL Java_com_adouble_ndkbuilderdemo_JniTest_getStringFromNative(JNIEnv *e, jobject obj){
	return (**e).NewStringUTF(e,"Hello from JniTest Function");
}
```

# Jni类的配置及引用
```
    defaultConfig {
...
        ndk {
            moduleName "NdkJniDemo"          //生成的so名字
            ldLibs "log"//实现__android_log_print
            abiFilters "armeabi", "armeabi-v7a", "x86" //输出指定三种abi体系结构下的so库，目前可有可无。
        }

    }
```

修改原文件
```java
public class JniTest {

    static {
        System.loadLibrary("NdkJniDemo");//之前在build.gradle里面设置的so名字，必须一致
    }

    public static native String getStringFromNative();
}
```


# 项目中引用so

以上项目编译完后，可以在app/build/intermediates/ndk/debug/lib下面找到生成的so



参考：

- [http://www.jianshu.com/p/3e9f3055d7a6](http://www.jianshu.com/p/3e9f3055d7a6)
- [http://xyzlf.github.io/2016/07/17/android-jni.html](http://xyzlf.github.io/2016/07/17/android-jni.html)


