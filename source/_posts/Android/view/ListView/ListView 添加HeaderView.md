---
title: ListView 添加HeaderView
date: 2016-04-17 11:38:14
tags: ListView
categories: android

---


# ListView 添加HeaderView


## 代码：


	ViewPager viewPager = new ViewPager(getActivity());
    ArrayList<FocusImageAlbumInfo> data = new ArrayList<>();
    mFocesImgAdapter = new TopAdPageAdapter(getActivity(), data);
    viewPager.setAdapter(mFocesImgAdapter);
    
    DisplayMetrics displayMetrics = getResources().getDisplayMetrics();
    int density = displayMetrics.density;
    int px = 220 * density;
    viewPager.setLayoutParams(new AbsListView.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, px));
    mListView.addHeaderView(viewPager);

## viewpager adapter代码

    @Override
    public Object instantiateItem(ViewGroup container, int position) {
        ImageView imageView = new ImageView(mContext);
        imageView.setLayoutParams(new ViewGroup.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.MATCH_PARENT));
        container.addView(imageView);
        initImageData();
        Picasso.with(mContext).load(mData.get(position).getPic()).into(imageView);
        return imageView;
    }