---
title: RecyclerView
date: 2017-11-03 09:38:14
tags: RecyclerView
categories: android

---

官网介绍：[http://developer.android.com/training/material/lists-cards.html](http://developer.android.com/training/material/lists-cards.html)

![](http://i.imgur.com/O93BmPu.png)

<!--more-->

# 2018-04-07 去掉滚动条和滑动到边界阴影

```
android:overScrollMode="never"
android:scrollbars="none"
```

# 2017-11-03 滚动过程中修改 ViewHodler 中控件的样式

```java
    @Override
    public void onAttachedToRecyclerView(RecyclerView recyclerView) {
        super.onAttachedToRecyclerView(recyclerView);

        recyclerView.addOnScrollListener(new RecyclerView.OnScrollListener() {
            @Override
            public void onScrollStateChanged(RecyclerView recyclerView, int newState) {
                super.onScrollStateChanged(recyclerView, newState);


            }

            @Override
            public void onScrolled(RecyclerView recyclerView, int dx, int dy) {
                super.onScrolled(recyclerView, dx, dy);
                LinearLayoutManager lm = (LinearLayoutManager) recyclerView.getLayoutManager();
                int firstPosition = lm.findFirstCompletelyVisibleItemPosition();

                if (mDatas.size() == 0) {
                    return;
                }

                Log.d(TAG, "onScrolled: firstPosition:" + firstPosition);

                // 坑1. 不要使用 recyclerView.getChildAt() 获取 itemView, 这种方式获取的可能为 null
                // 也可以使用 mLayoutManager.findViewByPosition 获取
                ChoiceVH view = ((ChoiceVH) recyclerView.findViewHolderForAdapterPosition(firstPosition));
                ChoiceVH view1 = ((ChoiceVH) recyclerView.findViewHolderForAdapterPosition(firstPosition + 1));
                ChoiceVH view2 = ((ChoiceVH) recyclerView.findViewHolderForAdapterPosition(firstPosition + 2));
                ChoiceVH view3 = ((ChoiceVH) recyclerView.findViewHolderForAdapterPosition(firstPosition + 3));
                ChoiceVH view4 = ((ChoiceVH) recyclerView.findViewHolderForAdapterPosition(firstPosition + 4));
                setViewUI(view, 0);
                setViewUI(view1, 1);
                setViewUI(view2, 2);
                setViewUI(view3, 3);
                setViewUI(view4, 4);
            }
        });
    }

    private int[] mTextSizes = {12, 14, 16, 14, 12};

    private int[] mTextColors = {R.color.center_choice_0, R.color.center_choice_1, R.color.center_choice_2,
            R.color.center_choice_3, R.color.center_choice_4};

    private void setViewUI(ChoiceVH holder, int index) {
        if (holder == null) {
            Log.d(TAG, "setViewUIFirst: view is null");
            return;
        }
        TextView tv = holder.mTv;
        Log.d(TAG, "setViewUIFirst: name:" + tv.getText() + " index" + index);
        tv.setTextSize(TypedValue.COMPLEX_UNIT_SP, mTextSizes[index]);
        tv.setTextColor(mContext.getResources().getColor(mTextColors[index]));
    }
```

# GridLayoutManager 设置 横跨


# 2017-06-25 局部刷新

- 更改数据源，刷新 RecyclerView
- 根据 position 获取 ViewHolder 在获取 View，对其进行直接操作（会产生复用问题）

```
mList.get(position).put("favorites", "0");//直接更改数据源
ViewHolder viewHolder = (ViewHolder) mRecyclerView.findViewHolderForAdapterPosition(position); //得到要更新的item的view
viewHolder.mCheck.setBackgroundResource(R.mipmap.collect_normal);//更改状态
```

# 使用

## 适配器
>先写ViewHolder

	public class RecyclerAdapter extends RecyclerView.Adapter<RecyclerAdapter.MyViewHolder> {

	    private Context mContext;
	    private List<String> mData;

	    public RecyclerAdapter(Context context, List<String> data) {
	        mContext = context;
	        mData = data;
	    }

	    @Override
	    public int getItemViewType(int position) {
	        return super.getItemViewType(position);  // 这里的返回值可以随意写，listview中需要小于getViewTypeCount的返回值
	    }

	    @Override
	    public MyViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
	        View view = LayoutInflater.from(mContext).inflate(R.layout.item_recycler, parent, false);
	        return new MyViewHolder(view);
	    }

	    @Override
	    public void onBindViewHolder(MyViewHolder holder, int position) {
	        holder.mText.setText(mData.get(position));
	    }

	    @Override
	    public int getItemCount() {
	        return mData == null ? 0 : mData.size();
	    }

	    public static class MyViewHolder extends RecyclerView.ViewHolder {
	        private final TextView mText;

	        public MyViewHolder(View itemView) {
	            super(itemView);
	            mText = ((TextView) itemView.findViewById(R.id.item_text));
	        }
	    }
	}


1. 屏幕在滑动，RecyclerView中的子项在屏幕中消失或出现都会调用`onBindViewHolder`方法，`position`为数据在`mData`中的位置，所以可以通过这个方法来作分页加载。
## 设置

	RecyclerView recyclerView = (RecyclerView) findViewById(R.id.main_recycler);
	LinearLayoutManager manager = new LinearLayoutManager(this);
	manager.setOrientation(LinearLayoutManager.HORIZONTAL);
	manager.setReverseLayout(true);
	recyclerView.setLayoutManager(manager);
	recyclerView.setAdapter(new RecyclerAdapter(this, strings));

# 多布局


	@Override
	public int getItemViewType(int position) {
	    // 不同类型返回不同的布局id R.layout....
	}

	@Override
	public MyViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
		View view = LayoutInflater.from(mContext).inflate(viewType, parent, false);
		return new MyViewHolder(view);
	}


	@Override
	public void onBindViewHolder(MyViewHolder holder, int position) {
		switch(getItemType()){
			case R.layout....:
				// 数据设置
			;
		}
	}

