---
title: RecyclerView简单拖动和滑动
date: 2016-04-17 18:38:14
tags: RecyclerView
categories: android

---


# RecyclerView简单拖动和滑动

<!--more-->

参考：

- [http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0630/3123.html](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0630/3123.html)
- [https://github.com/iPaulPro/Android-ItemTouchHelper-Demo](https://github.com/iPaulPro/Android-ItemTouchHelper-Demo)

## 0 recyclerViewAdapter
添加如下方法

    public void onItemDismiss(int position) {
        mData.remove(position);
        notifyItemRemoved(position);
    }

    public boolean onItemMove(int fromPosition, int toPosition) {
        Collections.swap(mData, fromPosition, toPosition);
        notifyItemMoved(fromPosition, toPosition);
        return true;
    }

## 1 创建一个ItemTouchHelper.Callback子类

	public class MyItemTouchHelper extends ItemTouchHelper.Callback {
	
	    private final AlbumDetailRecyclerViewAdapter mAdapter; 
	
	    public MyItemTouchHelper(AlbumDetailRecyclerViewAdapter adapter) {
	        mAdapter = adapter;
	    }
	
		// 把左右判断为滑动删除，上下绑定为拖动，
		// 如果使用网格布局 需要把上下左右都判断为拖动
	    @Override
	    public int getMovementFlags(RecyclerView recyclerView, RecyclerView.ViewHolder viewHolder) {
	        int dragFlags = ItemTouchHelper.UP | ItemTouchHelper.DOWN;
	        int swipeFlags = ItemTouchHelper.START | ItemTouchHelper.END;
	        return makeMovementFlags(dragFlags, swipeFlags);
	    }
	
	    @Override
	    public boolean isLongPressDragEnabled() {
	        return true;
	    }
	
	    @Override
	    public boolean isItemViewSwipeEnabled() {
	        return true;
	    }
	
	
	    @Override
	    public boolean onMove(RecyclerView recyclerView, RecyclerView.ViewHolder viewHolder, RecyclerView.ViewHolder target) {
	        mAdapter.onItemMove(viewHolder.getAdapterPosition(), target.getAdapterPosition());
	        return true;
	    }
	
	    @Override
	    public void onSwiped(RecyclerView.ViewHolder viewHolder, int direction) {
	        mAdapter.onItemDismiss(viewHolder.getAdapterPosition());
	    }
	
	} 

## 2 给recyclerView 添加触摸功能

    MyItemTouchHelper itemTouchHelperCallback = new MyItemTouchHelper(mRecyclerViewAdapter);
    ItemTouchHelper itemTouchHelper = new ItemTouchHelper(itemTouchHelperCallback);
    itemTouchHelper.attachToRecyclerView(mAlbumList);