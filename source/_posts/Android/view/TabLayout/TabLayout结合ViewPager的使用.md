---
title: TabLayout
date: 2016-04-05 20:38:14
tags: TabLayout
categories: android

---


# TabLayout结合ViewPager

## 导入支持包

在app/目录下按f4或者按![](http://i.imgur.com/Ek2HnGa.png)

---

选择Dependencies页面，点击右边的“+”号，选择library dependency
![](http://i.imgur.com/Gtrbzkl.png)

---

搜索design，选择下面出现的一个包，点击ok
![](http://i.imgur.com/le4E1vu.png)

---
这个时候Android studio 会自动重新编译代码，如果没有就需要手动同步代码

![](http://i.imgur.com/AYAOn7E.png)

## 开始使用

### 在xml布局文件中加入tablayout控件

    <!--tablayout 在Design包下android5.0中新的东西-->
    <android.support.design.widget.TabLayout
        android:id="@+id/discovery_tab_bar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"/>

加入viewpager：

    <android.support.v4.view.ViewPager
        android:id="@+id/discovery_viewpager"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">


### 关联TabLayout和ViewPager

方法一：

	TabLayout tabLayout = (TabLayout) view.findViewById(R.id.discovery_tab_bar);
	tabLayout.setOnTabSelectedListener(this);

	List<String> list = new ArrayList<>();
	Collections.addAll(list, "推荐", "分类", "广播", "榜单", "主播");
	TabLayout.Tab tab = null;
	for (String s : list) {
		tab = tabLayout.newTab();
		tab.setText(s);
		tabLayout.addTab(tab);
	}

	mViewPager = (ViewPager) view.findViewById(R.id.discovery_viewpager);
	DiscoveryFragmentPageAdapter adapter = new DiscoveryFragmentPageAdapter(getActivity().getSupportFragmentManager(), list);
	mViewPager.setAdapter(adapter);

	mViewPager.addOnPageChangeListener(new TabLayout.TabLayoutOnPageChangeListener(tabLayout));

---
方法二：推荐使用

	TabLayout tabLayout = (TabLayout) view.findViewById(R.id.discovery_tab_bar);

	List<String> list = new ArrayList<>();
	Collections.addAll(list, "推荐", "分类", "广播", "榜单", "主播");

	mViewPager = (ViewPager) view.findViewById(R.id.discovery_viewpager);
	DiscoveryFragmentPageAdapter adapter = new DiscoveryFragmentPageAdapter(getActivity().getSupportFragmentManager(), list);
	mViewPager.setAdapter(adapter);

	tabLayout.setupWithViewPager(mViewPager);

还需要在适配器中添加方法：

    @Override
    public CharSequence getPageTitle(int position) {
        return mList.get(position);
    }

## TabLayout重要属性

属性：

- 滑块颜色：

		app:tabIndicatorColor="@color/white"

- 字体颜色：

		app:tabSelectedTextColor="@color/gray"  app:tabTextColor="@color/white"
- tab模式：

		app:tabMode="fix"
	fix:表示所有tab会挤在屏幕被不能滚动

	scrollable：表示Tab可以滚动

- tabGravity：

		app:tabGravity="center"
	center:点击接近屏幕边缘的Tab时会聚焦（当然会聚焦啦）并居中

	fill:点击接近屏幕边缘的Tab时会向屏幕中心移动，直至能完全显示整个Tab标签，不会居中

## TabLayout 自定义指示器

关键代码：

    mTabLayout.setupWithViewPager(mViewPager);

    for (int i = 0; i < mTabLayout.getTabCount(); i++) {
        TabLayout.Tab tab = mTabLayout.getTabAt(i);
        if (tab != null) {
            tab.setCustomView(getTabView(i));
        }
    }

    public View getTabView(int position) {
        View v = LayoutInflater.from(this).inflate(R.layout.custom_tab, null);
        TextView tv = (TextView) v.findViewById(R.id.tv_tab);
        tv.setText(String.format("第%d个", position));
        //        ImageView img = (ImageView) v.findViewById(R.id.imageView);
        //img.setImageResource(imageResId[position]);
        return v;
    }
