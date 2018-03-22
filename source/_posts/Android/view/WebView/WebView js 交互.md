---
title: WebView js交互
date: 2016-05-15 11:38:14
tags: [WebView, javaScripe]
categories: android

---

# WebView js 交互


## js调用java代码

### 方法一

webview设置一个和js交互的接口（注意这里只是一般的意思，并不是java中接口的含义）

	mWebView.addJavascriptInterface(new DemoJavaScriptInterface(), "demo");

new  DemoJavaScriptInterface就是这个接口，demo就是这个接口的别名。

上面的代码执行之后在html的js中就能通过别名（这里是“demo”）来调用newDemoJavaScriptInterface类中的任何方法。

如我们想让html中的一个button点击之后调用java中的函数可以这样：

	<input type="button"  value="click me"  onclick="window.demo.clickOnAndroid()"/>

>但是因为安全问题，在Android4.2中(如果应用的android:targetSdkVersion数值为17+)JS只能访问带有 @JavascriptInterface注解的Java函数。因此如果你的开发版本比较高，需要在被调用的函数前加上@JavascriptInterface注解。

<!--more-->

google 的Demo：

WebViewDemo.java

	package com.google.android.webviewdemo;
	import android.app.Activity;
	import android.os.Bundle;
	import android.os.Handler;
	import android.util.Log;
	import android.webkit.JsResult;
	import android.webkit.WebChromeClient;
	import android.webkit.WebSettings;
	import android.webkit.WebView;
	/**
	 * Demonstrates how to embed a WebView in your activity. Also demonstrates how
	 * to have javascript in the WebView call into the activity, and how the activity
	 * can invoke javascript.
	 * <p>
	 * In this example, clicking on the android in the WebView will result in a call into
	 * the activities code in {@link DemoJavaScriptInterface#clickOnAndroid()}. This code
	 * will turn around and invoke javascript using the {@link WebView#loadUrl(String)}
	 * method.
	 * <p>
	 * Obviously all of this could have been accomplished without calling into the activity
	 * and then back into javascript, but this code is intended to show how to set up the
	 * code paths for this sort of communication.
	 *
	 */
	public class WebViewDemo extends Activity {
	    private static final String LOG_TAG = "WebViewDemo";
	    private WebView mWebView;
	    private Handler mHandler = new Handler();
	    @Override
	    public void onCreate(Bundle icicle) {
		super.onCreate(icicle);
		setContentView(R.layout.main);
		mWebView = (WebView) findViewById(R.id.webview);
		WebSettings webSettings = mWebView.getSettings();
		webSettings.setSavePassword(false);
		webSettings.setSaveFormData(false);
		webSettings.setJavaScriptEnabled(true);
		webSettings.setSupportZoom(false);
		mWebView.setWebChromeClient(new MyWebChromeClient());
		mWebView.addJavascriptInterface(new DemoJavaScriptInterface(), "demo");
		mWebView.loadUrl("file:///android_asset/demo.html");
	    }
	    final class DemoJavaScriptInterface {
		DemoJavaScriptInterface() {
		}
		/**
		 * This is not called on the UI thread. Post a runnable to invoke
		 * loadUrl on the UI thread.
		 */
		public void clickOnAndroid() {
		    mHandler.post(new Runnable() {
		        public void run() {
		            mWebView.loadUrl("javascript:wave()");
		        }
		    });
		}
	    }
	    /**
	     * Provides a hook for calling "alert" from javascript. Useful for
	     * debugging your javascript.
	     */
	    final class MyWebChromeClient extends WebChromeClient {
		@Override
		public boolean onJsAlert(WebView view, String url, String message, JsResult result) {
		    Log.d(LOG_TAG, message);
		    result.confirm();
		    return true;
		}
	    }
	}

demo.html

	<html>
	    <script language="javascript">
		/* This function is invoked by the activity */
		function wave() {
		    alert("1");
		    document.getElementById("droid").src="android_waving.png";
		    alert("2");
		}
	    </script>
	    <body>
		<!-- Calls into the javascript interface for the activity -->
		<a onClick="window.demo.clickOnAndroid()"><div style="width:80px;
		    margin:0px auto;
		    padding:10px;
		    text-align:center;
		    border:2px solid #202020;" >
		        <img id="droid" src="android_normal.png"/><br>
		        Click me!
		</div></a>
	    </body>
	</html>

main.xml：

	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
	    android:orientation="vertical"
	    android:layout_width="fill_parent"
	    android:layout_height="fill_parent"
	    >
		                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             
	    <TextView
		android:layout_width="fill_parent"
		android:layout_height="wrap_content"
		android:text="@string/intro"
		android:padding="4dip"
		android:textSize="16sp"
		/>
		                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             
	    <WebView
		android:id="@+id/webview"
		android:layout_width="fill_parent"
		android:layout_height="0dip"
		android:layout_weight="1"
		/>
		                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 
	</LinearLayout>

