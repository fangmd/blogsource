---
title: ListView分页加载数据
date: 2016-03-13 21:38:14
tags: 图片
categories: android

---


## ListView分页加载数据小结
### 效果描述
分页：当用户在ListView界面上的时候不会加载全部的数据，而是从服务端加载一部分数据，当用户滑动ListView到末端的时候再从服务器加载更多数据。**即：**我们在使用新闻app的时候滑到底部才会加载新的内容。

<!--more-->
### 接口介绍:OnScrollListener

	// 静态属性
	public static int SCROLL_STATE_IDLE = 0; // 空闲状态
	public static int SCROLL_STATE_TOUCH_SCROLL = 1; // 滚动状态,并且手指在屏幕上
	public static int SCROLL_STATE_FLING = 2;	//	滚动状态,手指已经离开了屏幕
	
	// 抽象方法
	public void onScrollStateChanged(AbsListView view, int scrollState); 
	// ListView在状态改变的时候调用,用户在正常滑动的时候通常会执行三次(刻意滑动,当listView停止的时候才将手离开屏幕执行两次)

	public void onScroll(AbsListView view, int firstVisibleItem, int visibleItemCount, int totalItemCount); 
	// ListView在滚动的时候会反复调用该方法,调用次数和listView的子项无关(屏幕只要滑动一点就会调用)
	

### 关键代码

1. 设置成员属性：分页页码，默认1; 判断是否滑到底部的标记

		private int mPageNum = 1;
		private boolean isBottom;


2. 设置LIstView滑动监听器

		mNewsListView.setOnScrollListener(new OnScrollListener() {

			@Override
			public void onScrollStateChanged(AbsListView view, int scrollState) {
				if (scrollState == OnScrollListener.SCROLL_STATE_IDLE && isBottom) {
						// 下载更多数据
						Toast.makeText(MainActivity.this, "正在加载",
								Toast.LENGTH_SHORT).show();		
						//加载数据的方法代码.......
						//这里面的代码通常是根据mPageNum加载不同的数据
						// 对mPageNum处理: mPageNum++
					
				}
			}

			@Override
			public void onScroll(AbsListView view, int firstVisibleItem,
					int visibleItemCount, int totalItemCount) {
				if (firstVisibleItem + visibleItemCount == totalItemCount) {
					// 说明:
					// fistVisibleItem:表示划出屏幕的ListView子项个数
					// visibleItemCount:表示屏幕中正在显示的ListView子项个数
					// totalItemCount:表示ListView子项的总数	
					// 前两个相加==最后一个说明ListView滑到底部
					isButtom = true;
				}else{
					isButtom = false;
				}
			}
		});
		


### `mPageNum`的用法
1. 使用url获取数据
	
			String baseUrl = "http://litchiapi.jstv.com/";
			String newsUrl = "/api/GetFeeds?column=%d&PageSize=20&pageIndex=%d&val=100511D3BE5301280E0992C73A9DEC41";
			String url =  baseUrl + String.format(newsUrl, column, mPageNum++);
			
		column,mPageNum会分别替换newsUrl中的两个%d

### 可能出现的疑问
1. 为什么不把加载数据的代码写在onScroll()方法内,这样就可以省去一个成员变量:isBottom

			@Override
			public void onScroll(AbsListView view, int firstVisibleItem,
					int visibleItemCount, int totalItemCount) {
				if (firstVisibleItem + visibleItemCount == totalItemCount) {
					//加载更多数据
				}else{
					//不记载新数据
				}
			}
	个人测试结果：由于onScroll执行次数是不可控的,当listView在滑动到底部的时候,onScroll在满足加载新数据条件的瞬间执行了很多次，从而加载了很多暂时不需要的数据。
	
	![ListViewLog图](http://i.imgur.com/BwokV87.png)

2. 当ListView到达底部的时候，服务器没有更多数据可以加载。
	- 继续用手指滑动屏幕
	- 因为没有更多数据，所以屏幕ListView不会动
	- 这个时候：`onScrollStateChanged`方法会执行，但是`onScroll`方法不会执行

	