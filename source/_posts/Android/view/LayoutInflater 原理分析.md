---
title: LayoutInflater 原理分析
date: 2016-04-02 21:38:14
tags: [view,LayoutInflater]
categories: android

---

# LayoutInflater 原理分析

## 0 LayoutInflater 对象创建的两种写法

	LayoutInflater layoutInflater = LayoutInflater.from(context);
	LayoutInflater layoutInflater = (LayoutInflater)conext.getSystemService(Context.LAYOUT_INFLATER_SERVICE);

第一种方法实际上内部代码就是第二种方法，LayoutInflater中做了封装：


	public static LayoutInflater from(Context context) {
        	LayoutInflater LayoutInflater = (LayoutInflater) context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
			if (LayoutInflater == null) {
		        	    throw new AssertionError("LayoutInflater not found.");
			        	    
			}	
        	return LayoutInflater;
				    
	}


## inflate 方法
LayoutInflater是使用了Android提供的pull解析来解析布局文件

通过：`createViewFromTag()`方法，吧节点名和参数传入，根据节点名来创建`View`对象，`createViewFromTag()`方法内部调用了`createView()`方法使用反射的方式来创建`View`实例并返回。

`rInflate()`方法中循环遍历这个更布局下的子元素。


	inflate(int resource, ViewGroup root, boolean attachToRoot)

1. 如果root为null，attachToRoot将失去作用，设置任何值都没有意义。
2. 如果root不为null，attachToRoot设为true，则会给加载的布局文件的指定一个父布局，即root。
3. 如果root不为null，attachToRoot设为false，则会将布局文件最外层的所有layout属性进行设置，当该view被添加到父view当中时，这些layout属性会自动生效。
4. 在不设置attachToRoot参数的情况下，如果root不为null，attachToRoot参数默认为true。






参考：[http://blog.csdn.net/guolin_blog/article/details/12921889](http://blog.csdn.net/guolin_blog/article/details/12921889)
