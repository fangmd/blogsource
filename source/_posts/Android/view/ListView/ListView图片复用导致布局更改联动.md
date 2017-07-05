---
title: ListView 布局复用导致的问题
date: 2016-04-10 11:38:14
tags: ListView
categories: android

---

# ListView 布局复用导致的问题
>解决由于listview布局复用，我们在改变一个item中布局的时候另一个item的布局也改变了。

## 方案一 取消listview的布局复用
>不推荐使用

数据量少，且listview布局中没有图片的时候可以使用。

即不管convertView是否为null都创建新的对象。

<!--more-->

## 方案二 

listview点击的时候给adapter传入点击的位置`pos`,在adapter的

	public View getView(int position, View convertView, ViewGroup parent)

方法中初始化view的状态，判断`position`是否等于传入的`pos`如果相等就改变view的状态；

具体代码：

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        if (convertView == null) {
            convertView = LayoutInflater.from(mContext).inflate(R.layout.simple_play_list_item, parent, false);
            ViewHolder holder = new ViewHolder(convertView);
            convertView.setTag(holder);
        }
        ViewHolder holder = (ViewHolder) convertView.getTag();
        holder.mTextView.setText(mData.get(position).getTitle());

		// 保存textcolor的颜色到成员变量
        if (mTextDefaultColors == null) {
            mTextDefaultColors = holder.mTextView.getTextColors();
        }

		// 初始化所有都view属性
        holder.mTextView.setTextColor(mTextDefaultColors);
        holder.mView.setVisibility(View.INVISIBLE);

		// 如果点击位置和单前位置符合，将textview的颜色改为红色，显示指示view
        if (position == mCheckPos) {
            holder.mTextView.setTextColor(mContext.getResources().getColor(R.color.red));
            holder.mView.setVisibility(View.VISIBLE);
        }
        return convertView;
    }

    public void setPos(int pos) {
        mCheckPos = pos;
        notifyDataSetChanged();
    }

**效果图：**

![](http://i.imgur.com/i62wPiw.png)