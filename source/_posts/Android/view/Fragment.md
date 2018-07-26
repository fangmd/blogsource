---
title: Fragment
date: 2016-04-02 21:38:14
tags: Fragment
categories: android

---


# Fragment 

<!--more-->
## 生命周期
- `onAttach()`
- `onCreate()`
- `onCreateView()`
- `onActivityCreated()`
- `onStart()`
- `onResume()`
- `onPause()`
- `onStop()`
- `onDestoryView()`
- `onDestory()`
- `onDetach()`

## 分类
- DialogFragment
- ListFragment
- PreferenceFragment


### ListFragment

>在onCreateView()中会返回View对象中含有了ListView,不需要自己定义了（一般不用重写）

## Activity 和 Fragment 互相操作控件

1. activity可以直接通过`findViewById()`方法找到fragment中的控件，因为fragment中的控件也显示在activity布局中
2. fragment获取activity中的控件对象，`getAcitivyt().findViewById()`



## fragment 回退栈 (不推荐使用，容易出 bug)
>实际上是事务的回退栈

### 自定义一个回退栈
1. 定义一个LinkedList存储Fragment
2. 添加新的Fragment的时候，判断栈中是否有fragment，如果有：先隐藏fragment然后添加；如果没有，直接添加
3. 删除，回退fragment的时候，绑定栈中是否有fragment，如果有：显示栈顶的fragment，

### 事务回退栈

    transaction.addToBackStack(null);//括号中的参数是栈的名称

## fragment中控件的点击事件

fragment中控件不能通过`onClick`属性来设置点击事件，实现方法：

	((TextView) getActivity().findViewById(R.id.fragment_set_set)).setOnClickListener(this);


## 懒加载


