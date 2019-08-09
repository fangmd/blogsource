---
title: LayoutInflater 源码
date: 2019-01-18 12:18:12
tags: 源码
category: android

---

LayoutInflater 的作用：根据 xml 布局文件构建 View。

## LayoutInflater 对象获取

```java
    /**
     * Obtains the LayoutInflater from the given context.
     */
    public static LayoutInflater from(Context context) {
        LayoutInflater LayoutInflater =
                (LayoutInflater) context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
        if (LayoutInflater == null) {
            throw new AssertionError("LayoutInflater not found.");
        }
        return LayoutInflater;
    }
```

1. `LayoutInflater` 是抽象类
2. 通过 `context` 获取服务的方式获取 `LayoutInflater` 对象

## LayoutInflater如何构建View树

1. 解析 xml: 通过 `R.layout.xxx` 获取 XmlPullParser 对象， `res.getLayout(resource)`
2. Tag -> View: 反射方式创建 View `createViewFromTag(root, name, inflaterContext, attrs);`




参考：[https://juejin.im/post/5a5c84d2f265da3e54690d3b](https://juejin.im/post/5a5c84d2f265da3e54690d3b)