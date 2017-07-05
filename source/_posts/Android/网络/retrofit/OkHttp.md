---
title: OKHttp
date: 2016-04-11 11:38:14
tags: OKHttp
categories: android

---


# OKHttp

## 基本使用

### 1. 创建客户端对象

	OkHttpClient client = new OkHttpClient();

<!--more-->
### 2. 创建请求对象
	final Request request = new Request.Builder().url("http://www.baidu.com").get().build(); // 网址和请求方式

#### 2.1 get

#### 2.2 post

	RequestBody requestBody = RequestBody.create(MediaType.parse("text/html"), "mode=rss"); // 可以传图片数组
	final Request requestPost = new Request.Builder().url("http://www.inexus.co/portal.php?mod=rss").post(requestBody).build(); 

### 3. 客户端载入请求
	mCall = client.newCall(request);

### 4. 客户端执行请求

#### 4.1 同步请求，在当前线程执行，可以直接获取请求结果

	Response response = mCall.execute();  
#### 4.2 异步请求，通过接口回调的方式放回请求结果

	mCall.enqueue(new Callback() {  // 

            // 下面两个方法，可以进行耗时操作，不能更新UI(不能Toast)

            /**
             * 失败回调，在非UI线程执行
             * @param call
             * @param e
             */
            @Override
            public void onFailure(Call call, IOException e) {

            }

            /**
             *
             * 成功回调，在非UI线程中执行
             * @param call
             * @param response
             * @throws IOException
             */
            @Override
            public void onResponse(Call call, Response response) throws IOException {
                if (response.code() == 200) {
                    ResponseBody body = response.body();
                    // 指定字符集取结果方法
					// String gbkStr = new String(body.bytes(), "GBK");
                    // 字符集为UTF-8 的字符串
                    String utfStr = body.string();
                    Log.d(TAG, "onResponse: utfStr" + utfStr);
					//Log.d(TAG, "onResponse:gbkStr " + gbkStr);
                    // 使用handler将数据反馈给主线程
                }
            }
        });

#### 5 结果处理 Response
>结果中的数据只能取出一次，不能重复取出



### 关闭请求
>在请求过程中用于退出了界面

    @Override
    protected void onDestroy() {
        super.onDestroy();
        mCall.cancel();
    }