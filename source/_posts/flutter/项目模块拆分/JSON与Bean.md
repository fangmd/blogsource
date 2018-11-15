---
title: JSON 与 Bean
date: 2018-09-11 13:18:12
tags: [Flutter]
category: Flutter

---

使用 `json_serializable`, `build_runner`, `json_annotation` 解决 JSON -> Model 的问题。

# 设置依赖

```
dependencies:
  json_annotation: ^1.1.0

dev_dependencies:
  build_runner: ^0.10.2
  json_serializable: ^1.2.0
```

# 创建一个 Model

`model/user_model.dart`

```
import 'package:json_annotation/json_annotation.dart';

part 'user_model.g.dart';

@JsonSerializable()
class UserModel {
  String name;
  int age;

  UserModel(this.name, this.age);

  factory UserModel.fromJson(Map<String, dynamic> json) =>
      _$UserModelFromJson(json);

  Map<String, dynamic> toJson() => _$UserModelToJson(this);
}
```

>写完后会报错，先不管

# 使用 build_runner 自动生成 JSON解析 相关的代码

创建 JSON 解析文件：在工程目录下执行下面命令

```
flutter packages pub run build_runner build
```

执行完成后，在 `model` 文件夹下会生成 `user_model.g.dart` 文件, `user_model.dart` 的报错会消失。

# 写个单元测试

在 `test` 文件夹下：

`json_test.dart`

```
import 'package:flutter_test/flutter_test.dart';
import 'dart:convert';

import 'package:flutter_app/model/user_model.dart';

void main() {
  String JSON_STRING = '''{"name":"fangmingdong","age":12}''';

  group('jsonparse test', () {

    test('to json', () {
      UserModel userModel = UserModel('fangmingdong', 12);
      String jsonStr = json.encode(userModel.toJson());
      print(jsonStr);
      expect(jsonStr, JSON_STRING);
    });

    test('mockdata test', () {
      UserModel userModel = UserModel.fromJson(json.decode(JSON_STRING));
      expect(userModel.age, 12);
      expect(userModel.name, 'fangmingdong');
    });
    
  });
}
```

# 工程级封装

通常接口设计都是以下面格式的：

```
{
  "code": 200,
  "msg": "success",
  "data":{
      ...
  }
}
```

Android 中通常使用泛型解析上面的 JSON:

```
public class BaseResponse<T>{
  int code;
  String msg;
  T data;
}
```

但是 `json_serializable` 并不支持泛型解析。

## 不太优雅的解决方案 继承 

解析：

```
{
  "code": 200,
  "msg": "success",
  "data": {
    "name": "fangmingdong",
    "age": 12,
    "book": {
      "id": 1,
      "name": "从你的全世界路过"
    }
  }
}
```


`base_response.dart`:

```
import 'package:json_annotation/json_annotation.dart';

part 'base_response.g.dart';

@JsonSerializable()
class BaseResponse{

  int code;
  String msg;

  BaseResponse(this.code, this.msg);

  factory BaseResponse.fromJson(Map<String, dynamic> json) =>
      _$BaseResponseFromJson(json);

  Map<String, dynamic> toJson() => _$BaseResponseToJson(this);

}
```

`user_model.dart`:

```
import 'package:flutter_app/model/base_response.dart';
import 'package:flutter_app/model/book_model.dart';
import 'package:json_annotation/json_annotation.dart';

part 'user_model.g.dart';

@JsonSerializable()
class UserModel extends BaseResponse {
  UserModelReal data;

  UserModel(this.data) : super(0, '');

  factory UserModel.fromJson(Map<String, dynamic> json) =>
      _$UserModelFromJson(json);

  Map<String, dynamic> toJson() => _$UserModelToJson(this);
}

@JsonSerializable()
class UserModelReal {
  String name;
  int age;
  BookModel book;

  UserModelReal(this.name, this.age, this.book);

  factory UserModelReal.fromJson(Map<String, dynamic> json) =>
      _$UserModelRealFromJson(json);

  Map<String, dynamic> toJson() => _$UserModelRealToJson(this);
}
```

test:

```
import 'package:flutter_test/flutter_test.dart';
import 'dart:convert';

import 'package:flutter_app/model/user_model.dart';
import 'package:flutter_app/model/book_model.dart';

void main() {
  String JSON_STRING = '''{"code":200,"msg":"success","data":{"name":"fangmingdong","age":12,"book":{"id":1,"name":"从你的全世界路过"}}}''';

  group('jsonparse test', () {

    test('to json', () {
      UserModel userModel = UserModel(new UserModelReal('fangmingdong', 12, BookModel(1, '从你的全世界路过')));
      String jsonStr = json.encode(userModel.toJson());
      print(jsonStr);
//      expect(jsonStr, JSON_STRING);
    });

    test('mockdata test', () {
      UserModel userModel = UserModel.fromJson(json.decode(JSON_STRING));
      expect(userModel.data.age, 12);
      expect(userModel.data.name, 'fangmingdong');
      expect(userModel.code, 200);
      expect(userModel.msg, "success");
    });
    
  });
}
```


参考：

- [https://juejin.im/post/5b5f00e7e51d45190571172f](https://juejin.im/post/5b5f00e7e51d45190571172f)

