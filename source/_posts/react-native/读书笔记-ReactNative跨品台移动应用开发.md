---
title: 读书笔记-ReactNative 跨平台移动应用开发
date: 2018-08-01 13:18:12
tags: [React Native]
category: 读书笔记

---


# 第2章 状态机思维与状态机变量

React 框架将所有的 UI 视为一个简单的状态机，那么任意一个 UI 场景就是状态机中的一种状态。

>状态在 state 中保存， 状态机可以意指 state

在 React 中将状态机视为 “不可变的常量”。

不能使用类似 `this.state.XXX = 'xxxx'` 这样的语句


### 减少 state 中的值

在 state 中保存尽量少的值。

state 中值每次改动就会触发 UI 的重新绘制。

## 深入理解 UI 重新渲染的过程

### 合并 state 变量

React Native 使用 setState 函数合并状态机变量, setState 函数的原型是：

```
setState(oldState, callback)
```

> setState 后状态如果没有改变，不会触发渲染



## React Native 组件的成员变量

在构造函数中定义成员变量，可以保证成员变量初始化了。

```
this.myProp = 'test';
this.myProp1 = true;
```

## 静态变量和静态函数

用法与 Java 一致

```
static myStatic = 'init';
static myMethod(){
    //....
}
```

## 组件回调函数的绑定

绑定操作为了让回调函数正确解析出 this.

如果回调函数使用了箭头函数来回调，则不需要绑定。

回调函数 4 种写法：

```
onChangeText = {(newText) => this.updateNum(newText)}; // 不需要 bind
```


```
updateNum = (newText) => {
    this.setState((state)=>{

        })
}

onChangeText={this.updateNum}; // 不需要 bind
```

```
// bind

onChangeText = {this.updatePW}
```

```
onChangeText={this.updatePW.bind(this)}
```

# 第五章 flex 布局，View, Image 与 可触摸组件

## Flex 布局

### 位置及宽，高相关样式键

position: 默认 relative, absolute.

absolute: 时使用 top, bottom, left, right 四个中的两个描述位置。表示相对父控件的位置（FrameLayout）

relative: top, bottom, left, right 表示相对同级元素的位置。

### 决定子组件排列规则的键

flexDirection, flexwrap, justifyContent, alignItems

### 决定组建显示规则的键

flex : 默认值 0 ，可以是 -1，0 任意值。

flex 值非零时，会自动缩放填充父控件剩余的空间，弹性方向如果是列，组件的 height 可能半失效，弹性方向是行，组件的 width 会半失效。

alignSelf: auto, flex-start, flex-end, cener, stretch. 在子控件中设置，作用是忽略父组件的 alignItems 值。

## View

View 组件是 React Native 最基本的组件，所有的组件都继承了 View 组件的属性。

- backgroundColor
- opacity
- borderStyle
- borderColor
- borderRadius

阴影效果：

- shadowColor
- shadowOffset
- shadowOpacity
- shadowRadius
- overflow: visible, hidden; 是否隐藏超出父控件的内容，只在 IOS 中有效
- elevation: Android 独有

View 变形： 角度：deg,

- translate
- scale
- rotate
- skew

## Image

也可以加载需要认证信息的 图片。

```
let imageSource = {
    uri: 'http://.....'
    headers:{
        Authorization1: '.....',
    }
}
```

可以使用 Image.prefetch 预加载图片

### 组件样式

resizeMode: cover, contain, stretch, center, repeat

## 可触摸组件

- TouchableNativeFeedback
- TouchableWithoutFeedback
- TouchableHighlight
- TouchableOpacity

# Text TextInput

## Text

居中：

```
justifyContent: 'center',
alignItems: 'center',
```


## Keyboard 

监听 Keyboard 状态，改变布局，防止界面被遮挡。

## 跨平台状态栏组件

StatusBar

## 高度自增输入框

AutoExpandingTextInput

# 第七章 组件生命周期，数据存储及 React Native 应用实现步骤

- constructor

初始化成员变量，初始化 state 值。

- componentWillMount

在 render 之前执行，这个方法中调用 setState 不会马上触发渲染。

在这里从本地读取数据

- componentDidMount

render 之后执行，在这里执行网络请求代码

- componentWillReceiveProps

- shouldComponentUpdate

判断是否需要渲染界面，在调用 setState 后执行。

返回 true: 表示需要渲染， false: 表示不渲染。

通过这个函数阻止不必要的重新渲染。

- componentWillUpdate

在重新渲染之前执行，在这里为重新渲染做一些准备工作。

- componentDidUpdate

重新渲染之后执行

- componentWillUnmount

组件从界面卸载之前执行

在这里释放资源，取消事件订阅

# 第十五章 选择器，位置想过和应用状态

## AppState API

通过 AppState Api 获取应用状态：是否在前台。



















