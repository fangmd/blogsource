---
title: Toast
date: 2018-09-11 13:18:12
tags: [Flutter]
category: Flutter

---


使用第三方库 `fluttertoast`

`toast_utils.dart`

```
import 'package:fluttertoast/fluttertoast.dart';

class ToastUtils {
  static show(String msg) {
    Fluttertoast.showToast(msg: msg);
  }
}
```