---
title: RecyclerView嵌套在ScrollView中
date: 2017-10-19 09:38:14
tags: RecyclerView
categories: android

---


<!--more-->

```
ScrollView
	RecyclerView
```

# 滑动卡顿问题处理

## 限制了RecyclerView自身的滑动，整个页面滑动仅依靠ScrollView实现

```
recyclerView.setHasFixedSize(true);
recyclerView.setNestedScrollingEnabled(false);
```

## 重写 LayoutManager

```java
LinearLayoutManager linearLayoutManager = new LinearLayoutManager(this) {
    @Override
    public boolean canScrollVertically() {
        return false;
    }
};
```


参考：

- [https://g9yh.github.io/2017/10/10/%E3%80%90Android%E3%80%91%E8%A7%A3%E5%86%B3ScrollView%E5%B5%8C%E5%A5%97RecyclerView%E5%AF%BC%E8%87%B4%E7%9A%84%E6%98%BE%E7%A4%BA%E3%80%81%E9%AB%98%E5%BA%A6%E5%8F%8A%E6%BB%91%E5%8A%A8%E5%8D%A1%E9%A1%BF%E9%97%AE%E9%A2%98/](https://g9yh.github.io/2017/10/10/%E3%80%90Android%E3%80%91%E8%A7%A3%E5%86%B3ScrollView%E5%B5%8C%E5%A5%97RecyclerView%E5%AF%BC%E8%87%B4%E7%9A%84%E6%98%BE%E7%A4%BA%E3%80%81%E9%AB%98%E5%BA%A6%E5%8F%8A%E6%BB%91%E5%8A%A8%E5%8D%A1%E9%A1%BF%E9%97%AE%E9%A2%98/)