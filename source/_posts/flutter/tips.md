---
title: Flutter 项目
date: 2018-09-07 13:18:12
tags: [Flutter]
category: Flutter

---


# alt + enter / option + enter

![flutter-tips-00](flutter-tips-00)


# 基本命令

下载依赖：

```
flutter packages get
```

# 延时操作

```
Future.delayed(Duration(milliseconds: 800), () {
  
});
```

# key

[https://segmentfault.com/a/1190000011276853](https://segmentfault.com/a/1190000011276853)

key的作用:

Flutter是受React启发的，所以Virtual Dom的diff算法也参考过来了（应该是略有修改），在diff的过程中如果节点有Key来比较的话，能够最大程度重用已有的节点（特别在列表的场景），除了这一点这个Key也用在很多其他的地方这个以后会总结一下。总之，这里我们可以知道key能够提高性能，所以每个Widget都会构建方法都会有一个key的参数可选，贯穿着整个框架。

# 如何重建回退栈

应用场景：登入成功后

[https://juejin.im/post/5b40341b6fb9a04f9078f0a0](https://juejin.im/post/5b40341b6fb9a04f9078f0a0)

```
void main() {
  DataUtils.getUserInfo().then((userInfo) {
    runApp(MyApp(userInfo));
  });
}

class MyApp extends StatelessWidget {
  MyApp(this.userModel);

  final UserModel userModel;

  @override
  Widget build(BuildContext context) {
    GlobalKey<FormState> _formKey = new GlobalKey<FormState>();
    return MaterialApp(
      title: 'MaterialManagement',
      theme: ThemeData(accentColor: Colors.white, primaryColor: Colors.blue),
      home: userModel == null
          ? LoginPage()
          : HomePage(),
      routes: <String, WidgetBuilder>{
        '/login': (BuildContext context) => LoginPage(),
        '/home': (BuildContext context) => HomePage()
      },
    );
  }
}
```

登入成功后：

```
runApp(MyApp(userInfo));
```

# RefreshIndicator

```
final GlobalKey<RefreshIndicatorState> _refreshIndicatorKey =
      new GlobalKey<RefreshIndicatorState>();

RefreshIndicator(
          key: _refreshIndicatorKey,      

代码控制刷新：

_refreshIndicatorKey.currentState.show();

进入页面自动刷新：

  @override
  void initState() {
    super.initState();
    WidgetsBinding.instance
        .addPostFrameCallback((_) => _refreshIndicatorKey.currentState.show());
  }
```

# 获取界面宽高

```
final double width = MediaQuery.of(context).size.width;
```

# 代码设置状态栏颜色

```
SystemChrome.setSystemUIOverlayStyle(SystemUiOverlayStyle.dark.copyWith(
  statusBarColor: Colors.blue, //or set color with: Color(0xFF0000FF)
));

SystemChrome.setSystemUIOverlayStyle(SystemUiOverlayStyle.light.copyWith(
  statusBarColor: theme.primaryColor,
));
```

`SystemUiOverlayStyle.dark`: 表示文字黑色

`SystemUiOverlayStyle.light`: 表示文字白色

通过 AppBar 设置状态栏颜色的时候不需要管状态栏字体，他会自动变更，当是直接修改状态栏样式的时候需要自己确定文字颜色。



