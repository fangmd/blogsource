---
title: AutoLayout Learn
date: 2017-05-18 13:18:12
tags: ios
category: ios

---

# Swift AutoLayout 框架

https://github.com/SnapKit/SnapKit

## Install

### 使用 CocoaPods 安装

CocoaPods is a dependency manager for Cocoa projects.  ios 的包管理器，管理第三方库

```
gem install cocoapods
```

修改 Podfile：(Podfile 文件需要自己创建，位置在项目根目录)

```
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '10.0'
use_frameworks!

target '<Your Target Name>' do
    pod 'SnapKit', '~> 3.0'
end
```

```
pod install
```

<!--more-->

### 使用 Carthage 安装

Carthage is a decentralized dependency manager that builds your dependencies and provides you with binary frameworks.

```
$ brew update
$ brew install carthage

```

修改 Cartfile：

```
github "SnapKit/SnapKit" ~> 3.0
```

Run `carthage update` to build the framework and drag the built SnapKit.framework into your Xcode project.


## Usage

在SnapKit通过 ‘snp_makeConstraints' 给元素增加约束，约束主要分为边距、宽、高、左上右下距离、基准线, 当然增加约束同样也是可以修正的, 修正方式有位移修正 [‘inset、offset’] 和倍率修正 [multipliedBy]

SnapKit 支持的属性与 NSLayoutAttribute 对照表：


```
ViewAttribute       NSLayoutAttribute
view.snap_left  --> NSLayoutAttribute.Left
view.snap_right     NSLayoutAttribute.Right
top
bottom
leading
trailing
width
height
centerX
centerY
baseline

```


约束语法：

- equalTo
- lessThanOrEqualTo
- greaterThanOrEqualTo


### 基本语法

参数简写：

```
// these two constraints are exactly the same
make.left.greaterThanOrEqualTo(label)
make.left.greaterThanOrEqualTo(label.snp.left)
```

给属性设置范围：

```
// width >= 200 && width <= 400
make.width.greaterThanOrEqualTo(200)
make.width.lessThanOrEqualTo(400)
```

约束的参考是 superView:

```
// creates view.left <= view.superview.left + 10
make.left.lessThanOrEqualTo(10)
```

其他约束方式：

```
make.size.equalTo(CGSize(width: 50, height: 100))
make.edges.equalTo(UIEdgeInsets(top: 10, left: 0, bottom: 10, right: 0))
make.left.equalTo(view).offset(UIEdgeInsets(top: 10, left: 0, bottom: 10, right: 0))
```


edges:

```
// make top, left, bottom, right equal view2
make.edges.equalTo(view2);

// make top = superview.top + 5, left = superview.left + 10,
//      bottom = superview.bottom - 15, right = superview.right - 20
make.edges.equalTo(superview).inset(UIEdgeInsets(top: 5, left: 10, bottom: 15, right: 20))
```

size:

```
// make width and height greater than or equal to titleLabel
make.size.greaterThanOrEqualTo(titleLabel)

// make width = superview.width + 100, height = superview.height + 100
make.size.equalTo(superview).offset(100)
```

center:

```
// make centerX and centerY = button1
make.center.equalTo(button1)

// make centerX = superview.centerX + 5, centerY = superview.centerY + 5
make.center.equalTo(superview).offset(5)
```

链式，设置多个属性：

```
// All edges but the top should equal those of the superview
make.left.right.bottom.equalTo(superview)
make.top.equalTo(otherView)
```

### multipliedBy 倍率修正

```
graybox
orangebox

orangebox.snp_makeConstraints{ (make) in
    make.center.equalTo(graybox):
    make.size.equalTo(graybox).multipliedBy(0.5)
}
```

orangebox 大小是 graybox 的一半

### 约束引用

