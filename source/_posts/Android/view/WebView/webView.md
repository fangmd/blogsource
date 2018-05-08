---
title: WebView
date: 2016-05-15 11:38:14
tags: WebView
categories: android

---

# WebView 属性的设置

## 通过 `WebSettings` 设置

<!--more-->


    // 支持获取手势焦点，输入用户名、密码或其他
    webview.requestFocusFromTouch();
    setNeedInitialFocus(true); //当webview调用requestFocus时为webview设置节点
    setLoadsImagesAutomatically(true);  //支持自动加载图片


```java
WebSettings webSettings = mWebView.getSettings();

webSettings.setJavaScriptEnabled(true);  // 是否允许js脚本运行

webSettings.setJavaScriptCanOpenWindowsAutomatically(true); //支持通过JS打开新窗口

webSettings.setPluginsEnabled(true);  //支持插件

webSettings.setDefaultTextEncodingName("utf-8");//设置编码格式

webSettings.setCacheMode(WebSettings.LOAD_DEFAULT); // 设置缓存模式， 关闭webview中缓存：WebSettings.LOAD_CACHE_ELSE_NETWORK 

webSettings.setDomStorageEnabled(true); 

webSettings.setDatabaseEnabled(true); // 是否允许使用数据库api

webSettings.setAppCacheEnabled(true); // 设置H5的缓存是否打开，默认关闭。

webSettings.setAllowFileAccess(true); // 是否可以进行文件操作

webSettings.setSavePassword(true); // 是否密码保存

webSettings.setSupportZoom(true); //页面是否支持放大和缩小

webSettings.setDisplayZoomControls(false); //隐藏原生的缩放控件

webSettings.supportMultipleWindows();  //多窗口 

webSettings.setBuiltInZoomControls(true);

webSettings.setLayoutAlgorithm(WebSettings.LayoutAlgorithm.NARROW_COLUMNS); // 1、LayoutAlgorithm.NARROW_COLUMNS ：适应内容大小  2、LayoutAlgorithm.SINGLE_COLUMN : 适应屏幕，内容将自动缩放

webSettings.setUseWideViewPort(true); 

//设置自适应屏幕，两者合用
webSettings.setUseWideViewPort(true);  //将图片调整到适合webview的大小 
webSettings.setLoadWithOverviewMode(true); // 缩放至屏幕的大小
//若上面是false，则该WebView不可缩放，这个不管设置什么都不能缩放。

mWebView.setScrollBarStyle(WebView.SCROLLBARS_OUTSIDE_OVERLAY);

mWebView.setHorizontalScrollbarOverlay(true);

mWebView.setHorizontalScrollBarEnabled(true);

mWebView.requestFocus();
```

## 设置 WebChromeClient 子类

>WebChromeClient 会在一些影响浏览器ui交互动作发生时被调用，比如WebView关闭和隐藏、页面加载进展、js确认框和警告框、js加载前、js操作超时、webView获得焦点等等

```java
mWebView.setWebChromeClient(new MyWebChromeClient());
```

### 显示页面加载进度

```java
    @Override
    public void onProgressChanged(WebView webView, int progress) {
        MainActivity.this.setTitle("Loading...");
        MainActivity.this.setProgress(progress * 100);
        if(progress == 100)
            MainActivity.this.setTitle("My title");
    }
```

## 设置WebViewClient子类

>WebViewClient 会在一些影响内容渲染的动作发生时被调用，比如表单的错误提交需要重新提交、页面开始加载及加载完成、资源加载中、接收到https认证需要处理、页面键盘响应、页面中的url打开处理等等

```java
mWebView.setWebViewClient(new MyWebViewClient());
```


### 设置当前网页的链接仍在 WebView 中跳转，而不是跳到手机浏览器里显示

```java
    @Override
    public boolean shouldOverrideUrlLoading(WebView view, String url) {
        view.loadUrl(url);
        return true;
    }
```

>@return True if the host application wants to leave the current WebView and handle the url itself, otherwise return false.

### 设置开始加载网页、加载完成、加载错误时处理


