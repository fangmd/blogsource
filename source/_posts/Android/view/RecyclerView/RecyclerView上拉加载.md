---
title: RecyclerView上拉加载
date: 2016-04-12 09:38:14
tags: RecyclerView
categories: android

---


# RecyclerView上拉加载

<!--more-->
## 方案一：
>效果不好

	private int mPage = 2; //分页加载，设置加载最多加载次数，按实际需要确定
    private int mMaxPage;

    @Override
    public void onBindViewHolder(MyViewHolder holder, int position) {
        Log.d(TAG, "onBindViewHolder: position" + position);
		//...
        // 分页加载 上拉加载
        if (position == mData.size()) {
            if (mPage <= mMaxPage) {
                loadData();
                holder.mLoadText.setText("正在加载...");
            } else {
                holder.mLoadText.setText("没有更多数据啦...");
            }
        }
    }

    @Override
    public int getItemCount() {
        return mData.size() + 1; // 预留一个位置给刷新布局
    }

    @Override
    public int getItemViewType(int position) {
        if (position == mData.size()) {
            return R.layout.load_more; // 利用了多布局的方法
        } else {
            return R.layout.d_a_recycler_item;
        }
    }

    private void loadData() {
        NetWorkUtil.getService().getNDAchor(mPage++).enqueue(this);
    }

    public void removeFootRequest() {  // 移除底部提示
        notifyItemRemoved(mData.size());
    }

    public void setMaxPage(int maxPage) {
        mMaxPage = maxPage;
    }