---
title: Flutter 国际化 - InheritedWidget
date: 2018-09-12 13:18:12
tags: [Flutter]
category: Flutter

---



# 使用 InheritedWidget

ShrineTheme 作为 root widget

```
class ShrineTheme extends InheritedWidget {
  ShrineTheme({ Key key, @required Widget child })
    : assert(child != null),
      super(key: key, child: child);

  final Color cardBackgroundColor = Colors.white;

  final TextStyle appBarTitleStyle = robotoRegular20(Colors.black87);

  static ShrineTheme of(BuildContext context) => context.inheritFromWidgetOfExactType(ShrineTheme);

  @override
  bool updateShouldNotify(ShrineTheme oldWidget) => false;
}
```

InheritedWidget 应用场景：全局主题替换，局部单例，全局单例，存储用户信息。存储全局变量

参考：

[https://flutterbyexample.com/state-management-the-flutter-way](https://flutterbyexample.com/state-management-the-flutter-way)
