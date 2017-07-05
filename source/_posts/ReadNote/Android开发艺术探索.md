---
title: Android 开发艺术探秘 
date: 2016-07-17 13:18:12
tags: [Android开发艺术探秘,  读书笔记]
category: android

---

# Activity 的生命周期和启动模式

Activity 生命周期分为两个部分:

1. 典型情况:正常使用过程中Activity的生命周期的改变.
2. 异常情况:Activity被系统回收,设备的Configuration发生改变.

## 典型情况的Activity生命周期
七个生命周期

1. `onCreate()`:布局初始化,数据加载
2. `onRestart()`:在`onStop()`-->`onStart()`
3. `onStart()`:activity还在后台
4. `onResume()`:activity出现在前台,可以和用户进行交互
5. `onPause()`:activity在前台, 不宜进行大量的耗时操作
6. `onStop()`:activity回到后台
7. `onDestroy()`:activity被销毁

<!--more-->

注意:

1. 如果`Activity`使用了透明主题,那么,在`Activity`回到后台的时候`onStop()`不会被调用.
2. 在`Activity A`跳转到`ActivityB`的时候:

	- Activity A's onPause() method executes.
	- Activity B's onCreate(), onStart(), and onResume() methods execute in sequence. (Activity B now has user focus.)
	- Then, if Activity A is no longer visible on screen, its onStop() method executes.

	所以:如果在A中做数据处理,B中马上要使用到的话,就需要在A中的`onPause()`保存数据,这样B就能获取到最新的数据.

## 异常情况的生命周期

### 资源相关的系统配置发生改变导致Activity被杀死重新创建
Android中系统配置改变的时候,Android会根据系统配置加载不同的资源,所以系统配置改变的时候需要重新创建`Activity`;

使用到的两个关键方法:

1. `onSaveInstanceState()`:调用时机在`onStop()`之前
2. `onRestoreInstanceState()`or`onCreate()`:这两个方法都可以用来恢复现场

***如何保证Activity不受系统配置影响:***

可以在`Activity`对应的清单文件标签中添加属性:

	android:configChanges="orientation|screenSize"

这样在屏幕旋转的时候就不会执行销毁Activity,会执行`onConfigurationChanged`方法

## 资源内存不足导致低优先级的Activity被杀死

Activity优先级有高到低:

1. 前台可见,和用户正常交互
2. 可见,非前台,处在`onPause()`
3. 后台Activity,`onStop()`

在`Activity`被销毁的时候会执行数据保存方法:`onSaveInstanceState()`


## Activity 启动模式
Android 中使用任务栈来管理`Activity`

- standard: 谁启动这个`Activity`,它就会在启动它的`Context`所处的栈中创建实例,*注意:*当我们使用`ApplicationContext`去启动`Activity`的时候会报错,因为`ApplicationContext`没有任务栈,这时候就需要`FLAG_ACTIVITY_NEW_TASK`(非`Activity`类型的`Context`没有所谓的任务栈).
- singleTop:onNewIntent
- singleTask:栈内复用,onNewIntent, *注意:*`Activity`会在自己想要的任务栈(此`Activity`也只会在这个任务栈中出现)内实现复用
- singleInstance:单实例模式,singleTask的加强版,单独占有一个任务栈,并且只有一个`Activity`实例

任务栈: 1. 前台任务栈 2. 后台任务栈(`Activity`处于暂停状态)

### TaskAffinity

通过给`Activity`设置这个参数来设定`Activity`所在的任务栈,`Activity`默认的任务栈名是包名.

使用:

1. 和`singleTask`配对使用:让`Acitivity`运行在指定的任务栈中.
2. 和`allowTaskReparenting`属性配对使用

### 给Activity指定启动模式

1. 清单文件设置

		android:launchMode = "..."

2. 代码设置:

		intent.addFrags(...)

上面两中方式可以同时使用,第二种方法的优先级高.

上面两中方法的可设置范围不同:第一种方法无法设置`FLAG_ACTIVITY_CLEAR_TOP`; 第二种方法无法设置`singleInstance`

### 一个命令

	adb shell dumpsys activity

### Activity Flags
标记位

作用:

1. 启动模式
2. 影响`Activity`运行状态

`FLAG_ACTIVITY_SINGLE_TOP`:在这个`Activity`的任务栈中在它前面的`Activity`都会出栈,singleTask

`FLAG_ACTIVITY_EXCLUDE_FROM_RECENTS`:和`android:excludeFromRecents="true"`一样的效果, 适用于应用的扉页

## IntentFilter 的匹配规则
启动Android四大组件的方式: 1. 显示启动; 2. 隐式启动.

隐式启动的时候`Intent`回去匹配目标组件定义的`IntentFliter`中所设置的过滤信息,匹配成功就会启动.

过滤信息:

1. action
2. category
3. data

***注意:***1. 一个过滤列表中的action,category,data可以有多个. 2. 只有一个Intent同时匹配action,category,data才算完全匹配,完全匹配才能启动目标组件.3. 一个`Activity`可以同时设置多个`intent-filter`

### action
是字符串,区分大小写(category不区分大小写)

一个过滤规则中可以有多个`action`,`Intent`只需要匹配其中的一个`action`就可以.

### category
匹配机制: 要求如果`Intent`中如果设置了`category`那么设置的`category`必须和过滤规则中的其中一个`category`相同.

### data
由两部分组成: 1. mimeType 2. URI

uri结构:

	<scheme>://<host>:<port>/[<path>|<pathPrefix>|<pathPattern>]

- scheme: url模式
- host:主机名
- port:端口号
- path,pathPattern,pathPrefix:路径信息


匹配其中一组data即可

过滤规则:

1. 如果`intent-filter`中设置了`mimeType`,没有设置`url`:那么uri默认为:content或者file;

		intent.setDataAndType(Uri.parse("file://abc", "image/png"));

	***注意:***必须使用`setDataAndType()`方法来同时设置type和uri,如果分别调用`setData`,`setType`会互相消除设置(查看源码可知)


