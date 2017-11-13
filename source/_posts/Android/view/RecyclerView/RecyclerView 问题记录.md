---
title: RecyclerView 问题记录
date: 2016-04-26 09:38:14
tags: RecyclerView
categories: android

---

# Item remove 后 点击事件中的 position 没有更新

问题描述：移除一个 item 后通过 `mAdapter.notifyItemRemoved(mDeletePosition);` 这种方式更新 Adapter，再去点击移除项下面的 item 的时候 position 错误。

旧代码：

```
    @Override
    public void onBindViewHolder(final RecyclerView.ViewHolder holder, final int position) {
        //...
        if (mListener != null) {
            holder.itemView.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View view) {
                    mListener.onItemClick(holder, position, mDatas.get(position));
                }
            });
        }

        if (mLongClickListener != null) {
            holder.itemView.setOnLongClickListener(new View.OnLongClickListener() {
                @Override
                public boolean onLongClick(View v) {
                    mLongClickListener.onItemLongClick(holder, position, mDatas.get(position));
                    return true;
                }
            });
        }
    }
```

新代码：

```java
    @Override
    public void onBindViewHolder(final RecyclerView.ViewHolder holder, final int position) {
        //...
        if (mListener != null) {
            holder.itemView.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View view) {
                    int position = holder.getAdapterPosition();
                    mListener.onItemClick(holder, position, mDatas.get(position));
                }
            });
        }

        if (mLongClickListener != null) {
            holder.itemView.setOnLongClickListener(new View.OnLongClickListener() {
                @Override
                public boolean onLongClick(View v) {
                    int position = holder.getAdapterPosition();
                    mLongClickListener.onItemLongClick(holder, position, mDatas.get(position));
                    return true;
                }
            });
        }
    }
```



# 在点击事件中更新其他项时报错

![](http://7xr8nu.com1.z0.glb.clouddn.com/recyclerviewsinglechoice.png)

![](http://7xr8nu.com1.z0.glb.clouddn.com/androidstudio.png)

需要制作子项选择后更新×投票×按钮的颜色的效果。

<!--more-->

如果在item点击事件中直接使用`notifyDataSetChanged();`来更新界面可能会报下面的错误：

	java.lang.IllegalStateException: Cannot call this method while RecyclerView is computing a layout or scrolling

解决方案：

    private void specialUpdate() {
        Handler handler = new Handler();
        final Runnable r = new Runnable() {
            public void run() {
                notifyItemChanged(getItemCount() - 1);
            }
        };
        handler.post(r);
    }

# 在 RecyclerView 中使用 CardView


cardView 的 match_parent 属性实效

处理方法：

```java
    @Override
    public Holder onCreateViewHolder(ViewGroup parent, int viewType) {

        View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.item2, null, true);
        view.setLayoutParams(new RecyclerView.LayoutParams(
                RecyclerView.LayoutParams.MATCH_PARENT,
                RecyclerView.LayoutParams.WRAP_CONTENT
        ));
        return new Holder((view));
    }
```

处理方法二：

```
CardView 不作为 Item 的 根布局
```