```java
    @Override
    public void onPageStarted(WebView webView, String s, Bitmap bitmap) {
        super.onPageStarted(webView, s, bitmap);

        // DialogManager.showLoadingDialog(this);

    }

    @Override
    public void onPageFinished(WebView webView, String s) {
        super.onPageFinished(webView, s);

        // 网页加载完成时处理  如：让 加载对话框 消失
        // DialogManager.dismissLoadingDialog();
    }

    @Override
    public void onReceivedError(WebView webView, int i, String s, String s1) {
        super.onReceivedError(webView, i, s, s1);

        // 加载网页失败时处理  如：
        webView.loadDataWithBaseURL(null,"<span style=\"\"color:#FF0000\"\">网页加载失败</span>", "text/html", "utf-8", null);
    }
```

#### WebView 在Android4.4的手机上onPageFinished()回调会多调用一次

需要尽量避免在onPageFinished()中做业务操作，否则会导致重复调用，还有可能会引起逻辑上的错误.

### 处理https请求，为WebView处理ssl证书设置

```java
    @Override
    public void onReceivedSslError(WebView webView, SslErrorHandler sslErrorHandler, SslError sslError) {

    }
```

# 前进 后退

相关方法：
```java
	goBack()//后退
	goForward()//前进
	goBackOrForward(intsteps) //以当前的index为起始点前进或者后退到历史记录中指定的steps， 如果steps为负数则为后退，正数则为前进

	canGoForward()//是否可以前进
	canGoBack() //是否可以后退
```

## 实现方法一

```java
        mWV.setOnKeyListener(new View.OnKeyListener() {
            @Override
            public boolean onKey(View v, int keyCode, KeyEvent event) {
                if (event.getAction() == KeyEvent.ACTION_DOWN) {
                    if (keyCode == KeyEvent.KEYCODE_BACK && mWV.canGoBack()) {  //表示按返回键
                        mWV.goBack();   //后退
                        return true;    //已处理
                    }
                }
                return false;
            }
        });
```

## 实现方法二

Avtivity 层处理：

```java
    public boolean onKeyDown(int keyCode, KeyEvent event) {       
        if ((keyCode == KeyEvent.KEYCODE_BACK) && mWebView.canGoBack()) {       
        mWebView.goBack();       
        return true;       
        }       
        return super.onKeyDown(keyCode, event);       
    }
```

# WebView 与 js 交互

## 使用 addJavascriptInterface 完成和 js 交互

>使 Js 调用 Native 本地 Java 对象,实现本地 Java 代码和 HTML 页面进行交互，
注意: 因为安全问题的考虑 Google 在使用 Android API 17以上的版本的时候 需要通过 @JavascriptInterface 来注解的 Java 函数才能被识别可以被 Js 调用。

### Js 中调 Native 本地 Java 方法

设置webView的addJavascriptInterface方法，该方法有两个参数，第一个参数为被绑定到js中的类实例，第二个参数为在js中暴露的类别名，在js中引用java对象就是用这个名字：

```java
mWebView.addJavascriptInterface(new JavaScriptInterface(this), "Android");

class JavaScriptInterface{

    Context mContext;

    /** Instantiate the interface and set the context */
    JavaScriptInterface(Context c) {
        mContext = c;
    }

    /**
    *Show a toast from the web page 
    *由Js调用执行Native本地Java方法
    */
    @JavascriptInterface
    public void showToast(String toast) {
        Log.d("TAG","Js Invoker Native Function");
        Toast.makeText(mContext, toast, Toast.LENGTH_SHORT).show();
    }

}
```

在HTML中Js调用Native方法 代码如下：

```html
<input
 type="button" value="Say hello" onclick="showAndroidToast('Hello Android!')">

<script type="text/javascript">

    function showAndroidToast(toast) {
        Android.showToast(toast);
    }

</script>
```

### Java调Js方法

比如在HTML中有如下Js函数:

```html
<script type="text/javascript">

    function showAlert() {
        alert("Be executed by Native");
    }

</script>
```

在Native调Js方法如下：
```java
mWebView.loadUrl("javascript：showAlert()");
```


# WebView 缓存模式的设置

## 网页数据缓存

当使用 WebView 加载 HTML 网页时，会在我们data/应用package下生成database与cache两个文件夹: 我们请求的Url记录是保存在webviewCache.db里，而url的内容是保存在webviewCache文件夹下.

