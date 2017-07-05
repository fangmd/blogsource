---
title: ios 学习纪录
date: 2017-05-18 13:18:12
tags: ios
category: ios

---

# 设置产品属性

选中左侧 根目录 文件夹，中间试图会出现产品属性设置的界面。

产品属性包括：Target, Project 两个部分，只有一个 Project, 可以有多个 Target.

Target 继承自 Project， Target 中设置的属性可以覆盖 Project 中相应的设置。


可设置的项目：

1. 屏幕方向
2. 应用图标
3. 设置启动屏幕
4. 设置设备支持情况



# 生命周期

## 应用生命周期

AppDelegate 中有应用生命周期的回调方法：

5 个生命周期状态

- NotRunning
- Inactive
- Active:app运行在foreground和正在接收事件
- Background:运行在background和正在执行代码
- Suspended:运行在background但没有执行代码

6 个生命周期方法：

- application:willFinishLaunchingWithOptions: - 这个方法是你在启动时的第一次机会来执行代码
- application:didFinishLaunchingWithOptions: - 这个方法允许你在显示app给用户之前执行最后的初始化操作
- applicationDidBecomeActive: - app已经切换到active状态后需要执行的操作
- applicationWillResignActive: - app将要从前台切换到后台时需要执行的操作
- applicationDidEnterBackground: - app已经进入后台后需要执行的操作
- applicationWillEnterForeground: - app将要从后台切换到前台需要执行的操作，但app还不是active状态
- applicationWillTerminate: - app将要结束时需要执行的操作


## 视图生命周期

```

视图创建 --》 viewDidLoad

viewWillAppear

视图可见

viewDidAppear

viewWillDisappear

视图不可见

viewDidDisappear

```


```

didReceiveMemoryWarning // 在这个方法中释放内存

系统低内存

viewDidUnloads  // ios 6 后不在使用这个方法
```


# 基本视图控件

基类：UIView

## 视图分类

- 控件：继承自 UIControl，能够相应高级事件
- 窗口：UIWindow，一个应用只有一个
- 容器视图：UIScrollView，UIToolbar以及他们的子类。UIScrollView 子类：UITextView,UITableView,UICollectionView 当内容超出屏幕的时候可以滑动；UIToolbar 可以包含其他控件，一般放在底部或者顶部
- 显示视图：UIImageView，UILabel，UIProgressView
- 文本和Web视图:UITextView(也是容器视图),UIWebView
- 导航视图：UITabBar，UINavigationBar
- 警告框和操作表：UIAlerView, UIActionSheet


## UIControl 控件类

其子类有：UIButton, UITextField, UISlider 等，都是有能力响应一些高级事件的控件

### UITextField and UITextView

UITextField 继承自 UIControl，UITextView 继承自 UIScrollView 两者都有对应的 委托协议

Placeholder: 设置提示文字

Clear Button: 清理输入的文字

通过 Keyboard Type 设置键盘类型

### 键盘的处理

当 UITextField，UITextView 作为第一响应者的时候，键盘就会出现，如果要让键盘消失，就需要改变第一相应者

## UIScrollView

## 等待相关的控件和进度条

### 活动指示器 ActivityIndicatorView

### UIProgressView

## 警告框和操作表用于和用户交流

## 工具栏和导航栏

### 工具栏 UIToolbar

位于底部，一般不超过 5 个按钮

Toolbar, Bar Buttom Item, Fixed Sapce Bar Button Item, Flexible Space Bar Button Item



### 导航栏

Navigayion Item, Bar Buttom Item

## 选择器

### 日期选择器：UIDatePicker

属性有：

1. Mode：设定日期选择器模式
2. Local：设定本地化，按照本地习惯和文字显示日期
3. Interval: 设定间隔时间，单位为分钟
4. Date: 设定开始时间
5. Constraints:设定能显示的最大和最小日期
6. Timer:在倒计时定时器模式下倒计时的秒数

### 普通选择器 UIPickerView

需要两个重要的协议：UIPickerViewDataSource, UIPickerViewDelegate

## 表视图 

两个协议：UITableViewDelegate, UITableViewDataSource

基本概念

1. 表头视图：table header view
2. 表脚视图：table footer view
3. 单元格：cell，列表中的每一行
4. 节 section：由多个单元格组成，有节头和节脚
5. 节头
6. 节脚

### 表视图相关类

UITableView 继承自 UIScrollView

UITableViewCell:单元格类

UITableViewController 类是 UITableView 的控制器

UITableViewHeaderFooter 类用于为节头和节脚提供视图

### 表视图分类

1. 普通表视图
2. 分组表视图


### UITableViewDelegate, UITableViewDataSource

UITableViewDelegate:

- tableView:cellForRowAtIndexPath, 返回UITableViewCell* , 要给表视图单元格提供数据，这个方法是必须实现的
- tableView:numberOfRowsInsSection, 返回 NSInteger, 返回某个节点中的行数
- tableView:titleForHeaderInSection, return NSString, 返回节点的标题
- tableView:titleForFooterInSection, return NSString, 返回节教的标题
- numberOfSectionInTableView, return NSInteger, 返回节个数
- sectionIndexTitleForTableView, return NSArray*, 提供表视图节索引标题
- tableView:commitEditingStyle:forRowAtIndexPath, return void, 为删除或修改提供数据

UITableViewDelegate 协议主要用来设定表视图中节头和节脚的标题，并响应一些动作事件

- tableView:viewForHeaderInSection, return UIView *, 为节头准备自定义视图，ios6之后可以使用UITableViewHeaderFooterView
- tableView:viewForFooterInSection, return UIView*, 为节脚准备自定义视图，ios6之后可以使用UITableViewHeaderFooterView
- tableView:didEndDisplayingHeaderView:forSection, return void, 在节头重屏幕消失后触发
- tableView:didEndDisplayingFooterView:forSection, return void, 在节脚从屏幕消失后触发
- tableView:didEndDisplayCell:forRowAtIndexPath, return void, 当单元格从屏幕消失后触发
- tableView:didSectionRowAtIndexPath, return void, 响应选择表视图单元格时调用方法

### 使用



## 搜索栏视图

涉及的类和协议：

- UISearchBar
- UISearchBarDelegate: 搜索栏控件的委托协议
- UISearchDisplayController：管理搜索栏并显示搜索结果视图
- UISearchDisplayDelegate：管理事件处理









## 应用界面构建层次

- UIWindow
    - UIView: 根视图
        + Button1
        + Label2
        + UIView（View2）
        

# 屏幕适配

NavigationBar: 44 点

UIStatusBar: 20 点

Toolbar: 49 点

## AutoLayout

为空间布局定义了一套约束 (constraint) , 可以通过 Interface Builder, 或者 代码实现


#



    

# UI 状态保持和恢复

应用退出，需要保持UI状态，重新进入的时候和退出时一致。

实现这个效果需要在下三个地方做出修改：

1. 应用程序委托对象
2. 视图控制器
3. 自定义视图


在 视图文件中 选中 `View Controller` 点击右侧菜单 `Shou the Identity inspector`中设置 恢复id`Restoration ID` 

在 AppDelegate 中添加下面代码：

```


```

<!--more-->

# IOS 整体架构

- Cocoa Touch: 构建 IOS 应用基本系统服务（多任务，触摸输入，推送通知等）和关键框架
- Media：提供图形，音频，视频和AirPlay技术
- Core Services：iCloud，应用内购买，SQLite数据库，XML技术
- Core OS：低级功能，开发中用不到


# 工具使用相关

## Simulator

Home 键: `command + shift + h`