判断隐身启动是否能成功: 

1. `PackageManager`或者`Intent`的`resolveActivity`方法
2. `PackageManager`:`queryIntentActivities`

		public abstract List<ResolveInfo> queryIntentActivities(Intent intent, int flags);
		public abstract ResolveInfo resolveActivity(Intent intent, int flags);

	参数二:`MATCH_DEFAULT_ONLY`:不包含`DEFAULT`的category的Activity不支持隐式启动.


# IPC 进程
IPC：Inter-Process Communication 进程间通讯

## 进程与线程
线程：CPU调度最小单元

进程：一个执行单元，在PC或者移动设备中就是一个程序或者应用， 一个进程中包含对个线程

ANR：Application Not Responding 应用无响应，Android中的UI线程阻塞导致

## Android中的多进程模式
开启多进程模式：给四大组件指定`android:process`属性

### 开启多进程模式
这里探讨：一个应用内的多进程的情况。

开启多进程方式：

1. 设置属性：`android:process="...进程名"`
2. 通过jni在native层fork一个新的进程（非常规）

在Android应用开启的时候系统自动分配的进程名就是应用的包名。

进程名自定义命名的方式：

1. `:`+名称：这种命名方式，系统默认在`：`前面加上当前应用的包名，属于应用的私有进程。例如：`android:process=":remote`
2. 普通命名：这种命名方式创建的线程属于全局进程，其他的应用可以通过ShareUID方式和它跑在同一个进程中。例如：`android:process="com.ryg.chapter_2.remote"`

Android系统会给每一个应用分配唯一的UID，具有相同UID的应用才能共享数据。

两个应用通过ShareUID跑在同一进程需要：

1. 相同的ShareUID
2. 签名相同

### 多进程模式的运行机制
Android为每个进程分配独立的虚拟机，不同的虚拟机在内存分配上有不同的地址空间。

当两个进程访问同一个类的时候，会创建多个副本。

多进程可能导致的问题：

1. 静态成员和单例模式完全失效：多个副本，内存分配区域不同
2. 线程同步机制完全失效
3. SharedPreferences的可靠性下降：可能会并发读写
4. Application会多次创建：开启进程相当与开启两个应用

## IPC基础概念介绍

### Serializable 接口
Java所提供的序列化接口，是一个空接口，为对象提供标准的序列化和反序列化操作。

使用：实现接口并定义标识（非必须）即可。

实现这个接口的对象就可以固话到本地了。（通过`ObjectOutputStream`和`ObjectInputStream`）

`serialVersionUID`作用：保证序列化的类版本和当前类的版本相同，防止类被更改了。

### Parcelable 接口

Android中推出的序列化方法。

效率高，主要用在内存序列化上。

### Binder
是Android中的一个类，实现了IBinder的接口。

是Android中跨进程通信的一种方式。

从Android Framework角度来说，是ServiceManager连接各种Manager（ActivityManager，WindowManager等）和相应的ManagerService的桥梁。

从Android应用层来说：Binder是客户端和服务端通讯的媒介，bindService的时候，会获得一个Binder对象，通过这个对象获得服务或者数据。

.....

## Android 中的 IPC 方式

### 使用Bundle
使用Bundle在Intent中传递数据. Bundle实现了序列化, 所以在使用Intent启动其他进程的四大组件的时候可以传递数据.

### 使用文件共享

两个进程读写同一个文件.

***注意:***避免并发读写,在数据同步要求不高的进程通讯使用. `SharedPreferences`是Android提供的轻量级存储方案,系统对它的读写有一定的缓存策略所以不适和在进程间通讯.

### 使用 Messenger
通过Messenger传递 Message对象

底层实现是AIDL

#### Messenger 的两中构造方法 (重要)

	public Messenger(Handler target){
		mTarger = target.getImessager();	
	}

	public Messenger(IBinder target){
		mTarget = IMessenger.Stub.asInterface(target);
	}

#### 服务端:

1. 创建一个`Service`并在清单文件中设置属性`android:process=":remote"`让其在进程中执行
2. 创建一个`Handler`的继承类`MessengerHandler`,作为进程通讯的信息处理类
3. 在`Service`中创建`Messenger`对象`mMessenger`构造参数为`new MessengerHandler()`
4. 在`Service`的`onBind()`方法中返回`mMessenger.getBinder()`

服务端给客户端发消息:上面实现了客户端-->服务端的通讯在上面的基础上:

1. 在`MessengerHandler`类中的`handleMessage()`方法中 ,在接收到客户端信息的`Message`对象通过:`Messenger client = msg.replyTo;` 方法获得从 服务端-->客户端的`Messenger` ***注意***:这里的replyTo对象需要在客户端设置
2. 通过`Messenger` client 发送`Message`对象

#### 客户端

1. `bindService()`:绑定服务端的`Service`,在Serice`ServiceConnection`接口中通过`Ibinder`创建`Messenger`对象,(`mMessenger = new Messenger(service)`);
2. 通过`mMessenger`发送的`Message`就能到达在服务端定义的`MessengerHandler`中

接受从服务端发来的消息:

1. 创建相应的`Handler`继承类: `MessengerHandler`
2. 通过`Handler`继承类创建`Messenger`:`private Messenger mGetReplyMessenger = new Messenger(new MessengerHandler())`
3. 设置`replyTo`对象:`msg = mGetReplyMessenger;`这样在服务端就能获得到从 服务端-->客户端的`Messenger`

注:

1. 客户端-->服务端通讯:需要在客户端获取 服务端创建的`Messenger`且用来创建`Messenger`对象的`Handler`在服务端: 传递方式是`bindService()`
2. 服务端-->客户端通讯:需要在服务端获取 客户端创建的`Messenger`且用来创建`Messenger`对象的`Handler`在客户端: 传递方式是`msg.replyTo`


### 使用 AIDL

Messenger是以串行的方式处理客户端发来的消息,如果大量的消息同时发送到服务器,服务端只能一个一个处理,不适合并发请求.