五种缓存模式：

1. LOAD_CACHE_ONLY：不使用网络，只读取本地缓存数据
2. LOAD_DEFAULT：根据cache-control决定是否从网络上取数据
3. LOAD_CACHE_NORMAL：API level 17中已经废弃, 从API level 11开始作用同LOAD_DEFAULT模式
4. LOAD_NO_CACHE：不使用缓存，只从网络获取数据
5. LOAD_CACHE_ELSE_NETWORK：只要本地有，无论是否过期，或者no-cache，都使用缓存中的数据

```java
//设置 缓存模式  
webSettings.setCacheMode(WebSettings.LOAD_DEFAULT); 
// 开启 DOM storage API 功能  
webSettings.setDomStorageEnabled(true); 
//开启 database storage API 功能  
webSettings.setDatabaseEnabled(true);
```

## H5缓存

- 通过`setAppCacheEnabled(boolean flag)`设置H5的缓存是否打开，默认关闭。
- 根据`setAppCachePath(String appCachePath)`提供的路径，在H5使用缓存过程中生成的缓存文件。
- 通过`setAppCacheMaxSize(long appCacheMaxSize)`设置缓存最大容量。

```java
String cacheDirPath = getCacheDir().getAbsolutePath()+ "/webViewCache";
WebSettings webSettings = mWebView.getSettings();
//开启 database storage API 功能  
webSettings.setDatabaseEnabled(true);   
//设置数据库缓存路径 
webSettings.setDatabasePath(cacheDirPath);
//开启Application H5 Caches 功能  
webSettings.setAppCacheEnabled(true);
//设置Application Caches 缓存目录  
webSettings.setAppCachePath(cacheDirPath);
```

# 加快HTML网页加载完成速度

默认情况html代码下载到WebView后，webkit开始解析网页各个节点，发现有外部样式文件或者外部脚本文件时，会异步发起网络请求下载文件，但如果在这之前也有解析到image节点，那势必也会发起网络请求下载相应的图片。在网络情况较差的情况下，过多的网络请求就会造成带宽紧张，影响到css或js文件加载完成的时间，造成页面空白loading过久。解决的方法就是告诉WebView先不要自动加载图片，等页面finish后再发起图片加载。


初始化 WebView 的时候: 禁止自动加载图片，等页面加载成功后再开始加载图片
```java
    if (Build.VERSION.SDK_INT >= 19) {
        mWV.getSettings().setLoadsImagesAutomatically(true);
    } else {
        mWV.getSettings().setLoadsImagesAutomatically(false);
    }
```

重写 `onPageFinished()`:
```java
    @Override
    public void onPageFinished(WebView webView, String s) {
        super.onPageFinished(webView, s);

        // 网页加载完成时处理  如：让 加载对话框 消失
        // DialogManager.dismissLoadingDialog();

        // 优化 html 加载
        if (!webView.getSettings().getLoadsImagesAutomatically()) {
            webView.getSettings().setLoadsImagesAutomatically(true);
        }
    }
```

>从上面的代码，可以看出我们对系统API在19以上的版本作了兼容。因为4.4以上系统在onPageFinished时再恢复图片加载时,如果存在多张图片引用的是相同的src时，会只有一个image标签得到加载，因而对于这样的系统我们就先直接加载。


# WebView 硬件加速导致页面渲染闪烁问题解决方法

关于Android硬件加速 开始于Android 3.0 (API level 11),在四个级别上开启/关闭硬件加速

1. Application级别：为整个应用程序开启硬件加速，在AndroidManifest中加入如下配置
2. Activity级别：控制每个activity是否开启硬件加速，只需在activity元素中添加android:hardwareAccelerated属性即可
3. Window级别：注：目前还不支持在Window级别上关闭硬件加速

    ```java
    getWindow().setFlags(
        WindowManager.LayoutParams.FLAG_HARDWARE_ACCELERATED，
        WindowManager.LayoutParams.FLAG_HARDWARE_ACCELERATED);
    ```
4. View级别：运行时单个view硬件加速，目前Android还不支持在View级别开启硬件加速 代码如下：

    ```java
    mView.setLayerType(View.LAYER_TYPE_SOFTWARE, null);
    ```

