---
title: ListView 复习总结
date: 2016-05-06 11:38:14
tags: ListView
categories: android

---

# ListView复习总结

## 0

1. LayoutInflate的`inflate`方法中第二个参数是`ViewGroup`，代表把创建的`View`通过`ViewGroup`的`addView(view)`方法加入到这个`ViewGroup`中；
2. 但是`ListView`没有`addView`方法，所以在`ListView`中使用这个的时候第二个参数必须为`null`
3. convertView接收的是可复用的布局对象，当没有可复用的布局对象时，接收的是null

## 1 ListView优化
1. 内存占用的优化：通过`convertView`达到布局复用的效果
2. 优化运行时间：通过`ViewHolder`
	1. 用`ViewHolder`保存`convertView`中的控件对象
	2. 通过`setTag`的方式，讲`ViewHolder`和`convertView`绑定在一块
	3. 减少了`findViewById`的次数

## 2 
1. `android:divider`
2. `setEmptyView(View)`当`listView`中没有数据的时候显示这个`View`
3. `setHeaderView`
4. `setFooterView`