Messenger的主要功能是传递消息.

如果需要跨进程调用方法就需要AIDL.

#### 服务端

1. 创建一个`Service`监听客户端的连接请求
2. 创建AIDL文件,暴露接口给客户端:使用Android studio 自动生成AIDL文件`IBookManager`接口,在这里定义需要的方法, 
3. 在正常工程中创建接口的实现`BookBinder extends IBookManager.Stub`,实现里面的方法
4. 在`Service`的`onBind()`方法中返回3中创建的类的对象`mBookBinder`


#### 客户端

1. 绑定服务端的`Service`
2. 将`Binder`对象转成`AIDL`接口所属的类型(`mIBookManager = IBookManager.Stub.asInterface(service);`),然后就可以调用里面的方法了

#### 传递自定义的对象

1. 在服务端aidl包中创建`Book.java`和`Book.aidl`

	//  Book.aidl
	parcelable Book;

	// Book.java
	// 和普通类的代码一样,不过需要实现parcelable接口

2. 在aidl接口文件中导入`Book`

#### 观察者模式监听服务器变化
P77-P90

### 使用ContentProvider
和Messenger一样,底层实现是Binder

### 使用Socket


## Binder 连接池
作用:将每个业务模块的Binder请求统一转发到远程`Service`中执行.避免重复创建`Service`的过程.


在项目中有多个地方使用到AIDL,创建了多个AIDL接口的时候,再多创建一个AIDL接口用来管理所有的AIDL接口类,通过一个接口获取其他所有的AIDL接口

## 选择合适的IPC方式

|名称|优点|缺点|使用场景|
|----|----|----|--------|
|Bundle|简单易用|自能传输Bundle支持的数据类型|适合四大组件的进程间通讯|
|文件共享|简单易用|不适合高并发,无法做到进程间即时通讯|无并发.交换简单的数据实时性不高|
|AIDL|功能强大.一对多,实时通讯|使用复杂需要处理号线程同步|一对多通讯,有ＲＰＣ需求|
|Messenger|功能一般，一对多的串行通讯．实时｜不支持RPC|无RPC需求,低并发的一对多通讯|
|ContentProvider|分享存储在数据库中的数据|可以理解为受约束的AIDL,提供数据源的CRUD操作|一对多的数据共享|
|Socket|功能强大|不支持RPC,细节处理繁琐|网络数据交换|

# View 的事件体系

## View的基础知识

### 什么是View
Android中所有控件的基类.

### View 的位置信息

由它的四个顶点来决定对应View的四个属性: mLeft,mTop,mRight,mBottom

***注意:***上面四个属性的值都是相对与View的父容器来说的.

View的宽高:

	width = mRight - mLeft;
	height = mBottom - mTop;

这四个属性都是`protected`,通过相应的`getter`方法来获取四个位置属性.

Android 3.0 后引入另外几个参数: x,y,translationX,translationY;

x,y:表示View左上角坐标

translationX,translationY:View相对父容器的偏移量

	x = mLeft + translationX;
	y = mTop + translationY;

**注意:**View滑动的时候 top和left没有变,变的是translationX,translationY,x,y

### MotionEvent 和 TouchSlop

MotionEvent:在手指触摸屏幕后发生的一系列事件:

- ACTION_DOWN:手指刚接触屏幕
- ACTION_MOVE:手指在屏幕上移动
- ACTION_UP:手指离开屏幕的瞬间


通过MotionEvent对象可以获得手指的x和y坐标: 系统提供了两组方法: `getX/getY` , `getRawX/getRawY`(一个获取到的坐标是相对当前View的,一个是相对手机屏幕的)

TouchSlop:是系统所能识别出被认为是滑动的最小距离. 是一个常量值,不同的手机中它的值不同.

获得这个值:

	ViewConfiguration.get(getContext()).getScaledTouchSlop();

使用:在触摸事件中做过滤

### VelocityTracker , GestureDetector , Scroller

#### VelocityTracker
最终手指在滑动过程中的速度,包括水平和垂直反向的速度.

使用:在`View`的`onTouchEvent`方法中追踪当前的事件速度

	VelocityTracker velocityTracker = VelocityTracker.obtain();
	velocityTracker.addMovement(event);

获取速度:

	velocityTracker.computeCurrentVelocity(1000); 
	int xVelocity = velocityTracker.getXVelocity();
	int yVelocity = velocityTracker.getYVelocity();

1. 获取速度前必须调用计算速度的方法
2. 速度可能是负的,计算公式为:

		速度 = (终点位置 - 起点位置) / 时间段

回收:不需要使用的时候调用

	velocityTracker.clear();
	velocityTracker.recycle();

#### GestureDetector
手势检测,辅助检测用户的单击,滑动,长按,双击等行为.

使用:

1. 创建对象

		GestureDetector mGestureDetector = new GestureDetector(this);
		// 解决长按屏幕无法拖动的现象
		mGestureDetector.setLongpressEnabled(false);

2. 接管目标`View`的`onTouchEvent`方法:在`onTouchEvent`方法中

		boolean consume = mGestureDetector.onTouchEvent(event);
		return consume;
接口:

1. onGestureListener:内部的方法
	- onDown:由一个ACTION_DOWN触发
	- onShowPress:手指在屏幕上没有松开或者拖动的状态
	- onSingleTapUp:单击
	- onScroll:手指按下拖动, 一个ACTION_DOWN,多个ACTION_MOVE
	- onLongPress:长按屏幕
	- onFling:快速滑动

2. onDoubleTapListener:内部方法
	- onDoubleTap:双击,和`onSingleTapConfirmed`不会共存
	- onSingleTapConfirmed: 和`onSingleTapUp`区别,它只能是严格的单击,不能是双击中的一次单击
	- onDoubleTapEvent:双击过程中每个ACTION会触发
	
>建议:如果只是监听滑动相关的,建议自己在`onTouchEvent`中实现,如果监听双击行为的话使用`GestureDetector`会很方便.

#### Scroller
弹性滑动对象,用于实现弹性滑动.