我们开启硬件加速后，WebView渲染页面更加快速，拖动也更加顺滑。但有个副作用就是容易会出现页面加载白块同时界面闪烁现象。解决这个问题的方法是设置WebView暂时关闭硬件加速 代码如下：

```java
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.HONEYCOMB) {
    webview.setLayerType(View.LAYER_TYPE_SOFTWARE, null);
}
```

# 为WebView自定义错误显示界面

```java
            /**
             * 显示自定义错误提示页面，用一个View覆盖在WebView 
             */
            protected void showErrorPage() {
                LinearLayout webParentView = (LinearLayout) mWebView.getParent();

                initErrorPage();
                while (webParentView.getChildCount() > 1) {
                    webParentView.removeViewAt(0);
                }
                LinearLayout.LayoutParams lp = new LinearLayout.LayoutParams(LinearLayout.LayoutParams.FILL_PARENT, LinearLayout.LayoutParams.FILL_PARENT);
                webParentView.addView(mErrorView, 0, lp);
                mIsErrorPage = true;
            }

            protected void hideErrorPage() {
                LinearLayout webParentView = (LinearLayout) mWebView.getParent();

                mIsErrorPage = false;
                while (webParentView.getChildCount() > 1) {
                    webParentView.removeViewAt(0);
                }
            }


            protected void initErrorPage() {
                if (mErrorView == null) {
                    mErrorView = View.inflate(this, R.layout.online_error, null);
                    Button button = (Button) mErrorView.findViewById(R.id.online_error_btn_retry);
                    button.setOnClickListener(new View.OnClickListener() {
                        public void onClick(View v) {
                            mWebView.reload();
                        }
                    });
                    mErrorView.setOnClickListener(null);
                }
            }
```


# WebView cookies清理：

```java
CookieSyncManager.createInstance(this);   
CookieSyncManager.getInstance().startSync();   
CookieManager.getInstance().removeSessionCookie();   
```

# 清理cache 和历史记录

```java
webView.clearCache(true); //清除网页访问留下的缓存，由于内核缓存是全局的因此这个方法不仅仅针对webview而是针对整个应用程序.
webView.clearHistory();  //清除当前webview访问的历史记录，只会webview访问历史记录里的所有记录除了当前访问记录.
webView.clearFormData()//这个api仅仅清除自动完成填充的表单数据，并不会清除WebView存储到本地的数据。
``

# 判断WebView是否已经滚动到页面底端

- getScrollY()方法返回的是当前可见区域的顶端距整个页面顶端的距离,也就是当前内容滚动的距离.   
- getHeight()或者getBottom()方法都返回当前WebView 这个容器的高度   
- getContentHeight 返回的是整个html 的高度,但并不等同于当前整个页面的高度,因为WebView 有缩放功能, 所以当前整个页面的高度实际上应该是原始html 的高度再乘上缩放比例. 

*准确方法：*

```java
    if (webView.getContentHeight() * webView.getScale() == (webView.getHeight() + webView.getScrollY())) {
        //已经处于底端
    }

    if(webView.getScrollY() == 0){
        //处于顶端
    }
```


# 屏蔽掉长按事件 因为webview长按时将会调用系统的复制控件

```java
mWebView.setOnLongClickListener(new OnLongClickListener() {  
            
    @Override  
    public boolean onLongClick(View v) {  
        return true;  
    }  
}); 
```

# 在WebView加入 flash支持

```java
String temp = "<html><body bgcolor=\"" + "black"  
                + "\"> <br/><embed src=\"" + url + "\" width=\"" + "100%"  
                + "\" height=\"" + "90%" + "\" scale=\"" + "noscale"  
                + "\" type=\"" + "application/x-shockwave-flash"  
                + "\"> </embed></body></html>";  
String mimeType = "text/html";  
String encoding = "utf-8";  
web.loadDataWithBaseURL("null", temp, mimeType, encoding, "");  
```

# WebView保留缩放功能但隐藏缩放控件:

```java
mWebView.getSettings().setSupportZoom(true);  
mWebView.getSettings().setBuiltInZoomControls(true);  
if (DeviceUtils.hasHoneycomb())  
    mWebView.getSettings().setDisplayZoomControls(false);  