```
var orangeBox = UIView()
var topConstraint:Constraint
var actionBut = UIButton()

func highleveloperator(){
    orangeBox.backgroundColor = UIColor.orangeColor()
    self.view.addSubview(orangeBox)

    orangeBox.snp_makeConstraints{ (make) in
        make.with.hegiht.equalTo(100)
        make.centerX.equalTo(self.view)
        self.topConstraint = make.top.equalTo(self.view).offset(100).constraint
    }

    actionBut.backgroundColor = UIColor.brownColor()
    actionBut.layer.borderWidth = 1
    actionBut.layer.borderColor = UIColor.grayColor().CGColor
    actionBut.layer.cornerRadius = 0.5
    actionBut.setTitle("移除约束", forState: UIControlState.Normal)
    actionBut.setTitleColor(UIColor.whiteColor(), forState: UIControlState.Normal)
    actionBut.addTarget(self, action:#selector(ViewController.btnTouch), forControlEvents: .TOuchUpInside)
    self.view.addSubView(actionBut)

    actionBut.snp_makeConstraints{ (make) in
        make.top.equalTo(orangeBox.snp_bottom).offset(30)
        make.width.equalTo(220)
        make.height.equalTo(35)
        make.centerX.equalTo(self.view)
    }
}


func btnTouch(){
    pring("come in")
    if actionBut.titleLabel?.text == "移除约束"{
        self.topConstraint?.uninstall()
        actionBut.setTitle("更新约束", forState: .Normal)
    }else{
        self.topConstraint?.activate()  // 因为约束之前被卸载了，所以需要先激活约束
        self.topConstraint?.updateOffset(200)
        view.layoutIfNeeded()
        actionBut.setTitle("移除约束", forState: .Normal)
    }
}

```



### 约束动态修改

#### References 获取约束引用

```
var topConstraint: Constraint? = nil

...

// when making constraints
view1.snp.makeConstraints { (make) -> Void in
  self.topConstraint = make.top.equalTo(superview).offset(padding.top).constraint
  make.left.equalTo(superview).offset(padding.left)
}

...
// then later you can call
self.topConstraint.uninstall()

// or if you want to update the constraint
self.topConstraint.updateOffset(5)
```

#### snp.updateConstraints

如果知识更新约束可以使用 `snp.updateConstraints` 而不是 `snp.makeConstraints`

```
// this is Apple's recommended place for adding/updating constraints
// this method can get called multiple times in response to setNeedsUpdateConstraints
// which can be called by UIKit internally or in your code if you need to trigger an update to your constraints
override func updateConstraints() {
    self.growingButton.snp.updateConstraints { (make) -> Void in
        make.center.equalTo(self);
        make.width.equalTo(self.buttonSize.width).priority(250)
        make.height.equalTo(self.buttonSize.height).priority(250)
        make.width.lessThanOrEqualTo(self)
        make.height.lessThanOrEqualTo(self)
    }

   // according to Apple super should be called at end of method
     super.updateConstraints()
}
```

#### snp.remakeConstraints

和 `snp.makeConstraints` 相似，但是会先移除之前设置的约束


### 让一个 view 在另一个 view 的下面

```
aView.snp.makeConstraints({ (make) in
    make.height.width.equalTo(100)
    make.top.equalTo(bView.snp.bottom).offset(20)
    make.centerX.equalTo(bView)
})
```












参考：

- [http://www.jianshu.com/p/7fa1c2ef74a0](http://www.jianshu.com/p/7fa1c2ef74a0)








# Objective-C AutoLayout 框架

https://github.com/SnapKit/Masonry


# AutoLayout 动画

```
        self.mView.updateConstraints()
        UIView.animate(withDuration: 0.5, animations: {
            /// 设置旋转
            self.mView.transform = CGAffineTransform.init(rotationAngle: CGFloat(0))

            self.mView.transform = CGAffineTransform.init(rotationAngle: CGFloat(-Double.pi/2))

            self.mView.snp.makeConstraints({ (make) in
                make.center.equalTo(self.mView.superview!)
                make.width.equalTo(self.mView.superview!.snp.width)
                make.height.equalTo(self.mView.superview!.snp.height)
            })
                
            self.mView.layoutIfNeeded()
            
        }) { (finished) in

        }
```


