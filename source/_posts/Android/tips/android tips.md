---
title: android tips 
date: 2016-04-02 12:18:12
tags: tips
category: android

---

- Use Vectors instead of PNG

    If you do have to use png, compress them. Take a look at [TinyPNG](https://tinypng.com/).

- Use shrinkResources
    
    shrinkResources true

- Define a variable at build time In your build.gradle

    ```
    android{
        defaultConfig {
            ...
            buildConfigField "String", "SERVER_ENDPOINT", '"http://www.myendpoint.com"'
            buildConfigField "int", "FOO", "52"
            buildConfigField "boolean", "LOG", "false"
            ...
        }
    }




## android 自带的工具类

- android.text.TextUtils
- android.webkit.URLUtil
- android.text.format.DateUtils
- tools schemas
- UncaughtExceptionHandler:捕获全局异常

<!--more-->

## 编程tips
- 如何在无关`Activity`的场景比如`Service`、`Receiver`调出`AlertDialog`
- ViewHolder声明为`static`

## 其他

- arraycopy(src, srcPos, dest, destPos, length)方法，瞬间摆脱Low到家的for循环来拷贝数组
- 在Binder间传送序列化对象时，Bundle中不要用Parceble，要用Json序列化后的String！不然会有莫名其妙的bug
- 数组复制，请使用System.arrayCopy或Arrays.copyOf 实现，且在JAVA中后者基于前者实现。
- Android DataBinding,现在还没有完全的双向绑定，而且很多我们常用的属性的getter和setter也没有，需要我们手动wrapper，但我相信这些在未来都会有更便捷的方案的
- 但凡和线程池/线程/同步/锁等有关的实践问题，尽量不要用Android的解决方案，如AsyncTask，而要用Java中已经历经考验的实践方案 如FutureTask+ThreadPoolExecuters。
- 进行类的单例化时，用这个就OK:

		public class myclass{
  		//some property..
  		// some mothod
  			public static class HOLDER {
				public myclass instance = myclass();
			}  
			public static myclass getInstance(){
				return HOLDER.instance;
			}
		}

参考：

- [https://github.com/nisrulz/android-tips-tricks](https://github.com/nisrulz/android-tips-tricks)