```

# 正确关闭 WebView 同时解决 WebView页面中播放了音频,退出Activity后音频仍然在播放

Activity `onDestory` 方法中执行下面代码

```java
    @Override
    protected void onDestroy() {
        if (mWebView != null) {
            mWebView.loadDataWithBaseURL(null, "", "text/html", "utf-8", null);
            mWebView.clearHistory();

            ((ViewGroup) mWebView.getParent()).removeView(mWebView);
            mWebView.destroy();
            mWebView = null;
        }
        super.onDestroy();
    }
```

# 加载网页方式

```java
    //加载一个网页：
    webView.loadUrl("http://www.google.com/");
    //加载apk包中的一个html页面
    webView.loadUrl("file:///android_asset/test.html");
    //加载手机本地的一个html页面的方法：
    webView.loadUrl("content://com.android.htmlfileprovider/sdcard/test.html");
```

#　WebView的状态：

```java
    onResume() //激活WebView为活跃状态，能正常执行网页的响应
    onPause()//当页面被失去焦点被切换到后台不可见状态，需要执行onPause动过， onPause动作通知内核暂停所有的动作，比如DOM的解析、plugin的执行、JavaScript执行。

    pauseTimers()//当应用程序被切换到后台我们使用了webview， 这个方法不仅仅针对当前的webview而是全局的全应用程序的webview，它会暂停所有webview的layout，parsing，javascripttimer。降低CPU功耗。
    resumeTimers()//恢复pauseTimers时的动作。

    destroy()//销毁，关闭了Activity时，音乐或视频，还在播放。就必须销毁。
```

但是注意：webview调用destory时,webview仍绑定在Activity上.这是由于自定义webview构建时传入了该Activity的context对象,因此需要先从父容器中移除webview,然后再销毁webview:

```java
      rootLayout.removeView(webView);
      webView.destroy();