如果使用`View`的`scrollTo/scrollBy`方法来滑动是不会有过度动画的.

## View 的滑动

### 使用 scrollTo/scrollBy

它改变的是内容的位置,也不能将当前的View滑动到附近所在View的区域上.

### 使用动画

### 改变布局参数

改变LayoutParams

一般可以多写一个View,控制它的宽高来达到平移的效果.

		MarginLayoutParams lp = (MarginLayoutParams)mBtn.getLayoutParams();
		// 设置参数 ...
		mBtn.requestLayout();
		// 或者 mBtn.setLayoutParams(lp);

### 各种滑动方式的对比

scrollTo/scrollBy:操作简单,适合对View的内容滑动.

动画:操作简单,复杂的动画效果.

改变布局参数:操作复杂,适合有交互的View.

## 弹性滑动

### 使用Scroller

### 通过动画

### 使用延时策略

## View 的事件分发机制

### 点击事件的传递规则

三个方法:

	public boolean dispathTouchEvent(MotionEvent ev)

进行事件分发, 返回的结果受当前View的`onTouchEvent`和下级的`dispatchTouchEvent`方法的影响,表示是否消耗当前事件.

	public boolean onInterceptTouchEvent(MotionEvent event)

在`dispatchTouchEvent`方法内部调用这个方法, 用来-判断是否要拦截某个事件.

	public boolean onTouchEvent(MotionEvent event)

在`dispatchTouchEvent`内部调用,返回结果表示是否消耗当前事件, 如果不消耗,则在同一事件序列中 当前View无法再次接收到事件.

事件处理伪代码:

	public boolean dispatchTouchEvent(MotionEvent ev){
		boolean consume = false;
		if(onInterceptTouchEvent(ev)){
			consume = onTouchEvent(ev);
		} else {
			consume = child.dispatchEvent(ev);
		}
		return consume;
	}

>伪代码解释:对于一个根ViewGroup在接收到一个点击事件的时候,首先会调用`dispatchTouchEvent`,这个方法内部右先调用`onInterceptTouchEvent`,如果这个方法返回`true`表示要拦截这个事件,`ViewGroup`自己消耗这个事件调用`onTouchEvent`, 如果这个方法返回`false`表示不拦截,那么这个触摸事件就传到`ViewGroup`的子控件中,接着子控件的`onInterceptTouchEvent`方法会执行, 这个过程一直执行知道事件被消耗掉.

View处理事件:优先级

	onTouchListener > onTouchEvent > onClickListener

事件传递:先向下传,如果最底层元素没有处理事件(onTouchEvent 没有返回true),就一级一级往会传

	Activity --> Window --> View ; View --> Window --> Activity

几个事件分发结论:

	P142

### 事件分发的源码解析


## View 滑动冲突

### 常见的滑动冲突场景
- 外部滑动方向和内部滑动方向不一致
- 外部滑动方向和内部滑动方向一致
- 上面两种情况嵌套

### 滑动冲突处理规则

场景一处理规则:

1. 根据滑动路径和水平的夹角
2. 水平和垂直方向上的距离
3. 水平和垂直方向的速度差

场景二处理规则:

1. 根据业务

场景三处理规则:

1. 具体分析

### 滑动冲突解决方式

#### 外部拦截法
比较符合点击事件的处理机制.

伪代码:

	public boolean onInterceptTouchEvent(MotionEvent ev){
		boolean intercepted = false;
		int x = (int) event.getX();
		int y = (int) event.getY();
		switch(event.getAction){
			case MotionEvent.ACTION_DOWN:
				intercepted = false;
				break;
			case MotionEvent.ACTION_MOVW:
				if(是父容器需要处理的点击事件){
					intercepted = true;
				} else {
					intercepted = false;
				}
				break;
			case MotionEvent.ACTION_UP:
				intercepted = false;
				break;
			default:
				break;
		}
		mLastXInterceptX = x;
		mLastYInterceptY = y;
		return intercepted;
	}


#### 内部拦截法



# View 的工作原理

## 初识 ViewRoot 和　DecorView

ViewRoot 对应于　ViewRootImpl 类, 它是连接Window和DecorView的纽带.

View的三大流程是通过ViewRoot完成的.

View的三大流程:

1. 测量流程
2. 布局流程
3. 绘制流程

## 理解 MeasureSpec
View的尺寸由 MeasureSpec 和 父容器 影响.

### MeasureSpec
MeasureSpec是一个 32位 int值, 高2位代表SpecMode, 低30位代表SpecSize.

SpecMode:测量模式.

1. UNSPECIFIED:父容器不对子View作限制,View需要多大的宽高就给多大.
2. EXACTLY:表示父容器已经检测到View的高宽, 此时View的宽高由SpecSize决定, 对应与:`match_parent` 和 `View在xml中指定宽高`
3. AT_MOST:父容器指定了一个大小, View不能超过这个数值, 对应于:`wrap_content`

### MeasureSpec 和 LayoutParams 的对应关系
系统内部通过 MeasureSpec 来进行View的测量, 正常情况下会使用View 指定的 MeasureSpec;

但是我们也可以给View设置LayoutParams, 在View测量的时候, 系统会将LayoutParams在父容器的约束下转换成MeasureSpec,然后根据这个确定View的宽高.

LayoutParams需要和父容器一起决定View.

## View的工作流程

### measure 过程
View测量,如果是ViewGroup的话还会测量其子View.

**View 的 measure 过程**

View的measure过程由measure方法完成, measure是一个final类型, View 的measure方法内部会调用`onMeasure()`方法,`onMeasure()`可以被重写.

View的`onMeasure()`方法:

	protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        	setMeasuredDimension(getDefaultSize(getSuggestedMinimumWidth(), widthMeasureSpec),
	                getDefaultSize(getSuggestedMinimumHeight(), heightMeasureSpec));
			    
	}

`setMeasureDimension()`方法设置View的宽高.

