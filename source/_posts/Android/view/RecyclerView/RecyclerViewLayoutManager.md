---
title: RecyclerView LayoutManager
date: 2016-04-17 18:38:14
tags: [RecyclerView, LayoutManager]
categories: android

---


# RecyclerView LayoutManager

<!--more-->

## StaggeredGridLayoutManager

### 让某个item占多列
在Adapter中添加下面代码：

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


## LinearLayoutManager

## GridLayoutManager

