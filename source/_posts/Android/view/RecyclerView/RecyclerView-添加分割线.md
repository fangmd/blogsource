---
title: RecyclerView - 添加分割线
date: 2016-04-17 18:38:14
tags: [RecyclerView]
categories: android

---


```java
public class SimpleItemDecoration extends RecyclerView.ItemDecoration {

    private Drawable mDivider;
    private int mLrOffset;

    public SimpleItemDecoration(Context context) {
        mDivider = context.getResources().getDrawable(R.drawable.line_divider);
    }

    public SimpleItemDecoration(Context context, int lrOffset) {
        mDivider = context.getResources().getDrawable(R.drawable.line_divider);
        mLrOffset = lrOffset;
    }

    @Override
    public void onDraw(Canvas c, RecyclerView parent, RecyclerView.State state) {
        super.onDraw(c, parent, state);
    }

    @Override
    public void onDrawOver(Canvas c, RecyclerView parent, RecyclerView.State state) {
        super.onDrawOver(c, parent, state);
        int left = parent.getPaddingLeft();
        int right = parent.getWidth() - parent.getPaddingRight();

        int childCount = parent.getChildCount();
        for (int i = 0; i < childCount; i++) {
            View child = parent.getChildAt(i);

            RecyclerView.LayoutParams params = (RecyclerView.LayoutParams) child.getLayoutParams();

            int top = child.getBottom() + params.bottomMargin;
            int bottom = top + mDivider.getIntrinsicHeight();

            mDivider.setBounds(left, top, right, bottom);
            mDivider.draw(c);
        }
    }

    @Override
    public void getItemOffsets(Rect outRect, View view, RecyclerView parent, RecyclerView.State state) {
        super.getItemOffsets(outRect, view, parent, state);
        int i = ScreenUtils.dp2px(view.getContext(), 1);
        int left = ScreenUtils.dp2px(view.getContext(), mLrOffset);
        outRect.set(left, i, left, 0);
    }
}
```

```java
mRecyclerView.addItemDecoration(new SimpleItemDecoration(getContext()));
```