`getDefaultSize()`方法:

	public static int getDefaultSize(int size, int measureSpec) {
	        int result = size;
	        int specMode = MeasureSpec.getMode(measureSpec);
	        int specSize = MeasureSpec.getSize(measureSpec);

		switch (specMode) {
		        case MeasureSpec.UNSPECIFIED:
		            result = size;
		            break;
		        case MeasureSpec.AT_MOST:
		        case MeasureSpec.EXACTLY:
		            result = specSize;
		            break;
												        
		}
		return result;
				    
	}

直接继承View的自定义控件需要重写`onMeasure()`方法并设置`wrap_content`时的自身大小,否则在布局中相当于使用了`match_parent`.

`onMeasure()`处理代码:P186

**ViewGroup 的 measure过程**
除了测量自己.还会遍历测量子View

.....

#### 获取View的宽高

View的测量是需要时间的,我们在 Activity 的`onCreate`,`onStart`,`onResume`直接获取View的宽高很可能获取到的值为`0`.

解决方法:

1. Activity/View #onWindowFocusChanged

	这个方法表示:View已经初始化完毕. *注意*这个方法会调用多次,在activity失去和获得焦点都会调用

	代码:

		public void onWindowFocusChanged(){
	 		super.onWindowFocusChanged();
			if(hasFocus){
				int width = mView.getMeasuredWidth();
				int height = mView.getMeasureHeight();
			}
		}

2. view.post(runnable)

		mView.post(new Runnable(){
			@Override
			public void run(){
				int width = mView.getMeasureWidth();
				int height = mView.getMeasureHeight();
			}
		});


3. ViewTreeObserver

	使用ViewTreeObserver的众多回调来获得尺寸.

4. view.measure(int widthMeasureSpec, int heightMeasureSpec)

	复杂....

### layout过程
layout作用确定ViewGroup中子元素的位置.

`layout`方法确定`ViewGroup`本身的位置,`onlayout`方法确定子`View`的位置.

`layout`:

	通过`setFrame`方法设定View的2个顶点的位置参数.

`onlayout`

### draw 过程
将View绘制到屏幕上.

绘制过程:

1. 绘制背景 background.draw(canvas);
2. 绘制自己 (onDraw)
3. 绘制 (dispatchDraw)
4. 绘制装饰 (onDrawScrollBars)

## 自定义 View

### 自定义 View 的分类

1. 继承View重写onDraw方法

	静态或者动态显示一些不规则的图形, 需要自己支持`wrap_content`,`padding`
2. 继承ViewGroup派生特殊的Layout

	自定义布局
3. 继承特定的View(比如 TextView)

	扩展某种View的功能
4. 继承特定的ViewGroup(比如 LinearLayout)
	
	常见,相比2更容易些

###自定义View须知

1. 让View支持wrap_content: 继承View或者ViewGroup时需要处理
2. 如果有必要,让View支持padding
3. 尽量不要在View中使用Handler: 可以使用`post`系列方法来实现.,除非真要用Handler发消息
4. View中如果有线程或者动画,需要及时停止:参考 View#onDetachedFromWindow
5. View中带有滑动嵌套的时候,需要处理滑动冲突

### 自定义View实例
....

# 理解 RemoteViews

RemoteViews表示的是View的结构，它可以在其他进程中显示，内部提供了一组基础的跨进程更新界面的方法。

使用：

1. 通知栏
2. 桌面小部件

## RemoteViews 的应用

### RemoteView 在通知栏上的应用

RemoteViews的创建：需要应用包名，布局文件资源id

### RemoteViews 在桌面小部件上的应用
AppWidgetProvider是Android中提供用于实现桌面小部件的类，本质是广播。

继承至BroadcastReceiver

#### 使用步骤：

#### 1 定义小部件界面

`res/layout`中创建布局文件

#### 2 定义小部件配置信息

`res/xml`下创建`appwidget_provider_info.xml`(名称随意)

	<appwidget-provider xmlns:android="....."
		android:initialLayout="@layout/widget"
		android:minHeight = "84dp"
		android:minWidth = "84dp"
		android:updatePeriodMillis = "86400000"

	</appwidget-provider>

updatePeridMillis:表示小部件的自动更新周期。

#### 3 定义小部件的实现类

创建一个继承`AppWidgetProvider`


#### 4 在AndroidManifest.xml中声明小部件
必须要定义一个action，作为桌面小部件的标识。

根据不同的action执行不同的方法：

- `onEnable`
- `onUpdate`
- `onDelete`
- `onDisable`
- `onReceive`


### PendingIntent 概述
表示一种处于pending状态的Intent，在将来某个时候发生。

作用：给RemoteViews添加点击事件。

支持三种待定意图：

1. 启动Activity
2. 启动Service
3. 发送广播

参数：

1. content
2. requestCode：一般设置为0;
3. intent
4. flags

**flags:**

1. FLAG_ONE_SHOT:表示这个PendingIntent只能使用一次，使用后就会自动cancel。
2. FLAG_NO_CREATE:表示PendingIntent不会主动创建，一般不使用。
3. FLAG_CANCEL_CURRENT:表示当前描述的PendingIntent如果已经存在，他们都会被cancel，然后创建一个新的PendingIntent，对于通知栏，之前的PendingIntent就不能使用了。
4. FLAG_UPDATE_CURRENT:更新已经存在的PendingIntent

## RemotesViews 的内部机制
作用：在其他进程中显示并更新View界面。

RemoteViews没有提供`findViewById`方法，可以使用`set`方法来更新UI界面。

。。。。。。。。。。





# Android 的 Drawable

Drawable表示的是一种可以在Canvas上进行绘制的抽象的概念.

## Drawable 简介
表示一种图像概念, 在开发中常用作背景使用,一般用xml定义.

Drawable在Android是一个抽象类.

Drawable可以通过`getIntrinsicWidth`和`getIntrinsicHeight`获得宽高,但是并不是所有的Drawable都有宽高的.

一般来说过Drawable没有宽高的概念,它作为背景的时候会被自动拉伸至View的大小.

