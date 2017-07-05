---
title: ListView 多布局使用
date: 2016-04-06 17:38:14
tags: ListView
categories: android

---

# ListView 多布局使用

<!--more-->

## 几个必须重写的方法

表示listview中可能有的布局种类：

	@Override
    public int getViewTypeCount() {
        // 返回支持的布局类型的最大数量
        return 3;
    }

判断布局的类型：

    @Override
    public int getItemViewType(int position) {
        // 当item需要创建或者复用的时候listview会调用这个方法：检查缓冲区中是否有相同类型的view，有就复用，没有就创建新的
        // listview 内部采用类型数值作为缓存区的数组下标
        DiscoveryRecommendItem item = mItems.get(position);
        if (item instanceof RecommendAlbums) {
            return 0;
        } else if (item instanceof SpecialAlbums) {
            return 1;
        } else if (item instanceof DiscoveryAlbums) {
            return 2;
        }
        return super.getItemViewType(position);
    }

在创建item项的时候，根据不同类型的布局使用不同的创建item项方法：

	@Override
    public View getView(int position, View convertView, ViewGroup parent) {

        if (getItemViewType(position) == 0) {
            // 小编推荐内容
            return bindRecommendAlbums(position, convertView, parent);
        } else if (getItemViewType(position) == 1) {
            return bindSpecialAlbums(position, convertView, parent);
        } else if (getItemViewType(position) == 2) {
            return bindDiscoveryAlbums(position, convertView, parent);
        }
        return null;
    }

	private View bindDiscoveryAlbums(int position, View convertView, ViewGroup parent) {。。。。} // 里面的写法和原来的一样，有几种布局就需要写几个这样的方法

	// 同样的不同的布局也要使用不同的ViewHolder