```

# 避免WebView内存泄露的一些方式

1. 可以将 Webview 的 Activity 新起一个进程，结束的时候直接System.exit(0);退出当前进程；

启动新进程，主要代码： AndroidManifest.xml 配置文件代码如下

        <activity
        android:name=".ui.activity.Html5Activity"
        android:process=":lyl.boon.process.web">
        <intent-filter>
            <action android:name="com.lyl.boon.ui.activity.htmlactivity"/>
            <category android:name="android.intent.category.DEFAULT"/>
        </intent-filter>
        </activity>

在新进程中启动 Activity ，里面传了 一个 Url：

        Intent intent = new Intent("com.lyl.boon.ui.activity.htmlactivity");
        Bundle bundle = new Bundle();
        bundle.putString("url", gankDataEntity.getUrl());
        intent.putExtra("bundle",bundle);
        startActivity(intent);

然后在 Html5Activity 的 onDestory() 最后加上 System.exit(0); 杀死当前进程。

2. 不能在xml中定义 Webview ，而是在需要的时候创建，并且Context使用 getApplicationgContext()，如下代码：

        LinearLayout.LayoutParams params = new LinearLayout.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.MATCH_PARENT);
        mWebView = new WebView(getApplicationContext());
        mWebView.setLayoutParams(params);
        mLayout.addView(mWebView);

3. 在 Activity 销毁的时候，可以先让 WebView 加载null内容，然后移除 WebView，再销毁 WebView，最后置空。

        @Override
        protected void onDestroy() {
        if (mWebView != null) {
            mWebView.loadDataWithBaseURL(null, "", "text/html", "utf-8", null);
            mWebView.clearHistory();

            ((ViewGroup) mWebView.getParent()).removeView(mWebView);
            mWebView.destroy();
            mWebView = null;
        }
        super.onDestroy();
        }



------








## WebViewClient
>WebViewClient就是帮助WebView处理各种通知、请求事件的。


### WebViewClient 方法
	WebViewClient mWebViewClient = new WebViewClient()
	{
	    shouldOverrideUrlLoading(WebView view, String url)  最常用的，比如上面的。
	    //在网页上的所有加载都经过这个方法,这个函数我们可以做很多操作。
	    //比如获取url，查看url.contains(“add”)，进行添加操作

	    shouldOverrideKeyEvent(WebView view, KeyEvent event)
	    //重写此方法才能够处理在浏览器中的按键事件。 

	    onPageStarted(WebView view, String url, Bitmap favicon) 
	    //这个事件就是开始载入页面调用的，我们可以设定一个loading的页面，告诉用户程序在等待网络响应。 

	    onPageFinished(WebView view, String url)
	    //在页面加载结束时调用。同样道理，我们可以关闭loading 条，切换程序动作。 

	    onLoadResource(WebView view, String url) 
	    // 在加载页面资源时会调用，每一个资源（比如图片）的加载都会调用一次。 

	    onReceivedError(WebView view, int errorCode, String description, String failingUrl)
	    // (报告错误信息) 

	    doUpdateVisitedHistory(WebView view, String url, boolean isReload)  
	    //(更新历史记录) 

	    onFormResubmission(WebView view, Message dontResend, Message resend) 
	    //(应用程序重新请求网页数据) 

	    onReceivedHttpAuthRequest(WebView view, HttpAuthHandler handler, String host,String realm)
	    //（获取返回信息授权请求） 

	    onReceivedSslError(WebView view, SslErrorHandler handler, SslError error) 
	    //重写此方法可以让webview处理https请求。

	    onScaleChanged(WebView view, float oldScale, float newScale) 
	    // (WebView发生改变时调用) 

	    onUnhandledKeyEvent(WebView view, KeyEvent event) 
	    //（Key事件未被加载时调用） 
	}

将上面定义的WebViewClient设置给WebView：

	webView.setWebViewClient(mWebViewClient);

## WebChromeClient

>WebChromeClient是辅助WebView处理Javascript的对话框，网站图标，网站title，加载进度等 :
方法中的代码都是由Android端自己处理。


	WebChromeClient mWebChromeClient = new WebChromeClient() {


	    //获得网页的加载进度，显示在右上角的TextView控件中
	    @Override
	    public void onProgressChanged(WebView view, int newProgress) {
		if (newProgress < 100) {
		    String progress = newProgress + "%";
		} else {
		}
	    }

	    //获取Web页中的title用来设置自己界面中的title
	    //当加载出错的时候，比如无网络，这时onReceiveTitle中获取的标题为 找不到该网页,
	    //因此建议当触发onReceiveError时，不要使用获取到的title
	    @Override
	    public void onReceivedTitle(WebView view, String title) {
		MainActivity.this.setTitle(title);
	    }

	    @Override
	    public void onReceivedIcon(WebView view, Bitmap icon) {
		//
	    }

	    @Override
	    public boolean onCreateWindow(WebView view, boolean isDialog, boolean isUserGesture, Message resultMsg) {
		//
		return true;
	    }

	    @Override
	    public void onCloseWindow(WebView window) {
	    }

	    //处理alert弹出框，html 弹框的一种方式
	    @Override
	    public boolean onJsAlert(WebView view, String url, String message, JsResult result) {
		//
		return true;
	    }

	    //处理confirm弹出框
	    @Override
	    public boolean onJsPrompt(WebView view, String url, String message, String defaultValue, JsPromptResult 
		    result) {
		//
		return true;
	    }

	    //处理prompt弹出框
	    @Override
	    public boolean onJsConfirm(WebView view, String url, String message, JsResult result) {
		//
		return true;
	    }
	};

同样，将上面定义的WebChromeClient设置给WebView：

	webView.setWebChromeClient(mWebChromeClient);


## 一个Activity实例

public class Html5Activity extends AppCompatActivity {

    private String mUrl;

    private LinearLayout mLayout;
    private WebView mWebView;


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_web);

        Bundle bundle = getIntent().getBundleExtra("bundle");
        mUrl = bundle.getString("url");

        Log.d("Url:", mUrl);

        mLayout = (LinearLayout) findViewById(R.id.web_layout);


        LinearLayout.LayoutParams params = new LinearLayout.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.MATCH_PARENT);
        mWebView = new WebView(getApplicationContext());
        mWebView.setLayoutParams(params);
        mLayout.addView(mWebView);

        WebSettings mWebSettings = mWebView.getSettings();
        mWebSettings.setSupportZoom(true);
        mWebSettings.setLoadWithOverviewMode(true);
        mWebSettings.setUseWideViewPort(true);
        mWebSettings.setDefaultTextEncodingName("utf-8");
        mWebSettings.setLoadsImagesAutomatically(true);

        //调用JS方法.安卓版本大于17,加上注解 @JavascriptInterface
        mWebSettings.setJavaScriptEnabled(true);

        saveData(mWebSettings);

        newWin(mWebSettings);

        mWebView.setWebChromeClient(webChromeClient);
        mWebView.setWebViewClient(webViewClient);
        mWebView.loadUrl(mUrl);
    }

    /**
     * 多窗口的问题
     */
    private void newWin(WebSettings mWebSettings) {
        //html中的_bank标签就是新建窗口打开，有时会打不开，需要加以下
        //然后 复写 WebChromeClient的onCreateWindow方法
        mWebSettings.setSupportMultipleWindows(true);
        mWebSettings.setJavaScriptCanOpenWindowsAutomatically(true);
    }


    /**
     * HTML5数据存储
     */
    private void saveData(WebSettings mWebSettings) {
        //有时候网页需要自己保存一些关键数据,Android WebView 需要自己设置
        mWebSettings.setDomStorageEnabled(true);
        mWebSettings.setDatabaseEnabled(true);
        mWebSettings.setAppCacheEnabled(true);
        String appCachePath = getApplicationContext().getCacheDir().getAbsolutePath();
        mWebSettings.setAppCachePath(appCachePath);
    }

    WebViewClient webViewClient = new WebViewClient(){

        /**
         * 多页面在同一个WebView中打开，就是不新建activity或者调用系统浏览器打开
         */
        @Override
        public boolean shouldOverrideUrlLoading(WebView view, String url) {
            view.loadUrl(url);
            return true;
        }

    };

    WebChromeClient webChromeClient = new WebChromeClient() {

        //=========HTML5定位==========================================================
        //需要先加入权限
        //<uses-permission android:name="android.permission.INTERNET"/>
        //<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
        //<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
        @Override
        public void onReceivedIcon(WebView view, Bitmap icon) {
            super.onReceivedIcon(view, icon);
        }

        @Override
        public void onGeolocationPermissionsHidePrompt() {
            super.onGeolocationPermissionsHidePrompt();
        }

        @Override
        public void onGeolocationPermissionsShowPrompt(final String origin, final GeolocationPermissions.Callback callback) {
            callback.invoke(origin, true, false);//注意个函数，第二个参数就是是否同意定位权限，第三个是是否希望内核记住
            super.onGeolocationPermissionsShowPrompt(origin, callback);
        }
        //=========HTML5定位==========================================================


        //=========多窗口的问题==========================================================
        @Override
        public boolean onCreateWindow(WebView view, boolean isDialog, boolean isUserGesture, Message resultMsg) {
            WebView.WebViewTransport transport = (WebView.WebViewTransport) resultMsg.obj;
            transport.setWebView(mWebView);
            resultMsg.sendToTarget();
            return true;
        }
        //=========多窗口的问题==========================================================
    };


    @Override
    public boolean onKeyDown(int keyCode, KeyEvent event) {
        if (keyCode == KeyEvent.KEYCODE_BACK && mWebView.canGoBack()) {
            mWebView.goBack();
            return true;
        }

        return super.onKeyDown(keyCode, event);
    }

    @Override
    protected void onDestroy() {
        if (mWebView != null) {
            mWebView.loadDataWithBaseURL(null, "", "text/html", "utf-8", null);
            mWebView.clearHistory();

            ((ViewGroup) mWebView.getParent()).removeView(mWebView);
            mWebView.destroy();
            mWebView = null;
        }
        super.onDestroy();
    }

}

# WebViewFragment



参考：

- [http://www.jianshu.com/users/320f9e8f7fc9/latest_articles](http://www.jianshu.com/users/320f9e8f7fc9/latest_articles)
- [http://blog.csdn.net/u011200604/article/details/52767304](http://blog.csdn.net/u011200604/article/details/52767304)