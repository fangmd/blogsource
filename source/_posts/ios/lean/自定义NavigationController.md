---
title: 自定义 NavigationController
date: 2017-05-19 13:18:12
tags: ios
category: ios

---

# NavigationController 上添加 button 的时候

>NavigationController 在界面切换的时候，里面的控件位置偏移

不要使用 Snapkit



# 改变 NavigationController 背景透明度的正确方式

```
mBarImageView = self.navigationController!.navigationBar.subviews[0]

改变背景透明度：mBarImageView?.alpha = 1



```


<!--more-->

## 说明

设置底下的线透明：

```
self.navigationController?.navigationBar.shadowImage = UIImage()
```

设置一张透明背景的图片：

```
self.navigationController?.navigationBar.setBackgroundImage(UIImage(), for: UIBarMetrics.default)
```

获取到背景图片：

```
barImageView = self.navigationController.navigationBar.subviews.firstObject
```

通过设置背景图的透明度实现渐变：

```
- (void)scrollViewDidScroll:(UIScrollView *)scrollView {
    CGFloat minAlphaOffset = - 64;
    CGFloat maxAlphaOffset = 200;
    CGFloat offset = scrollView.contentOffset.y;
    CGFloat alpha = (offset - minAlphaOffset) / (maxAlphaOffset - minAlphaOffset);
    _barImageView.alpha = alpha;
}
```