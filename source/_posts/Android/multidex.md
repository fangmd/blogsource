---
title: Multidex
date: 2016-01-17 09:38:14
tags: [Multidex]
categories: android

---


![官方文档 Configure Apps with Over 64K Methods](https://developer.android.com/studio/build/multidex.html)

## 0

'src/build.gradle':

```
    ...
    defaultConfig{
      ...
        // Enabling multidex support.
        multiDexEnabled true
    }


    dependencis{
      ...
      compile 'com.android.support:multidex:1.0.1'
      
    }
    
```

## 1

App:

```java
public class App extends Application {

  //....

  @Override
  protected void attachBaseContext(Context base) {
     super.attachBaseContext(base);
     MultiDex.install(this);
  }

}
```