---
title: ios-Tips
date: 2017-05-18 13:18:12
tags: ios
category: ios

---




# 获取 AppDelegate

```
let appDelegate = UIApplication.shared.delegate as! AppDelegate
```

<!--more-->

# 创建控价的时候，建议将 style 的设置在初始化的时候马上完成

例子：

```
let mLabelLearnCnt: UILabel = {
	let label = UILabel()
    label.textColor = UIColor.white
        label.font = UIFont(name: (label.font?.fontName)!, size: 40)
        return label
}()
```

# json 使用 ObjectMapper 的时候 "123"	 不能使用 Int 类型的属性接收，Gson 是可以的

```
[
  {
    "CourseCredits": "105",
    "finishCourse": 1,
  }
]
```

解决：

```
status <- (map["status"], TransformOf<Int, String>(fromJSON: { Int($0!) }, toJSON: { $0.map String($0) } }))
```

# 写 cell 的时候 contentView.addSubview() 和 addSubview() 

[https://stackoverflow.com/questions/29342682/what-difference-between-costuming-uitableviewcell-in-self-addsubview-and-self](https://stackoverflow.com/questions/29342682/what-difference-between-costuming-uitableviewcell-in-self-addsubview-and-self)

建议使用 `contentView.addSubview()`