## Drawable 的分类

### BitmapDrawable 

最简单的Drawable。

标签：bitmap

属性：

- `android：src`图片资源
- `android：antialias`是否开启图片抗锯齿
- `android：dither`是否开启抖动效果，防止图片失真
- `android：filter`是否开启过滤效果，图片被拉伸或者压缩的时候可以保持较好的显示效果
- `android：gravity`图片小于容器的时候，用于定位，多个选项用`|`隔开
- `android：mipMap`图像处理技术，纹理映射， 一般`false`
- `android：titleMode`平铺，


### ShapeDrawable
一种常见的Drawable，可以理解为通过颜色来构造图形。

	<shape
		xmlns:android="http://schemas.android.com/apk/res/android"
		android:shape=["rectangle"|"oval"|"line"|"ring"]>

		<corners
			android:radius="integer"
			android:topLeftRadius="integer"
			android:topRightRadius="integer"
			android:bottomLeftRadius="integer"
			android:bottomRightRadius="integer"/>

		<gradient
			android:angle="integer"
			android:centerX="integer"
			android:centerY="integer"
			android:centerColor="integer"
			android:endColor="color"
			android:gradientRadius="integer"
			android:startColor="color"
			android:type="["linear"|"radial"|"sweep"]"
			android:useLevel=["true"|"false"]/>

		<padding
			android:left="integer"
			android:top="integer"
			android:right="integer"
			android:bottom="integer"/>

		<size
			android:width="integer"
			android:height="integer"/>

		<solid
			android:color="color"/>

		<stroke
			android:width="integer"
			android:color="color"
			android:dashWidth="integer"
			android:dashGap="integer"/>
	</shape>


**android:shape**：

- 四个选项，默认是矩形，line和ring 必须通过`stroke`标签来指定线宽的颜色
- ring：有五个特殊属性：`android:innerRadius`,`android:thickness`,`android:innerRadiusRatio`,`android:thicknessRatio`,`android:useLevel`


**corner**:

- 只适用于矩形，设置四个角的圆角


**gradoent:**

- 和`solid`标签互斥


**solid:**

- 纯色填充


**stroke:**

- 描边，可设置虚线


**paddding:**

- 内边距


**size:**

- 表示shape的固有大小，但不是最终显示的大小，它会被拉伸或者压缩


### LayerDrawable
对应标签：`layer-list`，将不同的Drawable放置在不同层上叠加。

	<layer-list
		xmlns:android="http://schema.android.com/apk/res/android">
		<item
			android:drawable="@[package:]drawable/drawable_resource"
			android:id="@[+][package:]id/resource_name"
			android:top="dimension"
			android:right="dimension"
			android:bottom="dimension"/>
	</layer-list>

一个`layer-list`包含多个`item`，每个`item`表示一个Drawable。

### StateListDrawable
对应标签`selector`，也是表示Drawable集合，每个Drawable对应一个状态。主要用于设置成可点击的控件的背景。

	<selector xmlns:.....
		android:constantSize=["true"|"false"]
		android:dither=["true"|"false"]
		android:variablePadding=["true"|"false"]>

		<item
			android:drawable="@[package:]drawable/drawable_resource"
			android:state_pressed=["true"|"false"]
			android:state_focused=["true"|"false"]
			android:state_hovered=["true"|"false"]
			android:state_selected=["true"|"false"]
			android:state_checkable=["true"|"false"]
			android:state_checked=["true"|"false"]
			android:state_enabled=["true"|"false"]
			android:state_activated=["true"|"false"]
			android:state_window_focused=["true"|"false"]/>
	</selector>

- `android:constantSize`:表示drawable的固有大小是否不随状态改变， 默认`false`
- `android:dither`:是否开启抖动，默认`true`
- `android:variablePadding`:padding是否随状态改变，默认`false`建议`false`


### LevelListDrawable
对应标签：`level-list`,语法：

	<level-list
		xmlns:android="..."
		<item
			android:drawable="@drawable/drawable_resource"
			android:maxLevel="integer"
			android:minLevel="integer"/>
	</level-list>

使用：

1. 作为View的背景使用，通过Drawable的`setLevel`方法
2. 作为`ImageView`的前景使用，通过ImageView的`setImageLevel`方法来切换Drawable

级别：0-10000

### TransitionDrawable

对应标签`transition`，用于实现Drawable之间的淡入和淡出效果。

语法：

	<transition
		xmlans:android="...."
		<item
			android:drawable="@[package:]drawable/drawable_resource"
			android:id="@[+][package:]id/resource_name"
			android:top="dimension"
			android:right="dimension"
			android:bottom="dimension"
			android:left="dimension"/>
	</transition>

例子：

	<TextView
		android:id="@+id/button"
		android:layout_height="wrap_content"
		android:layout_width="wrap_content"
		android:background="@drawable/transition_drawable"/>

通过`startTransition`,`reverseTransition`实现淡入淡出效果以及逆过程。

	TransitionDrawable drawable = (TransitionDrawable) textView.getBackground();
	drawable.startTransition(1000);

### InsetDrawable
对应`inset`标签，将其他Drawable内嵌到自己当中，并可以在四周流出一定间距。 当一个View希望自己的背景比自己的实际区域小，可以采用InsetDrawable实现，也可以使用LayerDrawable实现。

语法：

	<inset
		xmlns:android="..."
		android:drawable="@drawable/drawable_resource"
		android:insetTop="dimension"
		android:insetBottom="dimension"
		android:insetRight="dimension"
		android:insetLeft="dimension"/>

属性表示各个方向的内凹距离。

实例：

	<inset xmlns:android="..."
		android:insetBottom="15dp"
		android:insetTop="15dp"
		android:insetLeft="15dp"
		android:insetRight="15dp">

		<shape android:shape="rectangle">
			<solid android:color="#ff0000"/>
		</shape>
	</inset>

### ScaleDrawable
对应`scale`标签，根据等级属性设置Drawable的缩放比例。

