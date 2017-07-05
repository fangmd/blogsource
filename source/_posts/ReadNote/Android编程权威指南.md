---
title: Android编程权威指南
date: 2016-03-25 21:18:12
tags: Android
categories: 读书笔记

---

# Android编程权威指南
>笔记，对第一行代码的补充


## 第一章 安卓应用初体验
1. 安卓应用属于事件驱动型。

	不同于命令行或脚本程序，事件驱动型应用启动后，即开始等待行为事件的发生。
2. 监听器

	是实现特点监听接口的对象，用来监听某类事件的发生。
3. 使用匿名内部类来实现监听器好处
	1. 代码阅读性好，一目了然。
	2. 减少一些命名类的使用

4. Android编译过程  P23

<!--more-->

## 第二章 Android与MVC设计模式

1. P28 getter，setter设置
	1. Windows-->Preferences --> java --> Code Style:m,s作为前缀

2. P30 应用的对象按模型、控制器和视图的类别被分为三部分
	- Model：存储应用的数据和业务逻辑。 通常被设计用来映射与应用相关的一些事物，模型对象不关心界面，唯一的目的就是存储和管理应用数据。
	- View：知道如何在屏幕上绘制自己以及如何响应用户的输入，凡是在屏幕上看到的都是视图对象
	- Controller：包含了应用的逻辑单元，是视图和模型对象的联系纽带。 它被设计用来响应由视图对象触发的各类事件，管理模型对象与视图层的数据流动。 控制器通常是Activity、Fragment、Service的一个子类

	应用的任何对象，归根结底都属于模型对象、视图对象以及控制对象中的一种。

	![](http://i.imgur.com/D8kBNyY.png)
3. 使用MVC设计模式的好处
	1. 有助于我们设计和理解应用
	2. 。。。。。。。

## 第三章 Activity的生命周期
1. P52  设备配置与备选资源
	1. 设备旋转会改变设备配置(device configuration),
	2. 设配配置：描述设备当前状态的一系列特征，包括：屏幕方向，屏幕密度，屏幕的尺寸，键盘类型，底座模式以及语言。。。
	3. 不同的设备配置，应用汇提供不同的备选资源
	4. 注：应用在运行中设备配置发生改变，Android就会销毁当前的activity，然后再新建一个activity。

2. 保存现场
	1. onSaveInstanceState(Bundle outState):该方法在onStop(),onDestory()方法之前由系统调用。onPause()无法判断在前还是后执行。

		源码上的解释：This method is called before an activity may be killed

## 第四章 Android应用的调试
### LogCat
1. 异常与栈跟踪
	1. 异常报告：首先告诉我们最高层级的异常及其栈追踪，然后是导致该异常的异常及其栈追踪。 不断追溯
	2. 最后一个没有原因的异常使我们要关注的目标。


2. 记录栈跟踪日志
	1. log方法打印

3. 设置断点
	1. 手动：需要自己设置断点，判断程序可能有问题的地方
	2. Run-->Add Java Exception Breakpoint菜单：当无法设置合适的断点时，使用这个方法，会在异常出现的时候停止，Suspend on caught exceptions , Subclasses of this exception

### Android Lint
>安卓特有的调试工具：可以调试android应用资源问题

1. 静态分析器(static analyzer)
	1. Android Tools--> Run Lint:Check for Common Errors


2. 操作R类问题
	1. 运行Android Lint：Window-->Run Android Lint菜单项
	2. 清理项目：Project --> Clean菜单项，Eclipse会重新编译整个项目，消除错误
	3. 重新检查资源文件中xml文件的有效性
	4. 删除gen目录：Eclipse重新编译的时候会创建新的gen目录


### Eclipse内置的代码调试器
1. DDMS(Dalvik Debug Monitor Service)应用透视图
	1. 透视图：Eclipse中预先定义的一组视图


## 第五章 第二个activity


## 第六章 Android SDK版本与兼容

1. SDK版本
	- 17 Jelly Bean 4.2
	- 16 Jelly Bean 4.1

2. manifest
	- SDK最低版本
	- SDK目标版本：降低目标版本可以保证即便是在高于目标版本的设备上，应用任然可以正常运行，且运行行为和目标版本保持一致。

3. SDK编译版本不会出现在manifest中，这是程序员和编译期之间的事，
4. 安全添加新版本API中的代码

	Android Lint已经改进，当新版本的API代码在老版本系统上运行时会在运行时就捕获。
	@TargetApi(11):可以压制警报，同时需要代码处理：Builder.VERSION.SDK_INT>=Build.VERSION_CODES.HONEYCOMB

5. 使用Android开发者文档
	1. 设计部分：UI设计的模式和原则
	2. 开发：SDK文档和培训资料
	3. 发布：如何发布应用

	开发部分可细分为：
	- Android培训
	- API使用指南
	- 参考文档
	- 开发工具

## 第七章 UI fragment与fragment管理器

### fragment引入
>采用fragment而不是activity进行应用的UI管理，可绕开Android系统activity规则的限制。

**fragment**:是一种控制器对象，activity可委派它完成一些任务。

支持库：

- libs/android-support-v4.jar
- android.support.v4.app.Fragment-Activity

activity知道如何管理fragment，所以fragment的使用需要activity的支持。
### 托管UI fragment
- 在布局中为fragment的视图安排位置
- 管理fragment实例的生命周期

#### fragment生命周期：

![](http://i.imgur.com/nw0mf1Q.png)

fragment代表activity在工作，它的状态反应了activity的状态，fragment需要对应的生命周期来处理activity的工作。

fragment生命周期和activity生命周期的区别：fragment的生命周期方法是由托管activity而不是操作系统调用的，操作系统无从知晓activity用来管理视图的fragment，fragment的使用是activity自己内部的事情。

#### 托管的两种方式
- 添加fragment到activity布局中

	简单但是不够灵活，将fragment及其视图与activity的视图绑定在一起，且在activity的生命周期过程中无法切换fragment视图。
- 在activity代码中添加fragment

	复杂的托管方式，但也是唯一一种可以在运行时控制fragment的方式，
	- 可以决定何时将fragment添加到activity中以及随后可以完成何种具体任务
	- 移除fragment，用其他fragment替换当前的fragment，然后再添加删除的fragment

### 创建UI fragment
1. 定义CrimeFragment的布局
2. 创建CrimeFragment类

	`Fragment.onCreate(Bundle)`的方法时公共方法，但是`Activity.onCreate(Bundle)`是保护方法：因为fragment需要被托管的任何activity调用。

	注意:在`Fragment.onCreate(Bundle)`方法中并没有生成fragment的视图，需要：`Fragment.onCreateView(LayoutInflater inflater,ViewGroup parent,Bundle saveInstanceState)`

	`inflater.inflate(R.layout.fragment_crime, container, false);`参数1表示布局资源id，参数2表示视图的父视图，需要父视图正确配置组件。参数3告诉布局生成器是否将生成的视图添加给父视图

### 添加UIfragment到FragmentManager


## 第八章 使用护具与组件创建用户界面
### 升级Crime类
### 更新布局
### 生成并使用组件
### 升入探讨XML布局属性
1. 样式、主题和主题属性
	- style：样式，可以创建自己的样式文件
	- 主题属性引用：

2. dp、sp
3. android开发设计原则
	- 官网查看

4. 布局参数

	以layout开头
5. 边距与内边距
6. 使用图形布局工具

## 第九章 使用ListFragment显示列表

### 更新 CriminalIntent 应用的模型层

1. 单例与数据集中存储

	创建一个单例设计类：CrimeLab，有一个Context属性，构造函数需要传入Context，**在构造方法中传入Context好处：**这个类就可以完成启动activity，获取项目资源，查找应用的私有存储空间等任务

	c.getApplicationContext()获得应用层的context，application context是针对应用的全局Context，任何时候只要是应用层面的单例，就应该使用application context。

### 创建ListFragment
1. 有getActivity()方法，不仅可以返回托管activity，且允许fragment处理更多activity相关事务
2. ListFragment会有默认的布局，所以onCreateView方法不是必须要覆盖的

### 使用抽象activity托管fragment
1. 通用的fragment托管布局
2. 抽象activity类

### ListFragment/ListView/ArrayAdapter

1. adapter:
	1. 创建必要的视图对象
	2. 用模型层数据填充视图对象
	3. 将准备好的视图对象返回给ListView

2. 创建ArrayAdapter<T> 实例
	1. 默认的ArrayAdapter<T>.getView(..)方法依赖toString()方法

3. 响应列表项的点击事件
	1. onListItemClick(ListView,View,int,long)

### 定制列表项
1. 创建列表项布局
2. 创建adapter子类

## 使用fragment argument
### 从fragment中启动activity
1. Fragement.startActivity(Intent)
2. 附加extra信息
3. 获取extra信息
4. 使用Crime数据更新CrimeFragment视图
5. 直接获取extra信息方式的缺点
	1. 牺牲了fragment的封装性，CrimeFragment不再是可复用的构建单元，总是需要由某个具体activity托管着，而且activity的Intent定义名为LEXTRA_CRIME_ID的extra

### fragment argument
1. 每一个fragment实例都附带一个Bundle对象，一个key-value对就是一个argument
	1. 创建一个Bundle对象
	2. 加数据

2. 附加argument给fragment：Fragment.setArguments(Bundle)，注意这个方法必须在fragment创建后，添加给activity前完成。
	1. 习惯做法：添加名为newInstance()的静态方法给Fragment类，使用这个方法完成fragment实例及bundle对象的创建，然后将argument放入bundle，附加给fragment

3. 获取argument
4.
	1. fragment获取argument的时候会调用getArgument()方法，在调用Bundle的get方法

### 重新加载显示列表项
1. 点击列表后修改对应的Crime明细信息，并将修改后的数据保存到模型层
2. 为什么在onResume()方法中刷新列表显示，而不是onStart()方法呢？
	1. 因为如果activity没有被停止的时候修改了数据，当activity回来的时候不会执行onStart()，但是onResume()一定会执行，


### 通过fragment获取返回结果
1. 使用Fragment.startActivityForResult(..)方法
2. Fragment.onActivityResult(..)方法

详细看：第20章

## 第11章 使用ViewPager
![](http://7xr8nu.com1.z0.glb.clouddn.com/AndroidAuthority1.png)


## 创建CrimePagerActivity
1. 以代码的方式定义并产生布局
	- 为ViewPager创建资源ID
	- 创建ViewPager实例并赋值mViewPager
	- 赋值资源ID给ViewPager，并对其进行配置
	- 设置ViewPager为activity的内容视图

2. 创建独立资源ID
	1. 在values目录下ids.xml

3. ViewPager与PagerAdapter
	1. PagerAdapter的子类FragmentStatePagerAdapter
	2. FragmentStatePagerAdapter是一个匿名实例，是我们的代理，负责管理与ViewPager的对话并协同工作：将返回的fragment添加给托管activity，并帮助Viewpager找到fragment的视图一一对应。

4. 整合配置并使用CrimePagerActivity
5. FragmentStatePagerAdapter与FragmentPagerAdapter
	1. 区别：在卸载不需要的fragment是采用的处理方法
		1. FragmentStatePagerAdapter会销毁掉不需要的fragment，可将fragment从activity的FragmentManager中彻底删除，可以保存状态：onSaveInstanceState(Bundle)
		2. FragmentPagerAdapter，对于不需要的fragment使用detach(Fragment)方法处理，只是销毁了fragment的视图。

	![](http://7xr8nu.com1.z0.glb.clouddn.com/AndroidAuthority2.png)
	![](http://7xr8nu.com1.z0.glb.clouddn.com/AndroidAuthority3.png)

### 深入学习ViewPager的工作原理
----需要重新看

## 第12章 对话框
>可以引起用户注意也可以接受用户的输入，在提示中要信息或提供用户选项方面非常有用。

做法：将AlertDialog视图封装在DialogFragment实例中，不使用DialogFragment(建议使用，使用FragmentManager管理对话框，可以更多配置选项来显示对话框)

如果设备旋转，独立配置的AlertDialog会消失，而配置封装在fragment中的AlertDialog并不会有这个问题

![](http://i.imgur.com/NLqI14j.png)

1. 创建DataPickeFragment类
2. 创建AlertDialog
3. 通过FragmentManager在屏幕上显示对话框

### 创建DialogFragment
1. 创建DataPickerFragment extends DialogFragment
2. 显示DialogFragment
	1. 托管activity的FragmentManager管理，将DialogFragment添加给FragmentManager管理并放置到屏幕上，可调用fragment实例的方法：
		1. public void show(FragmentManager manager , String tag)
		2. public void show(FragmentTransaction transaction, String tag)

	2. String 是唯一识别放在FragmentManager列队中的DialogFragment，

3. 设置对话框显示的内容


P210


  **由于兼容问题**