# item的一些设置
>分割线

        recyclerView.addItemDecoration(new RecyclerView.ItemDecoration() {
            @Override
            public void onDraw(Canvas c, RecyclerView parent, RecyclerView.State state) {
                super.onDraw(c, parent, state);
                // 画背景
                c.drawColor(Color.RED);
            }

            @Override
            public void onDrawOver(Canvas c, RecyclerView parent, RecyclerView.State state) {
                super.onDrawOver(c, parent, state);
                // 画前景
                Paint paint = new Paint();
                paint.setColor(Color.RED);
                c.drawCircle(recyclerView.getWidth() / 2, recyclerView.getHeight() / 2, 100, paint);
            }

            @Override
            public void getItemOffsets(Rect outRect, View view, RecyclerView parent, RecyclerView.State state) {
                super.getItemOffsets(outRect, view, parent, state);
                int position = parent.getChildAdapterPosition(view);
                outRect.set(0, 5 * position, 0, 5 * position); //边距,分割线,可以设置不同宽高的分割线,控制每个分割线的宽高
            }
        });

# 监听器
>自定义了一个item点击监听

    private RecyclerView mRecyclerView;
    private OnChildClickListener mOnChildClickListener;

	// 下面两个方法在adapter和recyclerView关联的时候自动调用
    @Override
    public void onAttachedToRecyclerView(RecyclerView recyclerView) {
        super.onAttachedToRecyclerView(recyclerView);
        mRecyclerView = recyclerView;
    }

    @Override
    public void onDetachedFromRecyclerView(RecyclerView recyclerView) {
        super.onDetachedFromRecyclerView(recyclerView);
        mRecyclerView = null;
    }

    @Override
    public void onClick(View v) {
        if (mRecyclerView != null && mOnChildClickListener != null) {
            int position = mRecyclerView.getChildAdapterPosition(v);
            mOnChildClickListener.onChildClick(v, position, mData.get(position));
        }
    }

    public void setOnChildClickListener(OnChildClickListener onChildClickListener) {
        mOnChildClickListener = onChildClickListener;
    }

    public interface OnChildClickListener {
        void onChildClick(View view, int position, Object data);
    }

使用：

	adapter.setOnChildClickListener(this);

--

    @Override
    public void onChildClick(View view, int position, Object data) {
        // ...
    }

# 分页加载 及 上拉加载

参考：[http://www.cnblogs.com/hsji/p/4955957.html](http://www.cnblogs.com/hsji/
p/4955957.html)

`DARecyclerViewAdapte`:

    @Override
    public void onBindViewHolder(MyViewHolder holder, int position) {
        Log.d(TAG, "onBindViewHolder: position" + position);
		//....
        // 分页加载
        if (position == mData.size() - 1 && mPage <= mMaxPage) {
            loadData();
        }
    }

	private void loadData() {
        Toast.makeText(mContext, "加载数据", Toast.LENGTH_SHORT).show();
        NetWorkUtil.getService().getNDAchor(mPage++).enqueue(this);
    }

    public void setMaxPage(int maxPage){
        mMaxPage = maxPage;
    }


# 闪屏处理

去除默认动画

	mRvControlRecord.setAnimation(null);
