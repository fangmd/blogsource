---
title: RecyclerView 扩展 SnapHelper
date: 2017-02-07 18:38:14
tags: [RecyclerView, SnapHelper]
categories: Android

---


# 基本使用 

LinearSnapHelper：LinearSnapHelper继承自SnapHelper，这个默认是让视图停在中间的，你只需要将RecyclerView和LinearSnapHelper绑定在一起就行了。

```java
LinearSnapHelper mLinearSnapHelper = new LinearSnapHelper();
mLinearSnapHelper.attachToRecyclerView(mRecyclerview);
```

## 让 RecyclerView 停在左边，或者右边

继承 LinearSnapHelper 重写里面的两个方法：

1. calculateDistanceToFinalSnap：当拖拽或滑动结束时会回调该方法，返回一个out = int[2]，out[0]x轴，out[1] y轴 ，这个值就是需要修正的你需要的位置的偏移量。 
2. findSnapView：这个方法用来获取特定的视图，当返回null时，表示没有获取到任何视图 。


<!--more-->

```java
public class LeftSnapHelper extends LinearSnapHelper {

    private OrientationHelper mHorizontalHelper;

    /**
     * 当拖拽或滑动结束时会回调该方法,该方法返回的是一个长度为2的数组,out[0]表示横轴,x[1]表示纵轴,这两个值就是你需要修正的位置的偏移量
     *
     * @param layoutManager
     * @param targetView
     * @return
     */
    @Override
    public int[] calculateDistanceToFinalSnap(RecyclerView.LayoutManager layoutManager, View targetView) {
        //注:由于是横向滚动,在这里我们只考虑横轴的值
        int[] out = new int[2];
        if (layoutManager.canScrollHorizontally()) {
            out[0] = distanceToStart(targetView, getHorizontalHelper(layoutManager));
        } else {
            out[0] = 0;
        }
        return out;
    }

    /**
     * 这个方法是计算偏移量
     *
     * @param targetView
     * @param helper
     * @return
     */
    private int distanceToStart(View targetView, OrientationHelper helper) {
        return helper.getDecoratedStart(targetView) - helper.getStartAfterPadding();
    }

    @Override
    public View findSnapView(RecyclerView.LayoutManager layoutManager) {
        return findStartView(layoutManager, getHorizontalHelper(layoutManager));
    }

    /**
     * 找到第一个显示的view
     * @param layoutManager
     * @param helper
     * @return
     */
    private View findStartView(RecyclerView.LayoutManager layoutManager,
                               OrientationHelper helper) {
        if (layoutManager instanceof LinearLayoutManager) {
            int firstChild = ((LinearLayoutManager) layoutManager).findFirstVisibleItemPosition();
            int lastChild = ((LinearLayoutManager) layoutManager).findLastVisibleItemPosition();
            if (firstChild == RecyclerView.NO_POSITION) {
                return null;
            }

            //这是为了解决当翻到最后一页的时候，最后一个Item不能完整显示的问题
            if (lastChild == layoutManager.getItemCount() - 1) {
                return layoutManager.findViewByPosition(lastChild);
            }
            View child = layoutManager.findViewByPosition(firstChild);

            //得到此时需要左对齐显示的条目
            if (helper.getDecoratedEnd(child) >= helper.getDecoratedMeasurement(child) / 2
                    && helper.getDecoratedEnd(child) > 0) {
                return child;
            } else {
                return layoutManager.findViewByPosition(firstChild + 1);
            }
        }
        return super.findSnapView(layoutManager);
    }

    /**
     * 获取视图的方向
     *
     * @param layoutManager
     * @return
     */
    private OrientationHelper getHorizontalHelper(@NonNull RecyclerView.LayoutManager layoutManager) {
        if (mHorizontalHelper == null) {
            mHorizontalHelper = OrientationHelper.createHorizontalHelper(layoutManager);
        }
        return mHorizontalHelper;
    }
}
```

让它停在右边：只需要在上面的基础上修改findSnapView方法即可

```java
public class RightSnapHelper extends LinearSnapHelper {

    private OrientationHelper mHorizontalHelper;

    /**
     * 当拖拽或滑动结束时会回调该方法,该方法返回的是一个长度为2的数组,out[0]表示横轴,x[1]表示纵轴,这两个值就是你需要修正的位置的偏移量
     *
     * @param layoutManager
     * @param targetView
     * @return
     */
    @Override
    public int[] calculateDistanceToFinalSnap(RecyclerView.LayoutManager layoutManager, View targetView) {
        //注:由于是横向滚动,在这里我们只考虑横轴的值
        int[] out = new int[2];
        if (layoutManager.canScrollHorizontally()) {
            out[0] = distanceToEnd(targetView, getHorizontalHelper(layoutManager));
        } else {
            out[0] = 0;
        }
        return out;
    }

    /**
     * 这个方法是计算偏移量
     *
     * @param targetView
     * @param helper
     * @return
     */
    private int distanceToEnd(View targetView, OrientationHelper helper) {
        return helper.getDecoratedEnd(targetView) - helper.getEndAfterPadding();
    }

    @Override
    public View findSnapView(RecyclerView.LayoutManager layoutManager) {
        return findEndView(layoutManager, getHorizontalHelper(layoutManager));
    }

    /**
     * 找到第一个显示的view
     *
     * @param layoutManager
     * @param helper
     * @return
     */
    private View findEndView(RecyclerView.LayoutManager layoutManager, OrientationHelper helper) {
        if (layoutManager instanceof LinearLayoutManager) {
            int lastChild = ((LinearLayoutManager) layoutManager).findLastVisibleItemPosition();
            if (lastChild == RecyclerView.NO_POSITION) {
                return null;
            }

            View child = layoutManager.findViewByPosition(lastChild);

            //得到此时需要右对齐显示的条目
            if (helper.getDecoratedStart(child) >= helper.getDecoratedMeasurement(child) / 2
                    && helper.getDecoratedStart(child) > 0) {
                return child;
            } else {
                return layoutManager.findViewByPosition(lastChild - 1);
            }
        }
        return super.findSnapView(layoutManager);
    }

    /**
     * 获取视图的方向
     *
     * @param layoutManager
     * @return
     */
    private OrientationHelper getHorizontalHelper(@NonNull RecyclerView.LayoutManager layoutManager) {
        if (mHorizontalHelper == null) {
            mHorizontalHelper = OrientationHelper.createHorizontalHelper(layoutManager);
        }
        return mHorizontalHelper;
    }
}
```



参考：

- [http://www.jianshu.com/p/3b827b6384db](http://www.jianshu.com/p/3b827b6384db)
- [http://blog.csdn.net/whitley_gong/article/details/52421215](http://blog.csdn.net/whitley_gong/article/details/52421215)