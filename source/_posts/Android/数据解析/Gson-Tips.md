---
title: Gson Tips
date: 2016-06-24 21:18:12
tags: [Gson, 数据解析]
category: android

---


# 为 POJO 字段提供备选属性名

```
@SerializedName(value = "emailAddress", alternate = {"email", "email_address"})
public String emailAddress;
```

同时匹配多个字段