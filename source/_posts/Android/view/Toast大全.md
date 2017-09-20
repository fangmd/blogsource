---
title: Toast
date: 2016-04-02 12:18:12
tags: Toast
category: android

---


## 默认Toast

	Toast.makeText(getApplicationContext(), "默认Toast样式",Toast.LENGTH_SHORT).show();

<!--more-->

## 自定义显示位置

	toast = Toast.makeText(getApplicationContext(),"自定义位置Toast", Toast.LENGTH_LONG);
	toast.setGravity(Gravity.CENTER, 0, 0);
	toast.show();

## 带图片效果

	toast = Toast.makeText(getApplicationContext(),"带图片的Toast",Toast.LENGTH_LONG);
	toast.setGravity(Gravity.CENTER, 0, 0);
	LinearLayout toastView = (LinearLayout) toast.getView();
	ImageView imageCodeProject = new ImageView(getApplicationContext());
	imageCodeProject.setImageResource(R.drawable.icon);
	toastView.addView(imageCodeProject, 0);
	toast.show();

## 完全自定义

	LayoutInflater inflater = getLayoutInflater();
	View layout = inflater.inflate(R.layout.custom,(ViewGroup) findViewById(R.id.llToast));
	ImageView image = (ImageView) layout.findViewById(R.id.tvImageToast);
	image.setImageResource(R.drawable.icon);
	TextView title = (TextView) layout.findViewById(R.id.tvTitleToast);
	title.setText("Attention");
	TextView text = (TextView) layout.findViewById(R.id.tvTextToast);
	text.setText("完全自定义Toast");
	toast = new Toast(getApplicationContext());
	toast.setGravity(Gravity.RIGHT | Gravity.TOP, 12, 40);
	toast.setDuration(Toast.LENGTH_LONG);
	toast.setView(layout);
	toast.show();


参考：

- [https://developer.android.com/guide/topics/ui/notifiers/toasts.html](https://developer.android.com/guide/topics/ui/notifiers/toasts.html)
- [http://www.cnblogs.com/salam/archive/2010/11/10/1873654.html](http://www.cnblogs.com/salam/archive/2010/11/10/1873654.html)
