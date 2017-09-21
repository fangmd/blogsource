---
title: RecyclerView 问题记录
date: 2016-04-26 09:38:14
tags: RecyclerView
categories: android

---




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