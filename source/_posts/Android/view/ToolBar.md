---
title: ToolBar 
date: 2016-03-22 12:18:12
tags: ToolBar
category: android

---


## 如何创建一个ToolBar
1. 在activity布局文件中加入`Toolbar`控件

	<android.support.v7.widget.Toolbar
	   android:id="@+id/my_toolbar"
	   android:layout_width="match_parent"
	   android:layout_height="?attr/actionBarSize"
	   android:background="?attr/colorPrimary"
	   android:elevation="4dp"
	   android:theme="@style/ThemeOverlay.AppCompat.ActionBar"
	   app:popupTheme="@style/ThemeOverlay.AppCompat.Light"/>

<!--more-->

标题颜色的设置注意兼容问题
2. 修改`sytle`资源文件

	    <style name="MyTheme" parent="Theme.AppCompat.Light.NoActionBar">
        	<!-- Customize your theme here. -->
        	<item name="colorPrimary">@color/colorPrimary</item>
        	<item name="colorPrimaryDark">@color/colorPrimaryDark</item>
        	<item name="colorAccent">@color/colorAccent</item>
    	</style>
3. 在清单文件中更改activity的主题，为`android:theme="@style/MyTheme"`

##ToolBar常见的设置

0. 0

		setSupportActionbar(toolbar);

1. 设置标题

		actionBar.setTitle("微信");
2. 设置不显示标题

		actionBar.setDisplayShowTitleEnabled(false);
2. 设置显示返回按钮`Home`，左侧按钮：对应的系统id为`android.R.home`

		actionBar.setDisplayHomeAsUpEnabled(true);

3. 定制返回按钮`Home`的图标

		actionBar.setHomeAsUpIndicator(R.mipmap.ic_add_black_18dp);

3. 设置标题图标

		actionBar.setIcon(R.mipmap.ic_launcher);
        actionBar.setLogo(R.mipmap.ic_launcher);
两个的效果一样
4. 设置监听事件

	    @Override
    	public boolean onOptionsItemSelected(MenuItem item) {
        	switch (item.getItemId()) {
            	case android.R.id.home: // 返回键的id
                	finish();
                	break;
        	}
        	return true;
    	}
5. 用反射机制获取title对应的`TextView`

    	private TextView getActionBarTitleTextView() {
        	TextView titleTextView = null;

        	try {
            	Field f = mToolbar.getClass().getDeclaredField("mTitleTextView");
            	f.setAccessible(true);
            	titleTextView = ((TextView) f.get(mToolbar));
        	} catch (NoSuchFieldException e) {
            	e.printStackTrace();
        	} catch (IllegalAccessException e) {
            	e.printStackTrace();
        	}
        	return titleTextView;
    	}

7. 在toolbar上使用spinner
6. 自定义内部控件

		一些布局属性虽然没有提示，但是可以使用。
8. 添加一个搜索按钮
	- 添加`menu`xml文件

			<?xml version="1.0" encoding="utf-8"?>
			<menu xmlns:android="http://schemas.android.com/apk/res/android"
      				xmlns:app="http://schemas.android.com/apk/res-auto">
    			<item
        			android:id="@+id/search"
        			android:icon="@android:drawable/ic_menu_search"
        			app:actionViewClass="android.support.v7.widget.SearchView"
        			app:showAsAction="ifRoom|collapseActionView"
        			android:title="Search"
        		/>
			</menu>
	这里使用兼容的方式，注意`app`；`showAsAction`里面的属性必须有`collapseActionView`才能用代码控制折叠
	
	- 代码二

		    @Override
    		public boolean onCreateOptionsMenu(Menu menu) {
        		getMenuInflater().inflate(R.menu.option, menu);
        		mItem = menu.findItem(R.id.search);
        		SearchView search = ((SearchView) MenuItemCompat.getActionView(mItem));
        		search.setOnQueryTextListener(this);
        		return true;
    		}

   		 	@Override
    		public boolean onQueryTextSubmit(String query) {
        		Toast.makeText(MainActivity.this, "搜索" + query, Toast.LENGTH_SHORT).show();
        		MenuItemCompat.collapseActionView(mItem);
        		return true;
    		}
	搜索添加输入方式：`search.setInputType(InputType.TYPE_CLASS_NUMBER);`
	
	点击其他控件展开搜索：
   
		@Override
		public void onClick(View v) {
			Toast.makeText(MainActivity.this, "点击事件打开搜索", Toast.LENGTH_SHORT).show();
			MenuItemCompat.expandActionView(mItem);
		}



## ToolBar 左侧空白处理

	<style name="ClubToolbar" parent="Widget.AppCompat.Toolbar">  
		<item name="contentInsetStart">0dp</item><!-- 设置该属性解决空白部分-->  
	</style>

	<!-- Base application theme. “NoActionBar”隐藏原有的Actionbar 设置Toolbar -->
	    <style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
		//.....
		<item name="toolbarStyle">@style/ClubToolbar</item>
	    </style>














## ToolBar Class OverView
>A standard toolbar for use within application content.

>A Toolbar is a generalization of action bars for use within application layouts. While an action bar is traditionally part of an Activity's opaque window decor controlled by the framework, a Toolbar may be placed at any arbitrary level of nesting within a view hierarchy. An application may choose to designate a Toolbar as the action bar for an Activity using the setActionBar() method.

Toolbar也属于action bars的范畴，用来给用户控制应用的布局。它是一个不透明的窗口的传统部件，用来控制fragmentwork，toolbar里面可以放置多层次的内部组件。 可以通过`setActionBar()`方法来设置`Toolbar`


>Toolbar supports a more focused feature set than ActionBar. From start to end, a toolbar may contain a combination of the following optional elements:

Toolbar提供更多的特性比`ActionBar`，有一下特性

>A navigation button. This may be an Up arrow, navigation menu toggle, close, collapse, done or another glyph of the app's choosing. This button should always be used to access other navigational destinations within the container of the Toolbar and its signified content or otherwise leave the current context signified by the Toolbar. The navigation button is vertically aligned within the Toolbar's minimum height, if set.

导航按钮：home按钮
>
>A branded logo image. This may extend to the height of the bar and can be arbitrarily wide.

一个logo图片

>A title and subtitle. The title should be a signpost for the Toolbar's current position in the navigation hierarchy and the content contained there. The subtitle, if present should indicate any extended information about the current content. If an app uses a logo image it should strongly consider omitting a title and subtitle.

标题和副标题

>One or more custom views. The application may add arbitrary child views to the Toolbar. They will appear at this position within the layout. If a child view's Toolbar.LayoutParams indicates a Gravity value of CENTER_HORIZONTAL the view will attempt to center within the available space remaining in the Toolbar after all other elements have been measured.

其他视图控件：通过`Toolbar.LayoutParams`控制控件的位置

>An action menu. The menu of actions will pin to the end of the Toolbar offering a few frequent, important or typical actions along with an optional overflow menu for additional actions. Action buttons are vertically aligned within the Toolbar's minimum height, if set.

一个菜单

>In modern Android UIs developers should lean more on a visually distinct color scheme for toolbars than on their application icon. The use of application icon plus title as a standard layout is discouraged on API 21 devices and newer.
