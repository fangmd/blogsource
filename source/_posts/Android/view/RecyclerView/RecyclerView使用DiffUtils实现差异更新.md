---
title: RecyclerView 使用 DiffUtil 实现差异更新
date: 2017-09-24 09:23:14
tags: features
categories: android

---


# RecyclerView 的 Adapter 提供的局部更新方法

```java
adapter.notifyItemChange()
adapter.notifyItemInserted()
adapter.notifyItemRemoved()
adapter.notifyItemMoved();
```

需要实现局部更新，只需要计算出更新的 item 的 position 就可以了

# DiffUtil

作用：对比两个数据集，计算出变动项，使用 RecyclerView.Adapter 的局部更新方法。


## DiffUtil.Callback


Callback 中有 4 个方法需要实现：

1. getOldListSize()：旧数据集的长度。
2. getNewListSize()：新数据集的长度
3. areItemsTheSame()：判断是否是同一个Item。
4. areContentsTheSame()：如果是通一个Item，此方法用于判断是否同一个 Item 的内容也相同。

## 例子

数据源格式是 String:

```java
public class AdapterDiffCallback extends DiffUtil.Callback {

    List<String> mOldData;
    List<String> mNewData;


    public AdapterDiffCallback(List<String> oldData, List<String> newData) {
        mOldData = oldData;
        mNewData = newData;

    }

    @Override
    public int getOldListSize() {
        return mOldData.size();
    }

    @Override
    public int getNewListSize() {
        return mNewData.size();
    }

    @Override
    public boolean areItemsTheSame(int oldItemPosition, int newItemPosition) {
        return mOldData.get(oldItemPosition).getClass().equals(mNewData.get(newItemPosition).getClass());
    }

    @Override
    public boolean areContentsTheSame(int oldItemPosition, int newItemPosition) {
        String s = mOldData.get(oldItemPosition);
        String s1 = mNewData.get(newItemPosition);
        return s.equals(s1);
    }
}
```

使用这个：

```java
DiffUtil.DiffResult diffResult = DiffUtil.calculateDiff(new AdapterDiffCallback(mNewData, mOldData));
diffResult.dispatchUpdatesTo(mAdapter);
```
