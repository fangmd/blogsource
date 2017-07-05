---
title: ShareSDK 使用记录
date: 2016-04-18 12:18:12
tags: ShareSDK
category: android

---

# ShareSDK 使用记录

## 0 集成SDK

参考[http://wiki.mob.com/android_%E5%BF%AB%E9%80%9F%E9%9B%86%E6%88%90%E6%8C%87%E5%8D%97/](http://wiki.mob.com/android_%E5%BF%AB%E9%80%9F%E9%9B%86
%E6%88%90%E6%8C%87%E5%8D%97/)

快速集成，这里不集成快捷分享内容。


## 分享模块

	private void showShare() {
	 ShareSDK.initSDK(this);
	 OnekeyShare oks = new OnekeyShare();
	 //关闭sso授权
	 oks.disableSSOWhenAuthorize(); 

	// 分享时Notification的图标和文字  2.5.9以后的版本不调用此方法
	 //oks.setNotification(R.drawable.ic_launcher, getString(R.string.app_name));
	 // title标题，印象笔记、邮箱、信息、微信、人人网和QQ空间使用
	 oks.setTitle(getString(R.string.share));
	 // titleUrl是标题的网络链接，仅在人人网和QQ空间使用
	 oks.setTitleUrl("http://sharesdk.cn");
	 // text是分享文本，所有平台都需要这个字段
	 oks.setText("我是分享文本");
	 // imagePath是图片的本地路径，Linked-In以外的平台都支持此参数
	 //oks.setImagePath("/sdcard/test.jpg");//确保SDcard下面存在此张图片
	 // url仅在微信（包括好友和朋友圈）中使用
	 oks.setUrl("http://sharesdk.cn");
	 // comment是我对这条分享的评论，仅在人人网和QQ空间使用
	 oks.setComment("我是测试评论文本");
	 // site是分享此内容的网站名称，仅在QQ空间使用
	 oks.setSite(getString(R.string.app_name));
	 // siteUrl是分享此内容的网站地址，仅在QQ空间使用
	 oks.setSiteUrl("http://sharesdk.cn");

	// 启动分享GUI
	 oks.show(this);
	 }


基本使用：

1. 获取相应的ShareParams对象

		QQ.ShareParams sp = new QQ.ShareParams();

2. 向`ShareParams`存入需要分享的数据，可分享数据参考上面的`oks`的注解

		sp.setTitle("测试title");
        sp.setTitleUrl("http://sharesdk.cn"); // 标题超级链接
        sp.setText("测试分享文本");
        sp.setImageUrl("http://f1.sharesdk.cn/imgs/2014/05/21/oESpJ78_533x800.jpg"); // 分享网络图片

3. 获得相应平台对象并分享`ShareParams`

		Platform qq = ShareSDK.getPlatform(QQ.NAME);
        qq.setPlatformActionListener(activity);
        qq.share(sp);