### ClipDrawable
对应标签`clip`，更具等级来裁剪图片。 裁剪方向由：`android:clipOrientation`,`android:gravity`控制。

例子：

	<ImageView
		android:id="@+id/test_clip"
		android:layout_width="100dp"
		android:layout_height="100dp"
		android:src="@drawable/clip_drawable"
		android:gravity="center"/>

	ClipDrawable drawable = (ClipDrawable) imageView.getDrawable;
	drawable.setLevel(5000);

等级范围：0-10000,0表示完全裁剪， 10000表示不裁剪。

## 自定义Drawable
一般不使用。




# 理解 Window 和 WindowManager 

Window 是一个抽象类,它的具体体现是PhoneWindow.

通过WindowManager创建Window.

Window是View的直接管理者.

## Window 和 WindowManager

### Flags
Window的属性

几个常用的:

- FLAG_NOT_FOCUSABLE:表示window不需要获取焦点
- FLAG_NOT_TOUCH_MODAL:一般来说都需要开启,当前window以外的触摸事件传递到其他的window
- FLAG_SHOW_WHEN_LOCKED:window显示在锁屏界面


### Tapy
表示Window的类型/

1. 应用Window:对应一个Activity
2. 子Window:不能独立存在,必须依附于特定的父Window中(比如:Dialog)
3. 系统Window:需要声明权限,比如:Toash,系统状态栏

### Window 分层
每个Window对应有z-ordered,层级大的覆盖在层级小的window上面.

层级:

	应用Window < 子Window < 系统Window

### windowManager 提供的功能
1. 添加View
2. 更新View
3. 删除View

实现可拖动的Window: P297

## Window 内部机制

每个Window都对应一个View和一个ViewRootImpl, window和View通过ViewRootImpl建立联系.

### Window 的添加过程

### window 删除过程

###　window 更新过程

## Window 创建过程
View是Android中视图的呈现方式, 但是View不能独立存在,它需要依附在Window中,因此有视图的地方就有Window.

### Activity 的Window创建过程

### Dialog 的Window创建过程
必须使用Activity的Context来创建,如果使用Application的Context创建会报错.

报错:没有应用的token,token一般只有Activity持有.

注意:系统Window不需要token,指定type

### Toast 的创建过程
Toast的创建流程比Dialog复杂,它具有定时消失功能,所以系统采用了Handler.

Toast内部有两类IPC:

1. Toast访问NotificationManagerService
2. NotificationManagerService回调Toast里的TN接口

Toast内部视图指定方式:

1. 系统默认
2. `setView()`方法来自定义视图

Toast的show和cancel用于显示和隐藏Toast,内部是一个IPC的过程.

# 四大组件的工作流程


# Android 的消息机制

Handler是Android消息机制的上层接口，通过Handler轻松将任务切换到Handler所在的进程中去执行。

通常作用：更新UI

Android消息机制一般就是指Handler的运行机制，Handler运行需要MessageQueue和Looper支持。

MessageQueue是消息队列，用于存储消息。

Looper：消息循环，循环从消息队列中查找消息，并将消息发送到相应的Handler中处理。

ThreadLocal：不是线程，作用是在每个线程中存储数据，在不同的线程中互补干扰地存储并提供数据。

Handler创建的时候需要Looper，获取Looper就需要用到ThreadLocal；线程默认是没有Looper（除了UI线程ActivityThread）

## Android 消息机制概述
ViewRootImpl的`checkThread`方法中会检测UI更新线程是否是UI线程，否则抛异常。

Handler的出现是为了解决子线程无法访问UI的矛盾。

**系统为什么不允许子线程访问UI线程？**

1. 因为UI控件不是线程安全的，并发访问会出现问题
2. 如果加上锁机制，会让逻辑变得复杂，访问效率低

##　Android 的消息机制分析

### ThreadLocal 的工作原理
是一个线程内部的数据存储类，通过它可以在指定的线程中存储数据，数据存储后，只有在指定的线程中才能获取到存储的数据。

**作用:*

1. 基于上面的特征：Handler可以通过它来存取Looper。
2. 复制逻辑下的对象传递。
	在函数调用栈比较深，同时又需要用监听器做接口回调的时候，可以使用ThreadLocal，它让监听器作为全局对象存在。
	需要在同一个线程中，并且操作同一个ThreadLocal对象。

**线程访问同一个ThreadLocal对象，但是获取的值是不一样的。**

因为不同线程访问同一个ThreadLocal的`get`方法，ThreadLocal会在各自线程中取出一个数组从这个数组中根据这个ThreadLocal索引找值。

Thread类内部有个成员用于存储ThreadLocal数据： `ThreadLocal.Values localValues`,内部有一个数组存储数据。

不同线程中虽然使用同一个`ThreadLocal`对象，但是他们操作的不是同一个`localValues`

### 消息队列的工作原理
MessageQueue主要包含两个操作：插入，读取（删除） ； `enqueueMessage`,`next`.

通过单链表的数据结构维护消息列表。

`next`方法内部是一个无限循环，作用：找到消息并返回删除。

### Looper 的工作原理
会不停从MessageQueue中查看是否有新消息，如果有就处理，没有就一直阻塞。

构造方法：(私有)

	private Looper(boolean quitAllowed){
		mQueue = new MessageQueue(quitAllowed);
		mThread = Thread.currentThread();
	}

如何创建Looper对象：`Looper.prepare()`

如何开始消息循环：`Looper.loop()`

通常步骤：

	new Thread("Thread#2"){
		@Override
		public  void run(){
			Looper.prepare();
			Handler handler = new Handler();
			Looper.loop();
		}
	}

针对主线程：`prepareMainLooper`,`getMainLooper`一般不会使用。

关闭Looper：`quit`，`quitSafely`，一般在子线程中使用Looper的时候，需要手动关闭，来终止消息循环。

`loop（）`方法：内部无限循环，发现消息执行 消息处理：`msg.target.dispatchMessage(msg)`

### Handler 的工作原理

消息发送和接收。

