---
title: Flutter 国际化 - InheritedWidget
date: 2018-09-12 13:18:12
tags: [Flutter]
category: Flutter

---


# 0 创建语言文件

`string_base.dart`:

```
abstract class StringBase {
  String welcomeMessage;
}
```

`string_en.dart`:

```
class StringEn extends StringBase {
  @override
  String welcomeMessage = "Welcome To Flutter";
}
```

`string_zh.dart`:

```
class StringZh extends StringBase {
  @override
  String welcomeMessage = "欢迎 To Flutter";
}
```


# 1 设置依赖

```
dependencies:
  flutter:
    sdk: flutter
  flutter_localizations:
    sdk: flutter
```

# 2 创建语言代理

`my_localizations_delegate.dart`

```
import 'dart:async';

import 'package:flutter/foundation.dart';
import 'package:flutter/material.dart';
import 'package:passon_movie/common/language/my_localizations.dart';


class MyLocalizationsDelegate
    extends LocalizationsDelegate<MyLocalizations> {
  MyLocalizationsDelegate();

  @override
  bool isSupported(Locale locale) {
    ///支持中文和英语
    return ['en', 'zh'].contains(locale.languageCode);
  }

  ///根据locale，创建一个对象用于提供当前locale下的文本显示
  @override
  Future<MyLocalizations> load(Locale locale) {
    return new SynchronousFuture<MyLocalizations>(
        new MyLocalizations(locale));
  }

  @override
  bool shouldReload(LocalizationsDelegate<MyLocalizations> old) {
    return false;
  }

  ///全局静态的代理
  static MyLocalizationsDelegate delegate = new MyLocalizationsDelegate();
}
```

语言包管理类：

`my_localizations.dart`:

```
import 'package:flutter/material.dart';
import 'package:passon_movie/common/language/string_base.dart';
import 'package:passon_movie/common/language/string_en.dart';
import 'package:passon_movie/common/language/string_zh.dart';

class MyLocalizations {
  final Locale locale;

  MyLocalizations(this.locale);

  ///根据不同 locale.languageCode 加载不同语言对应
  static Map<String, StringBase> _localizedValues = {
    'en': StringEN(),
    'zh': StringZH(),
  };

  StringBase get currentLocalized {
    return _localizedValues[locale.languageCode];
  }

  ///通过 Localizations 加载当前的 DefaultLocalizations
  ///获取对应的 StringBase
  static MyLocalizations of(BuildContext context) {
    return Localizations.of(context, MyLocalizations);
  }
}
```

# 3 创建一个工具类获取 String

`common_utils.dart`:

```
import 'package:flutter/material.dart';
import 'package:p_project/common/localization/my_localizations.dart';
import 'package:p_project/style/string_base.dart';

class CommonUtils {
  static StringBase getLocale(BuildContext context) {
    return MyLocalizations.of(context).currentLocalized;
  }
}
```

# 4 AppLocalizationContainer

`app_localization_container.dart`:

```
import 'package:flutter/material.dart';
import 'package:p_project/common/c.dart';

class AppLocalizationContainer extends StatefulWidget {
  final Widget child;

  @override
  State<StatefulWidget> createState() {
    return _AppLocalizationContainerState();
  }

  AppLocalizationContainer({@required this.child});

  static _AppLocalizationContainerState of(BuildContext context) {
    return (context.inheritFromWidgetOfExactType(
            _AppLocalizationInheritedWidget) as _AppLocalizationInheritedWidget)
        .data;
  }
}

class _AppLocalizationContainerState extends State<AppLocalizationContainer> {
  String locale = ZH;

  @override
  Widget build(BuildContext context) {
    return _AppLocalizationInheritedWidget(
      data: this,
      child: widget.child,
    );
  }

  void setLocal(String locale) {
    print('change launguage $locale');
    setState(() {
      this.locale = locale;
    });
  }
}

class _AppLocalizationInheritedWidget extends InheritedWidget {
  final _AppLocalizationContainerState data;

  _AppLocalizationInheritedWidget({
    Key key,
    @required this.data,
    @required Widget child,
  }) : super(key: key, child: child);

  @override
  bool updateShouldNotify(InheritedWidget oldWidget) => true;
}
```

# 5 AppRootWidget 与 main

`app.dart`:

```
import 'package:flutter/material.dart';
import 'package:p_project/app_localization_container.dart';
import 'package:p_project/common/localization/my_localizations_delegate.dart';
import 'package:p_project/page/home_page.dart';
import 'package:flutter_localizations/flutter_localizations.dart';

class AppRootWidget extends StatefulWidget {
  @override
  AppRootWidgetState createState() => new AppRootWidgetState();

  AppRootWidget();
}

class AppRootWidgetState extends State<AppRootWidget> {
  ThemeData get _themeData => new ThemeData(
        primaryColor: Colors.cyan,
        accentColor: Colors.indigo,
        scaffoldBackgroundColor: Colors.grey[300],
      );

  @override
  Widget build(BuildContext context) {
    var locale = AppLocalizationContainer.of(context).locale;
    return new MaterialApp(
      title: 'Inherited',
      debugShowCheckedModeBanner: false,
      theme: _themeData,
      localizationsDelegates: [
        GlobalMaterialLocalizations.delegate,
        GlobalWidgetsLocalizations.delegate,
        MyLocalizationsDelegate.delegate,
      ],
      supportedLocales: [
        const Locale('en', 'US'), // English
        const Locale('zh', 'Chinese'), // Hebrew
      ],
      locale: Locale(locale),
      routes: {
        '/': (_) => HomePage(),
      },
    );
  }
}
```

`home_page.dart`:

```
import 'package:flutter/material.dart';
import 'package:p_project/app_localization_container.dart';
import 'package:p_project/common/common_utils.dart';

class HomePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: SafeArea(
        child: Column(
          children: <Widget>[
            RaisedButton(
              onPressed: () {
                AppLocalizationContainer.of(context).setLocal('zh');
              },
              child: Text('change launge - zh'),
            ),
            RaisedButton(
              onPressed: () {
                AppLocalizationContainer.of(context).setLocal('en');
              },
              child: Text('change launge - en'),
            ),
            Text(CommonUtils.getLocale(context).welcomeMessage),
            Text(AppLocalizationContainer.of(context).locale),
          ],
        ),
      ),
    );
  }
}
```

`main.dart`:

```
void main() => runApp(AppLocalizationContainer(
      child: AppRootWidget(),
    ));
```


参考

- [官方文档](https://flutter.dev/docs/development/accessibility-and-localization/internationalization)
- [https://github.com/CarGuo/GSYGithubAppFlutter](https://github.com/CarGuo/GSYGithubAppFlutter)
- [https://flutterbyexample.com/state-management-the-flutter-way](https://flutterbyexample.com/state-management-the-flutter-way)
- [https://stackoverflow.com/questions/52077970/flutter-how-to-create-a-singleton-from-an-http-request](https://stackoverflow.com/questions/52077970/flutter-how-to-create-a-singleton-from-an-http-request)
