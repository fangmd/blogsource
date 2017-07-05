---
title: 日志管理
date: 2017-05-18 13:18:12
tags: ios
category: ios

---




# swift 实现工具类

```
class LoggerUtils{
    class func d(message: String){
        NSLog(message)
    }

    static func e(message: String){
        NSLog(message)
    }
}
```

class: 修饰表示可以直接静态方法，可以使用 类 直接调用，可以被子类重写

static：表示 class final, 静态方法并且不能被重写

建议使用 `class` 修饰

<!--more-->

# 在 realse 版本中取消 log

利用 `Preprocessor Macros` 值实现，在 `Build Settings` 中设置，默认已经有值： Debug 版本中参数 `DEBUG=1` 

LoggerUtils.swift

```
#if DEBUG
    let a = true
#else
    let a = false
#endif

class LoggerUtils{
```

# LoggerUtils.swift

```
#if DEBUG
    let a = true
#else
    let a = false
#endif

class LoggerUtils{
    
    static func d(message:String){
        //        NSLog(message)
        if(a){
            DDLogVerbose(message);
        }
    }
    
    static func e(message: String){
        //        Log(.Network, .Error, String)
    }
    
    static func i(message: String){
        
    }
    
    static func w(message: String){
        
    }

}
```

# NSLogger 

[Github 地址](https://github.com/fpillet/NSLogger)

## install

```
pod "NSLogger/Swift"
```

## usage

