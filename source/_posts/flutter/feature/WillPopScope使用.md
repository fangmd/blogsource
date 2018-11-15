---
title: WillPopScope使用
date: 2018-09-12 13:18:12
tags: [Flutter]
category: Flutter

---

# 常用结构

Home 页面：

```
Scaffold
  - SafeArea
    - WillPopScope
```

>IOS 下会怎么样？？？

```
  const WillPopScope({
    Key key,
    @required this.child,
    @required this.onWillPop,
  }) : assert(child != null),
       super(key: key);
```

`WillPopScope` 创建的时候需要两个参数 `child`, `onWillPop`

`onWillPop` 就表示当前页面将退出。 值类型是一个函数 `typedef Future<bool> WillPopCallback();`

```
WillPopScope(
    onWillPop: (){
        print('onWillPop');
        return Future.value(false);
    },
    child: Container(
        child: Text('haha'),
    ),
),
```

返回 `Future.value(false);` 表示不退出.
返回 `Future.value(true);` 表示退出.

## Android 返回按钮点击两次退出应用

```
  int last = 0;

  Future<bool> doubleClickBack() {
    int now = DateTime.now().millisecond;
    if (now - last > 800) {
      last = DateTime.now().millisecond;
      return Future.value(false);
    } else {
      return Future.value(true);
    }
  }
```

```
onWillPop: doubleClickBack,
```


## 返回的时候提示弹窗

[https://stackoverflow.com/questions/49356664/how-to-override-the-back-button-in-flutter](https://stackoverflow.com/questions/49356664/how-to-override-the-back-button-in-flutter)


```
import 'dart:async';

import 'package:flutter/material.dart';

class HomePage extends StatefulWidget {
  HomePage({Key key, this.title}) :super(key: key);

  final String title;

  @override
  State<StatefulWidget> createState() => new _HomePageState();
}

class _HomePageState extends State<HomePage> {

  Future<bool> _onWillPop() {
    return showDialog(
      context: context,
      builder: (context) => new AlertDialog(
        title: new Text('Are you sure?'),
        content: new Text('Do you want to exit an App'),
        actions: <Widget>[
          new FlatButton(
            onPressed: () => Navigator.of(context).pop(false),
            child: new Text('No'),
          ),
          new FlatButton(
            onPressed: () => Navigator.of(context).pop(true),
            child: new Text('Yes'),
          ),
        ],
      ),
    ) ?? false;
  }

  @override
  Widget build(BuildContext context) {
    return new WillPopScope(
      onWillPop: _onWillPop,
      child: new Scaffold(
        appBar: new AppBar(
          title: new Text("Home Page"),
        ),
        body: new Center(
          child: new Text("Home Page"),
        ),
      ),
    );
  }
```