最终消息由Looper交给Handler处理，Handler调用`dispatchMessage`方法：

	public void dispatchMessage(Message msg){
		if(msg.callback != null){
			handleCallback(msg);
		} else {
			if(mCallback != null){
				if(mCallback.handleMessage(msg)){
					return;
				}
			}
			handleMessage(msg);
		}
	}

Message的callback就是一个Runnable对象，就是Handler的post方法所传递的参数。

`handleCallback（msg）`方法内部就是执行了Runnable的`run`方法。

`mCallback`是一个接口，在创建Handler的时候作为参数。

Handler构造方法，可以通过特定的Looper来创建，就是说Handler在创建的时候会检查本线程中的Looper对象是否存在，否则抛异常。

## 主线程的消息循环
主线程入口：`main`方法，这个方法里调用：

	Looper.prepareMainLooper();
	Looper.loop();
主线程Handler： ActivityThread.H, 内部定义了一组消息类型，主要是四大组件的启动和停止等过程。

# Android 的线程和线程池

主线程：处理和界面相关的实务。

##　主线程和子线程
java线程模型：主线程指进程所拥有的线程，在java中默认进程只有一个线程。 子线程也叫工作线程，除了主线程之外其他的都是子线程。

## Android 中的线程形态

### AsyncTask
一种轻量级的异步任务类，可以在线程池中执行后台任务，然后把执行进度和结果返回给主线程中更新UI。

它封装了Thread和Handler。

四个核心方法：

1. `onPreExecute`:在主线程中执行，在异步任务之前会调用这个方法，一般做一些准备工作
2. `doInBackground(Params ... params)`:异步任务，在这个方法中通过`publishProgress`方法更新任务进度（同时触发`onPregressUpdate`），将异步结果传递给`onPostExecute`
3. `onProgressUpdate`:在主线程中执行，用于更新进度
4. `onPostExecute`:在主线程中执行，将结果更新到UI线程

**注意：**

1. AsyncTask的对象必须在主线程中创建
2. execute方法必须在UI线程中调用
3. 不要在程序中调用AsyncTask中的四大方法
4. 一个AsyncTask对象只能执行一次，即只能调用`execute`方法一次，否则会异常

### AsyncTask 的工作原理
。。
。。


### HandlerThread

继承Thread，是一种可以使用Handler的Thread，它在其`run`方法中创建Handler，具体使用场景是与IntentService配合使用。

需要在不适用的时候`quit`或者`quitSafely`

### IntentService
是一种特殊的Service,它继承Service并且是一个抽象类。

用于执行后台耗时操作，任务执行完成会自动销毁。

优点：属于四大组件，优先级高。

内部封装了：HandlerThread和Handler

`onCreate`方法：

	创建一个HandlerThread并且调用`start`方法
	通过本线程Looper创建Handler：mServuceHandler

`onStartCommand`内部调用`onStart`:

	通过mServiceHandler发送一个消息，这个消息会在HandlerThread中被处理，处理完成就会结束线程。

## Android线程池
优点：

1. 重用线程池中的线程，避免线程创建和销毁所带来的性能消耗
2. 能有效控制线程池的最大并发数，避免大量线程之间因互相强系统资源导致的阻塞
3. 可以对线程做简单管理，并提供定时执行和指定间隔执行功能

线程池：Executor，是一个接口，真正实现类`ThreadPoolExecutor`

### ThreadPoolExecutor
构造方法提供了一系列的参数来配置线程池：

- `corePoolSize`:线程池的核心线程数，会在线程池中一直存活
- `maximunPoolSize`:线程池最大线程数，线程数达到最大后，后续的新任务被阻塞
- `keepAliveTime`:非核心线程闲置时间超过时长就会被回收， `allowCoreThreadTimeOut`设置为`true`那么核心线程也会被回收
- `unit`:keepAliveTime的单位
- `workQueue`:线程池中的队列消息，通过线程池的execute方法提交的Runnable对象会存储到这个参数中
- `threadFactory`:线程工厂，为线程池提供创建新线程的功能，是一个接口


执行任务的规则：

1. 优先使用核心线程
2. 任务中线程数量达到或者超过核心线程数，新的任务会被插入到任务队列中等待执行
3. 如果步骤2执行不成功（任务队列满了），如果线程数量未达到线程最大数，就会创建新的非核心线程
4. 步骤3也达到线程数量的时候，拒绝执行任务，会调用`rejectedExecutionHandler`

### 线程池的分类

#### FixedThreadPool

1. 通过`Executors`的`newFixedThreadPool`创建
2. 线程数固定
3. 不会被回收
4. 能快速响应外界请求
5. 没有超时机制
6. 任务队列没有数量限制

#### CachedThreadPool:

1. 通过`Executors`的`newCachedThreadPool`方法来创建。
2. 线程数不固定
3. 都是非核心线程
4. 线程数最大为：Integer.MAX_VALUE
5. 有超时时间：60s
6. 它的任务队列相当于一个空集合，因为所有的任务都会执行
7. 这类线程适合执行大量但是耗时较少的任务
8. 在没有任务的适合，它不占用任务系统资源，因为没有核心线程

### ScheduledThreadPool

1. 通过`Executors`的`newScheduledThreadPool`
2. 核心线程数量是固定的
3. 非核心线程数量是没有限制的
4. 主要用于执行定时任务和具有固定周期的重复任务

### SingleThreadExecutor

1. 通过`Executors`的`newSingleThreadExecutor`来创建
2. 只有一个核心线程，没有非核心线程
3. 确保所有的任务按顺序执行，这些任务之间不需要处理线程同步的问题
4. 将所有的任务都统一到一个线程中

最后使用方法：

	Runnable command = new Runnable(){
		@Override
		public void run(){
			SystemClock.sleep(2000);
		}
	}

	ExecutorService fixedThreadPool = Executors.newFixedThreadPool(4);
	fixedThreadPool.execute(command);

	ExecutorService cachedThreadPool = Executors.newCachedThreadPool();
	cachedThreadPool.execute(command);

	




