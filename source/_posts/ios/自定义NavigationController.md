---
title: 自定义 NavigationController
date: 2017-05-19 13:18:12
tags: ios
category: ios

---


# Install


# 设置成透明

```
    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        self.navigationController?.navigationBar.setBackgroundImage(UIImage(), for: UIBarMetrics.default)
        self.navigationController?.navigationBar.shadowImage = UIImage()
    }
    
    override func viewWillDisappear(_ animated: Bool) {
        super.viewWillDisappear(animated)
        self.navigationController?.navigationBar.shadowImage = nil
        self.navigationController?.navigationBar.setBackgroundImage(nil, for: UIBarMetrics.default)
    }
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