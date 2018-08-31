---
title: JSBridge 原理
date: 2018-08-20 09:38:14
tags: [源码]
categories: android

---

项目地址：[https://github.com/lzyzsd/JsBridge](https://github.com/lzyzsd/JsBridge)

项目作用: Android 与 js 交互方案。

# 与 原生 JS 交互方式比有什么优点


# 目录结构和类介绍

```
- BridgeHandler
- BridgeUtil
- BridgeWebView
- BridgeWebViewClient
- CallBackFunction
- DefaultHandle
- Message
- WebViewJavascriptBridge
```

## WebViewJavascriptBridge

是一个接口，`BridgeWebView` 实现了 WebViewJavascriptBridge

```java
public interface WebViewJavascriptBridge {
    public void send(String data);
    public void send(String data, CallBackFunction responseCallback);
}
```

>这个地方的代码感觉作者没有写好, BridgeWebView 中实际起作用的方法是 doSend


## DefaultHandler, BridgeHandler

JS 调用原生方法的时候，Handler 接收来自 JS 传递的参数

DefaultHandler： 在没有设置 Handler 的时候会触发。

```java
public interface BridgeHandler {
    void handler(String data, CallBackFunction function);
}
```

## CallBackFunction

回调函数。

使用场景：在 app 方法被调起之后，app 可以通过这个方法 给JS传数据处理结果

>在 BridgeHandler 中使用


## Message

JS 与 Native 交互的信息封装类。

包含了：

1. callbackId：CallBackFunction id，通过这个 id 找到 CallBackFunction 函数
2. data: Native 调用 JS 是传递的参数
3. handlerName：暂时没有实际作用
4. responseId: native call js 时，返回值通过 CallBackFunction 函数返回，通过 responseId 找到 CallBackFunction。


```
成员变量：
private String callbackId; //callbackId
private String responseId; //responseId
private String responseData; //responseData
private String data; //data of message
private String handlerName; //name of handler

函数：
getter/setter
toJson
toObject
toArrayList
```


toJson: 将 Message 中的成员变量转化成 JSON 形式，使用场景：与 `javascript:WebViewJavascriptBridge._handleMessageFromNative('%s');` 拼接通过 `webView.loadUrl` 调用。

toArrayList: 清理事件队列时使用，String -> List<Message>, 获取 JS 端所有的事件一个一个处理。

## BridgeWebViewClient

>自定义WebViewClient必须要集成此类


`shouldOverrideUrlLoading`:

JS 与 Native 交互是通过 URL拦截 方式实现的。

在这里 JSBridge 会拦截属于自己需要处理的内容。

`onPageFinished`: 

向页面注入一段 JS 代码，内容在 `assets/WebViewJavascriptBridge.js` 中。

处理 `startupMessage` 中存储的事件。

## BridgeWebView

成员变量：

```java
Map<String, CallBackFunction> responseCallbacks = new HashMap<String, CallBackFunction>();
Map<String, BridgeHandler> messageHandlers = new HashMap<String, BridgeHandler>();
BridgeHandler defaultHandler = new DefaultHandler();
```


responseCallbacks： 存储 native call js 时，返回值 CallBackFunction 函数

messageHandlers: 存储所有 Handlers, BridgeHandler, 

defaultHandler: 默认 Handler

# 调用流程

## JS Call Native

Native 端要注册一个方法：

```
webView.registerHandler("methodName", (data, function) -> {
    try {
        JSONObject jsonObject = new JSONObject(data);
        jsCallBack.call(jsonObject.optString("tel"));
    } catch (JSONException e) {
        e.printStackTrace();
    }
});
```

注册 Handler，参数2 BridgeHandler 存储在 `messageHandlers` 中, 参数1 表示调用唯一表示。

JS 调用 Native 注册的方法：

```
NativePageController('callFunc', {'tel' : '18888888888'});
```

参数1 表示调用唯一表示，参数2 data 传递的数据。这些数据会转成 url 形式传递到native。

JS 调用 Native 注册的方法后，`shouldOverrideUrlLoading` 最先获取到 url 数据，解析 url，发现属于 js 调用方法就会开始处理。 `flushMessageQueue` 处理所有 JS 端出发的事件。

```java
    void flushMessageQueue() {
        if (Thread.currentThread() == Looper.getMainLooper().getThread()) {
            loadUrl(BridgeUtil.JS_FETCH_QUEUE_FROM_JAVA, new CallBackFunction() {

                @Override
                public void onCallBack(String data) {
                    // deserializeMessage 反序列化消息
                    List<Message> list = null;
                    try {
                        list = Message.toArrayList(data);
                    } catch (Exception e) {
                        e.printStackTrace();
                        return;
                    }
                    if (list == null || list.size() == 0) {
                        return;
                    }
                    for (int i = 0; i < list.size(); i++) {
                        Message m = list.get(i);
                        String responseId = m.getResponseId();
                        // 是否是response  CallBackFunction
                        if (!TextUtils.isEmpty(responseId)) {
                            CallBackFunction function = responseCallbacks.get(responseId);
                            String responseData = m.getResponseData();
                            function.onCallBack(responseData);
                            responseCallbacks.remove(responseId);
                        } else {
                            CallBackFunction responseFunction = null;
                            // if had callbackId 如果有回调Id
                            final String callbackId = m.getCallbackId();
                            if (!TextUtils.isEmpty(callbackId)) {
                                responseFunction = new CallBackFunction() {
                                    @Override
                                    public void onCallBack(String data) {
                                        Message responseMsg = new Message();
                                        responseMsg.setResponseId(callbackId);
                                        responseMsg.setResponseData(data);
                                        queueMessage(responseMsg);
                                    }
                                };
                            } else {
                                responseFunction = new CallBackFunction() {
                                    @Override
                                    public void onCallBack(String data) {
                                        // do nothing
                                    }
                                };
                            }
                            // BridgeHandler执行
                            BridgeHandler handler;
                            if (!TextUtils.isEmpty(m.getHandlerName())) {
                                handler = messageHandlers.get(m.getHandlerName());
                            } else {
                                handler = defaultHandler;
                            }
                            if (handler != null){
                                handler.handler(m.getData(), responseFunction);
                            }
                        }
                    }
                }
            });
        }
    }
```

1. data -> List<Message>, 生成事件集合
2. 通过 函数表示 从 messageHandlers 中找到对应的 Handler，调用 Handler 开始处理数据

> js 端多次调用 native 时事件会以 json 形式存储在 js 端，等待 app 去获取所有事件，依次处理


Native 处理事件后，通过如何返回参数：

```
Message responseMsg = new Message();
responseMsg.setResponseId(callbackId);
responseMsg.setResponseData(data);
queueMessage(responseMsg);
```



## Native Call JS

JS 端注册 Handler :

```java
NativePageController.registerHandler("Name", (data){

});
```

Native 端掉用 JS:

```java
webView.callHandler("Name", "{data...}", new CallBackFunction() {
    @Override
    public void onCallBack(String data) {
                
    }
});
```

调用 js 代码的时候：`doSend`

```java
    /**
     * 保存message到消息队列
     * @param handlerName handlerName
     * @param data data
     * @param responseCallback CallBackFunction
     */
    private void doSend(String handlerName, String data, CallBackFunction responseCallback) {
        Message m = new Message();
        if (!TextUtils.isEmpty(data)) {
            m.setData(data);
        }
        if (responseCallback != null) {
            String callbackStr = String.format(BridgeUtil.CALLBACK_ID_FORMAT, ++uniqueId + (BridgeUtil.UNDERLINE_STR + SystemClock.currentThreadTimeMillis()));
            responseCallbacks.put(callbackStr, responseCallback);
            m.setCallbackId(callbackStr);
        }
        if (!TextUtils.isEmpty(handlerName)) {
            m.setHandlerName(handlerName);
        }
        queueMessage(m);
    }
```

1. 生成 Message 对象，
2. responseCallbacks 中存储接收返回数据的 responseCallback
3. 开始处理, Message 生成 URL 调用。

```java
    /**
     * 分发message 必须在主线程才分发成功
     * @param m Message
     */
    void dispatchMessage(Message m) {
        String messageJson = m.toJson();
        //escape special characters for json string  为json字符串转义特殊字符
        messageJson = messageJson.replaceAll("(\\\\)([^utrn])", "\\\\\\\\$1$2");
        messageJson = messageJson.replaceAll("(?<=[^\\\\])(\")", "\\\\\"");
        String javascriptCommand = String.format(BridgeUtil.JS_HANDLE_MESSAGE_FROM_JAVA, messageJson);
        // 必须要找主线程才会将数据传递出去 --- 划重点
        if (Thread.currentThread() == Looper.getMainLooper().getThread()) {
            this.loadUrl(javascriptCommand);
        }
    }
```

JS 端处理完成后，返回数据：

```java
    /**
     * 获取到CallBackFunction data执行调用并且从数据集移除
     * @param url
     */
    void handlerReturnData(String url) {
        String functionName = BridgeUtil.getFunctionFromReturnUrl(url);
        CallBackFunction f = responseCallbacks.get(functionName);
        String data = BridgeUtil.getDataFromReturnUrl(url);
        if (f != null) {
            f.onCallBack(data);
            responseCallbacks.remove(functionName);
            return;
        }
    }
```

1. 从 URL 获取 函数标示
2. 通过 标示从 `responseCallbacks` 中获取 CallBackFunction
3. 从 URL 获取 data
4. CallBackFunction 调用并传 data 参数。
5. 移除 responseCallbacks 中对于的 CallBackFunction

# 总结

JSBridge 的设计方式可以扩展到其他的跨平台（需要两个平台都能作为服务端）互调情况下。

1. 两个平台之间信息交互通过 JSON
2. JSON 中包括：调用接口的唯一标示，data 数据，handler 名字（数据接收函数的唯一标示），接收返回数据函数的唯一标示。
3. 创建一个 Message 类存储 JSON 中的数据，方便处理

数据结构：

1. messageHandlers Map, Key 函数唯一标示，需要提前约定，调用的时候也需要
2. responseCallbacks Map，key 是动态生成的唯一标示






