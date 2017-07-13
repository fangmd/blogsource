---
title: Swift 时间处理
date: 2017-07-13 13:18:12
tags: [ios, Date]
category: ios

---



# Date -> String

```
// 获取当前时间
let currentdate = Date()
// DateFormatter 用于 Date 和 String 之间的转换
let dateformatter = DateFormatter()

// 自定义格式
dateformatter.dateFormat = " YYYY - MM - dd HH:mm:ss"

// date -> string
let customDate = dateformatter.string(from: currentdate)
```

# String -> Date

```
let dateformatter = DateFormatter()

var string1 = " 2016 - 10 - 05"
dateformatter.dateFormat = " YYYY - MM - dd"
var newDate = dateformatter.date(from: string1)
```

# Date 比较

## compare

```
if date1?.compare(date2!) == .orderedAscending{
      print("<")
}
```

```
if date1?.compare(date2!) == .orderedSame{
      print(" = ")
}
```

```
if date1?.compare(date2!) == .orderedDescending{
    print("<")
}
```

## timeInterval

```
if  (date1?.timeIntervalSinceReferenceDate)! -   (date2?.timeIntervalSinceReferenceDate)! >= 0{
  print("大于等于")
}else{
    print("小于")
}
```


# 计算时间的差值

## 2

如果你的第一个Date 比第二个Date小,那么结果就是负数

```
var days = Calendar.current.dateComponents([.year, .month, .day], from: date1, to: date2!)
var y = days.year
var month = days.month
var sss = days.day
```



参考:[http://www.jianshu.com/p/a6275cc54e04](http://www.jianshu.com/p/a6275cc54e04)