###　方法二
JS端与Native端存在一个伪协议，Native端口根据这个协议去侦听/截获页面的相关行为。所以，我们首先需要定义一个协议（可参考上面的javascript:伪协议）：协议名+方法名+相关参数。在本文中，我们假定该协议格式为："jsbridge://" + "method" + "jsonParams"，整个协议就是个特殊的字符串。之后我们要做的工作是把这个字符串从JS端传到Native端，然后Native去解析这个字符串并执行相关代码。这其中的关键就是如何传这个字符串，方法有很多，我们一个一个来看：

#### shouldOverrideUrlLoading
我们将通过参数String url来传递我们协议字符串，所以在Native端我们创建设置WebViewClient子类，该子类覆写shouldOverrideUrlLoading方法，这个就可以拦截Webview加载新url了。那么在JS端该如何生成这个url呢？一般我们可以创建一个iframe，设置它的src属性，并将其添加到页面的文档流中，或者直接设置window.location.href。相关代码如下：

	// 方式(1) 直接设置window.location.href
	window.location.href = "jsbridge://toast?{msg:jstojava}";
	// 方式(2) 在需要js调用native api的时候,js在页面中创建一个不可见的iframe,设置这个iframe的地址
	var iframe = document.createElement("iframe");
	iframe.style.display = "none";
	document.documentElement.appendChild(iframe);
	iframe.src = "jsbridge://toast?{msg:jstojava}";

#### prompt，console.log，alert

1. prompt：默认显示一个对话框，对话框中包含一条文字信息，用来提示用户输入文字；
2. console.log：默认向web控制台输出一条消息；
3. alert：默认用于显示带有一条指定消息和一个 OK 按钮的警告框。

对于上述三个方法的默认行为，大家可通过chrome的开发者工具试试，调用方式非常简单。所以，我们只要能拦截这三个方法的默认行为并获得其中的参数即可。而Android中的类WebChromeClient确实存在相对应的方法来处理，只要覆写WebChromeClient中相对应的三个方法，并设置Webview。下面是这三个方法的方法声明，要注意的是这三个方法的参数差异是有点大，具体使用那个参数可能需要与JS端配合：

	class WebChromeClientImp extends WebChromeClient {
	    @Override
	    public boolean onJsPrompt(WebView view, String url, String message, String defaultValue, JsPromptResult result) {
	    }

	    @Override
	    public boolean onConsoleMessage(ConsoleMessage consoleMessage) {
	    }

	    @Override
	    public boolean onJsAlert(WebView view, String url, String message, JsResult result) {
	    }
	}


## WebView 调用JS代码

### 0
按照上面的Demo

	mWebView.loadUrl("javascript:wave()");

其中wave（）是js中的一个方法，当然你可以把这个方法改成其他的方法，也就是android调用其他的方法。


Demo流程：

1. 点击图片，则在 js 端直接调用 android 上的方法clickOnAndroid()；

2. clickOnAndroid()方法（利用线程）调用js的方法。

3. 被②调用的js直接控制html。


### 1

	WebSettings webSettings = mWebView .getSettings();
	webSettings.setJavaScriptEnabled(true);

	mWebView.addJavascriptInterface(new InsertObj(), "jsObj");

*上面这是前提！！！*

然后实现上面的类，这个类提供了四个方法，注释的非常清楚。


	class InsertObj extends Object {
	    //给html提供的方法，js 中可以通过：var str = window.jsObj.HtmlcallJava(); 获取到
	    @JavascriptInterface
	    public String HtmlcallJava() {
		return "Html call Java";
	    }

	    //给html提供的有参函数 ： window.jsObj.HtmlcallJava2("IT-homer blog");
	    @JavascriptInterface
	    public String HtmlcallJava2(final String param) {
		return "Html call Java : " + param;
	    }

	    //Html给我们提供的函数
	    @JavascriptInterface
	    public void JavacallHtml() {
		runOnUiThread(new Runnable() {
		    @Override
		    public void run() {
		        //这里是调用方法
		        mWebView.loadUrl("javascript: showFromHtml()");
		        Toast.makeText(Html5Activity.this, "clickBtn", Toast.LENGTH_SHORT).show();
		    }
		});
	    }

	    //Html给我们提供的有参函数
	    @JavascriptInterface
	    public void JavacallHtml2(final String param) {
		runOnUiThread(new Runnable() {
		    @Override
		    public void run() {
		        mWebView.loadUrl("javascript: showFromHtml2('IT-homer blog')");
		        Toast.makeText(Html5Activity.this, "clickBtn2", Toast.LENGTH_SHORT).show();
		    }
		});
	    }
	}


Android 调用js有个漏洞：
[http://blog.csdn.net/leehong2005/article/details/11808557](http://blog.csdn.net/leehong2005/article/details/11808557)

转自：[http://xiroid.com/post/android/androidzhong-javayu-javascriptjiao-hu-de-ji-chong-fang-shi](http://xiroid.com/post/android/androidzhong-javayu-javascriptjiao-hu-de-ji-chong-fang-shi)


