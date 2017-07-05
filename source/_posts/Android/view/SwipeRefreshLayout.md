---
title: SwipeRefreshLayout
date: 2016-04-12 09:38:14
tags: SwipeRefreshLayout
categories: android

---


# SwipeRefreshLayout
>谷歌公司推出的用于下拉刷新的控件，SwipeRefreshLayout已经被放到了sdk中，在Version 19.1之后SwipeRefreshLayout 被放到support v4中。
源码在SDK\sdk\extras\android\support\v4\src\java\android\support\v4\widget\SwipeRefreshLayout.java

<!--more-->

参考：[http://blog.csdn.net/dalancon/article/details/46125667](http://blog.csdn.net/dalancon/article/details/46125667)

## API

1. `setOnRefreshListener(OnRefreshListener listener)`  设置下拉监听，当用户下拉的时候会去执行回调
2. `setColorSchemeColors(int... colors)` 设置 进度条的颜色变化，最多可以设置4种颜色
3. `setProgressViewOffset(boolean scale, int start, int end)` 调整进度条距离屏幕顶部的距离
4. `setRefreshing(boolean refreshing)` 设置SwipeRefreshLayout当前是否处于刷新状态，一般是在请求数据的时候设置为true，在数据被加载到View中后，设置为false。

## 使用方法

### 1 获取布局控件对象

	// android.support.v4.widget.SwipeRefreshLayou
	mSwipeRefreshLayout = (SwipeRefreshLayout) view.findViewById(R.id.discovery_anchor_swipeRefreshLayout);

### 2 设置颜色

	mSwipeRefreshLayout.setColorSchemeColors(R.color.red, R.color.blue, R.color.lightyellow);

### 3 设置刷新的监听

	mSwipeRefreshLayout.setOnRefreshListener(this);

    @Override
    public void onRefresh() {
		loadData();
		// 加载数据，每次设置`mSwipeRefreshLayout.setRefreshing(true);`是就会执行这个方法；需要在数据加载完成的地方执行`mSwipeRefreshLayout.setRefreshing(false);`
    }

### 4

	// 这句话是为了，第一次进入页面的时候显示加载进度条
    mSwipeRefreshLayout.setProgressViewOffset(false
            , 0
            , (int) TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP, 24, getResources().getDisplayMetrics()));

## 和RecyclerView连用代码

    recyclerView.addOnScrollListener(new RecyclerView.OnScrollListener() {
        @Override
        public void onScrollStateChanged(RecyclerView recyclerView, int newState) {
            super.onScrollStateChanged(recyclerView, newState);
            if (newState == RecyclerView.SCROLL_STATE_IDLE && mLastVisibleItem + 1 == mAdapter.getItemCount() ) {
                mSwipeRefreshLayout.setRefreshing(true);
                // 此处在现实项目中，请换成网络请求数据代码，sendRequest .....
                handler.sendEmptyMessageDelayed(0, 3000);
            }
        }

        @Override
        public void onScrolled(RecyclerView recyclerView, int dx, int dy) {
            super.onScrolled(recyclerView, dx, dy);
            mLastVisibleItem = mLinearLayoutManager.findLastVisibleItemPosition(); // mLastVisibleItem == 单前屏幕最下方的item的position
        }
    });

## 注意
`SwipeRefreshLayout`的子控件必须是可滑动的，比如`ListView,RecyclerView,ScrollView`
