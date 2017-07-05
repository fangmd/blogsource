---
title: ViewPager与Fragment连用 填坑
date: 2016-05-12 09:13:12
tags: [Fragment,ViewPager]
category: Android

---

# ViewPager与Fragment连用 填坑
问题：ViewPager滑动的时候Fragment内部加载的数据消除了，划回来又要重新加载

## 方案一： 不推荐
增加ViewPager的缓存数量。

    mViewPager.setOffscreenPageLimit(2);

## 方案二：

>Fragment关联的view hierarchy正在被移除时，不会执行onDestroy()方法，而是会调用onDestroyView()。

>比如 ViewPager + Fragment，默认情况下当ViewPager滑动到第三页的时候，第一页的Fragment就会执行onDestroyView，当再次滑动到第二页的时候，第一页的Fragment的onCreateView又会重新执行绘制页面。伴随而来的问题就是成员变量要重新赋值一次，辛苦耗时加载出来的页面又要重新加载一次，这样也就给内存增加了无意思的压力，用户体验上也不大友好，尤其在有网络请求等开销时长比较长的情况下。

*重点：*onDestroyView的执行和Activity的onDestroy不一样，不会销毁当前的页面，所以Fragment的所有成员变量的引用都还在。

解决基本思路：

1. 在`onCreateView()`中将创建的布局提升成全局变量，判断View是否为null，如果是null创建，不为null直接返回View。PS：可以将需要赋值或者处理的子控件在这里都`find`出来并提升成全局变量。

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
                             Bundle savedInstanceState) {
        if (mRootView == null) {
            mRootView = inflater.inflate(R.layout.discovery_category_fragment, container, false);
        }
        return mRootView;
    }

2. 在`onDestoryView()`的时候将`mRootView`从他的父控件中移除

    @Override
    public void onDestroyView() {
        super.onDestroyView();
        ((ViewGroup) mRootView.getParent()).removeAllViews();
    }

参考：[http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2014/0729/1635.html](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2014/0729/1635.html)


