---
title: RecyclerView悬浮条
date: 2016-11-27 09:38:14
tags: RecyclerView
categories: android

---


# 布局

	<FrameLayout
		android:layout_width="match_parent"
		android:layout_height="match_parent">


		<android.support.v7.widget.RecyclerView
			android:id="@+id/rv_float"
			android:layout_width="match_parent"
			android:layout_height="match_parent"
			app:layoutManager="LinearLayoutManager"/>


		<TextView
			android:id="@+id/tv_float"
			android:layout_width="match_parent"
			android:layout_height="60dp"
			android:background="#fff"
			android:gravity="center"
			android:text="head"
			android:textSize="20sp"/>


	</FrameLayout>

# Activity

	mRV.addOnScrollListener(new RecyclerView.OnScrollListener() {
		@Override
		public void onScrollStateChanged(RecyclerView recyclerView, int newState) {
			super.onScrollStateChanged(recyclerView, newState);

		}

		@Override
		public void onScrolled(RecyclerView recyclerView, int dx, int dy) {
			super.onScrolled(recyclerView, dx, dy);

			LinearLayoutManager layoutManager = (LinearLayoutManager) recyclerView.getLayoutManager();
			View view = layoutManager.findViewByPosition(mFloatItemCurrentPos + 1);

			if (view != null && view.getTop() <= mFloatItemHeight) {
				mTV.setY(-(mFloatItemHeight - view.getTop()));
			}else {
				mTV.setY(0);
			}

			if (mFloatItemCurrentPos != layoutManager.findFirstVisibleItemPosition()) {
				mFloatItemCurrentPos = layoutManager.findFirstVisibleItemPosition();
				mTV.setY(0);

				updateFloatItem();
			}
		}
	});

	private void updateFloatItem() {
		 String s = mStrings.get(mFloatItemCurrentPos);
		 mTV.setText(s);
	 }


参考：

- http://www.jianshu.com/p/fe69a53502ab[](http://www.jianshu.com/p/fe69a53502ab)
