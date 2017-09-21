---
title: RecyclerView LayoutManager
date: 2017-09-20 18:38:14
tags: [RecyclerView, LayoutManager]
categories: android

---


# 自定义 LayoutManager


## 继承 RecyclerView.LayoutManager 抽象类

实现 抽象方法：

```java
    @Override
    public RecyclerView.LayoutParams generateDefaultLayoutParams() {
        return new RecyclerView.LayoutParams(ViewGroup.LayoutParams.WRAP_CONTENT, ViewGroup.LayoutParams.WRAP_CONTENT);
    }
```

## 管理子 View 的布局

重写 `onLayoutChildren` 方法

```java
    @Override
    public void onLayoutChildren(RecyclerView.Recycler recycler, RecyclerView.State state) {
        super.onLayoutChildren(recycler, state);

        // 1. 先移除所有view
        removeAllViews();

        // 2. 在布局之前，将所有的子 View 先 Detach 掉，放入到 Scrap 缓存中
        detachAndScrapAttachedViews(recycler);

        // 3. 获取 View，获取 View 的宽高，计算 View 应该的布局位置，注意要对所有的 子 View 设置位置所以下面代码在循环中操作

        // 获取 View Item 的数量
        int itemCount = getItemCount();

        // 从缓存中取 View
        final View view = recycler.getViewForPosition(position);
        // 将 Item View 加入到 RecyclerView 中
        addView(view);

        // 获取 View 的宽高
        // 测量 Item View
        measureChildWithMargins(view, 0, 0);
        int viewWidth = getDecoratedMeasuredWidth(view);
        int viewHeight = getDecoratedMeasuredHeight(view);

        // 4 放置 View，没有 layoutDecoratedWithMargins 的 View 不会显示
        layoutDecoratedWithMargins(view, left, top, right, bottom)

    }

```

<!--more-->


## 设置是否可滑动

充血下面的，true：表示可滑动，false：表示不可滑动，默认false

```java
        /**
         * Query if horizontal scrolling is currently supported. The default implementation
         * returns false.
         *
         * @return True if this LayoutManager can scroll the current contents horizontally
         */
        public boolean canScrollHorizontally() {
            return false;
        }

        /**
         * Query if vertical scrolling is currently supported. The default implementation
         * returns false.
         *
         * @return True if this LayoutManager can scroll the current contents vertically
         */
        public boolean canScrollVertically() {
            return false;
        }
```


如果设置了可滑动，就需要重写下面的两个方法中的一个：

```java
        /**
         * Scroll horizontally by dx pixels in screen coordinates and return the distance traveled.
         * The default implementation does nothing and returns 0.
         *
         * @param dx            distance to scroll by in pixels. X increases as scroll position
         *                      approaches the right.
         * @param recycler      Recycler to use for fetching potentially cached views for a
         *                      position
         * @param state         Transient state of RecyclerView
         * @return The actual distance scrolled. The return value will be negative if dx was
         * negative and scrolling proceeeded in that direction.
         * <code>Math.abs(result)</code> may be less than dx if a boundary was reached.
         */
        public int scrollHorizontallyBy(int dx, Recycler recycler, State state) {
            return 0;
        }

        /**
         * Scroll vertically by dy pixels in screen coordinates and return the distance traveled.
         * The default implementation does nothing and returns 0.
         *
         * @param dy            distance to scroll in pixels. Y increases as scroll position
         *                      approaches the bottom.
         * @param recycler      Recycler to use for fetching potentially cached views for a
         *                      position
         * @param state         Transient state of RecyclerView
         * @return The actual distance scrolled. The return value will be negative if dy was
         * negative and scrolling proceeeded in that direction.
         * <code>Math.abs(result)</code> may be less than dy if a boundary was reached.
         */
        public int scrollVerticallyBy(int dy, Recycler recycler, State state) {
            return 0;
        }
```




# StaggeredGridLayoutManager

## 让某个 item 占多列
在 Adapter 中添加下面代码：

    @Override 
    public void onBindViewHolder(RecyclerView.ViewHolder holder, int position) {
        if (holder instanceof CommonItemViewHolder) {
            ... 
        } else if (holder instanceof HeaderViewHolder) {
            ... 
            StaggeredGridLayoutManager.LayoutParams layoutParams = new StaggeredGridLayoutManager.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.MATCH_PARENT);
            layoutParams.setFullSpan(true);
            holder.itemView.setLayoutParams(layoutParams);
        } 
    } 


# LinearLayoutManager

# GridLayoutManager


