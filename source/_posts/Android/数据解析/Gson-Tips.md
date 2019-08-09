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

# 一个字段自定义解析

由于后台返回数据格式变化：json `hot_left` 没有数据的时候属于数组，有数据的时候是对象。

下面可以解决这个问题

```java
public class OAJsonDe implements JsonDeserializer<ImgEntity> {

    @Override
    public ImgEntity deserialize(JsonElement json, Type typeOfT, JsonDeserializationContext context) throws JsonParseException {
        if (json.isJsonArray()) {
            return null;
        }
        Gson newGson = new Gson();
        return newGson.fromJson(json, typeOfT);
    }
}

//
@JsonAdapter(value = OAJsonDe.class)
@SerializedName(value = "hot_left")
public ImgEntity hotLeft;
```