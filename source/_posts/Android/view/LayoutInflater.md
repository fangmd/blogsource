---
title: LayoutInflater 待续
date: 2016-05-06 12:18:12
tags: LayoutInflater
category: android

---

# LayoutInflater 待续

1. 创建对象的方法
	 public static LayoutInflater from(Context context)

2. 只有一个抽象方法,ContextThemeWrapper类更换主题的时候使用这个克隆原来的LayoutInflater
	 public abstract LayoutInflater cloneInContext(Context newContext);


3. 普通常用方法
	inflate


# MenuInflater



