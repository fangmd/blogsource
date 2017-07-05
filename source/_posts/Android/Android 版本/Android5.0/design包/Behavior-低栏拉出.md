---
title: CoordinatorLayout Behavior - 底栏拉出
date: 2016-07-11 14:38:14
tags: [CoordinatorLayout, behavior]
categories: android

---

# 布局

1. 根布局为`CoordinatorLayout`
2. 正常布局占满整个屏幕
3. (可选) 添加一个底布局全部拉出的时候显示的头布局, 相关属性:

	    <RelativeLayout
		android:id="@+id/design_bottom_sheet_bar"
		android:layout_width="match_parent"
		android:layout_height="?attr/actionBarSize"
		android:background="@color/colorAccent"
		app:layout_anchor="@+id/design_bottom_sheet"
		app:layout_anchorGravity="top"
		android:layout_gravity="bottom"
		android:visibility="gone">

		<TextView
		    android:layout_width="wrap_content"
		    android:layout_height="wrap_content"
		    android:text="点击收起BottomSheet"
		    android:textColor="#ffffff"
		    android:layout_centerInParent="true"/>

	    </RelativeLayout>

4. 可拉出的底布布局: 下面的`ImageView`为占位控件可替换成正常的布局控件,

	    <RelativeLayout
		android:id="@+id/design_bottom_sheet"
		android:layout_width="match_parent"
		android:layout_height="match_parent"
		android:minHeight="100dp"
		app:behavior_peekHeight="56dp"
		app:behavior_hideable="false"
		app:layout_behavior="@string/bottom_sheet_behavior"
		android:background="#ffffff"
	    >
		<TextView
		    android:id="@+id/bottom_sheet_tv"
		    android:layout_width="wrap_content"
		    android:layout_height="56dp"
		    android:layout_centerHorizontal="true"
		    android:gravity="center"
		    android:text="这是一个BottomSheet"/>
		<ImageView
		    android:id="@+id/bottom_sheet_iv"
		    android:layout_width="wrap_content"
		    android:layout_height="match_parent"
		    android:layout_centerInParent="true"
		    android:padding="10dp"
		    android:minHeight="100dp"
		    android:adjustViewBounds="true"
		    android:scaleType="centerInside"
		    android:layout_gravity="center"/>
	    </RelativeLayout>

5. 活动代码:

	    private void initBehavior() {
		mBehavior = BottomSheetBehavior.from(mDesignBottomSheet);
		mBehavior.setBottomSheetCallback(new BottomSheetBehavior.BottomSheetCallback() {
		    @Override
		    public void onStateChanged(@NonNull View bottomSheet, int newState) {
				// if(newState!=BottomSheetBehavior.STATE_COLLAPSED&&bottom_sheet_tv.getVisibility()==View.VISIBLE){
				//  bottom_sheet_tv.setVisibility(View.GONE);	
				//bottom_sheet_iv.setVisibility(View.VISIBLE);
				//  }else if(newState==BottomSheetBehavior.STATE_COLLAPSED&&bottom_sheet_tv.getVisibility()==View.GONE){
				// bottom_sheet_tv.setVisibility(View.VISIBLE);
				//bottom_sheet_iv.setVisibility(View.GONE);
				//}

		        if (newState == BottomSheetBehavior.STATE_COLLAPSED) {
		            mDesignBottomSheetBar.setVisibility(View.GONE);
		        } else if (newState == BottomSheetBehavior.STATE_EXPANDED) {
		            mDesignBottomSheetBar.setVisibility(View.VISIBLE);
		        }
		    }

		    /**
		     * 滑动过程中
		     * @param bottomSheet
		     * @param slideOffset
		     */
		    @Override
		    public void onSlide(@NonNull View bottomSheet, float slideOffset) {

		        if (bottomSheet.getTop() < 2 * mDesignBottomSheetBar.getHeight()) {
		            mDesignBottomSheetBar.setVisibility(View.VISIBLE);
		            mDesignBottomSheetBar.setAlpha(slideOffset);
		            mDesignBottomSheetBar.setTranslationY(bottomSheet.getTop() - 2 * mDesignBottomSheetBar.getHeight());
		        } else {
		            mDesignBottomSheetBar.setVisibility(View.INVISIBLE);
		        }
		    }
		});
	    }

6. 修改底部拉出布局的高度:

	    /**
	     * 修改底部拉出布局的高度,流出标题栏的位置
	     *
	     * @param hasFocus
	     */
	    @Override
	    public void onWindowFocusChanged(boolean hasFocus) {
		super.onWindowFocusChanged(hasFocus);
		//修改SetBottomSheet的高度 留出顶部工具栏的位置

		// 下面的代码可以调整拉出的高度,也可以直接在布局中写死高度
		if (!isSetBottomSheetHeight) {
		    CoordinatorLayout.LayoutParams linearParams = (CoordinatorLayout.LayoutParams) mDesignBottomSheet.getLayoutParams();
		    linearParams.height = mBaiduMapCoordinatorLayout.getHeight() - mDesignBottomSheetBar.getHeight();
		    mDesignBottomSheet.setLayoutParams(linearParams);
		    isSetBottomSheetHeight = true;//设置标记 只执行一次
		}
	    }

7. 点击折叠布局:

	    @OnClick(R.id.design_bottom_sheet_bar)
	    public void onClick() {
		mBehavior.setState(BottomSheetBehavior.STATE_COLLAPSED);
	    }




