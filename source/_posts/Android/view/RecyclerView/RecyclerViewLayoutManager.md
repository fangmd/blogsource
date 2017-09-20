---
title: RecyclerView LayoutManager
date: 2017-09-20 18:38:14
tags: [RecyclerView, LayoutManager]
categories: android

---


# 自定义 LayoutManager





<!--more-->

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


