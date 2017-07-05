---

title: Android机型适配--屏幕适配--适配方案

date: 2016-06-01 09:13:12

tags: [机型适配, 屏幕适配]

category: Android

---

# 屏幕尺寸的适配 4点

# 0 布局适配

## 布局元素自适应屏幕尺寸
- 使用`RelativeLayout`,禁用`AbsoluteLayout`

## 根据屏幕的配置加载不同的UI布局
>应用场景：需要为不同屏幕尺寸的设备设计不同的布局

做法：使用限定符

作用：通过配置限定符使程序在运行时根据当前设备的配置(屏幕尺寸)自动加载合适的布局资源

*限定符类型：*

### 尺寸限定符 ， 适配Android3.2之前的版本
需要在平板和手机上都适配的时候：

	res/layout-large/main.xml

系统在尺寸>7英寸时采用适配平板的布局；

*这种方式只适配Android3.2版本之前，不推荐使用*
### 最小宽度(Smallest-width)限定符 ，适合android3.2之后的版本
>在Android3.2之后，引入了最小宽度限定符

定义：通过指定某个最小的宽度(以dp为单位)来精确定位屏幕从而加载不同的UI资源

	sw XXXdp
small width，不区分方向，无论是宽度还是高度，只要大于等于xxxdp就采用这个布局

### 布局别名限定符
定义两个布局文件：

- `res/layout/main.xml`
- `res/layout/main_twopanes.xml`

1. `res/values-large/layout.xml`(Android3.2之前的双面板布局)

		<resources>
			<item name="main" type="layout">@layout/main_twopanes</item>
		</resources>

2. `res/values-sw500dp/layout.xml`(Android3.2及之后的双面板布局)

		<resources>
			<item name="main" type="layout">@layout/main_twopanes</item>
		</resources>

### 屏幕方向限定符 orientation
>有点复杂，看参考1

# 1 布局组件适配
做法：使用`wrap_content`,`match_parent`,`weight`来控制视图组件的宽度和高度

# 2 图片资源适配
做法：使用自动拉伸位图Nine-Patch的图片类型

# 3 用户界面流程适配

做法：进行用户界面流程的自适应配置：

1. 确定当前布局
2. 根据当前布局做出响应
3. 重复使用其他活动的片段
4. 处理屏幕配置变化

		View articleView = findViewById(R.id.article);
		mIsDualPane = srticleView != null && articleView.getVisibility() == View.VISIBILE;

上面的代码确定布局

后续操作的使用需要：加判断

		if(mIsDualPane){

		}else{

		}

# 屏幕密度匹配

# 0 布局控件匹配
让布局控件在不同屏幕密度上显示相同的像素效果；

做法：

1. 使用密度单位：dp，sp

dp解决了同一数值在不同分辨率中展示相同尺寸大小的问题，但是没有解决设备尺寸大小匹配问题；

*那么该如何解决控件的屏幕尺寸和屏幕密度的适配问题呢？*

从上面可知：
1. 因为屏幕密度不一样，所以不能使用px
2. 因为屏幕宽度不一样，所以小心使用dp


*百分比适配方法*

1. 以某个分辨率为基准，生成所有分辨率对应像素列表
2. 将生成像素列表存放在res目录下对应的values文件下
3. 根据UI设计师给的设计图上面的尺寸，找到对应像素的单位，然后设置给控件

## 1. 以某一个分辨率为基准，生成所有分辨率对应像素列表

现在我们以320x480的分辨率为基准：

- 将屏幕的宽度分为320份，取值为x1~x320
- 将屏幕的高度分为480份，取值为y1~y480


然后生成该分辨率对应像素数的列表，如下图：

- `lay_x.xml`宽：


		<?xml version="1.0" encoding="utf-8"?>
		<resources><dimen name="x1">1.0px</dimen>
			<dimen name="x2">2.0px</dimen
			....
			<dimen name="x320">320px</dimen>
		</resources>

- `lay_y.xml（高）`

		<?xml version="1.0" encoding="utf-8"?>
		<resources><dimen name="y1">1.0px</dimen>
		<dimen name="y2">2.0px</dimen>
		<dimen name="y3">3.0px</dimen>
		<dimen name="y4">4.0px</dimen>
		...
		<dimen name="y480">480px</dimen>
		</resources>

以上面的为基准补全其他分辨率，以1080*1920为例：
>基准为：320*480 所以：1080/320 = 3.375px，1920/480=4px，最后：

- `lay_x.xml`


		<?xml version="1.0" encoding="utf-8"?>
		<resources><dimen name="x1">3.375px</dimen>
		<dimen name="x2">6.65px</dimen>
		<dimen name="x3">10.125px</dimen>
		...
		<dimen name="x320">1080px</dimen>
		</resources>



- `lay_y.xml`

		<?xml version="1.0" encoding="utf-8"?>
		<resources><dimen name="y1">4px</dimen>
		<dimen name="y2">8px</dimen>
		<dimen name="y3">12px</dimen>
		<dimen name="y4">16px</dimen>
		...
		<dimen name="y480">1920px</dimen>
		</resources>

自动生成工具[https://github.com/hongyangAndroid/Android_Blog_Demos/tree/master/blogcodes/src/main/java/com/zhy/blogcodes/genvalues](https://github.com/hongyangAndroid/Android_Blog_Demos/tree/master/blogcodes/src/main/java/com/zhy/blogcodes/genvalues)

注：工具默认基准为400*320，当然对于特殊需求，通过命令行指定即可：

	java -jar 文件名.jar 基准宽 基准高 额外支持尺寸1的宽，额外支持尺寸1的高_额外支持尺寸2的宽,额外支持尺寸2的高：

例如：需要设置的基准是800x1280，额外支持尺寸：735x1152 ；3200x4500；

	java -jar 文件名.jar 800 1280 735，1152_3200,4500


## 把生成的各像素数列表放到对应的资源文件
将生成像素数列表（lay_x.xml和lay_y.xml）存放在res目录下对应的values文件（注意宽、高要对应），如下图：

![](http://upload-images.jianshu.io/upload_images/944365-c583e1356f18e329.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*注意：*必须在默认values里面也创建对应默认lay_x.xml和lay_y.xml文件，如下图
lay_x.xml，以dp为单位不同于上面的单位

	<?xml version="1.0" encoding="utf-8">
	<resources>
	<dimen name="x1">1.0dp</dimen>
	<dimen name="x2">2.0dp</dimen>
	...
	</resources>


*总结*
使用上述的适配方式，应该能进行90%的适配了，但其缺点还是很明显：

- 由于实际上还是使用px作为长度的度量单位，所以和google的要求使用dp作为度量单位会有所背离
- 必须尽可能多的包含所有分辨率，因为这个是使用这个方案的基础，如果有某个分辨率缺少，将无法完成该屏幕的适配
- 过多的分辨率像素描述xml文件会增加软件包的大小和维护的难度


# 图片资源的匹配

本质：使得图片资源在不同屏幕密度上显示相同的像素效果

做法：提供备用位图（符合屏幕尺寸的图片资源）

即一套分辨率=一套位图资源（这个当然是Ui设计师做了）



参考：
- [http://www.jianshu.com/p/ec5a1a30694b](http://www.jianshu.com/p/ec5a1a30694b)
- [http://www.jianshu.com/p/ad563d169871](http://www.jianshu.com/p/ad563d169871)
