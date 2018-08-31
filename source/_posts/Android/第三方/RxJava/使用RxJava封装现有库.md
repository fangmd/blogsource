---
title: 使用 RxJava 封装现有库
date: 2018-07-30 13:18:12
tags: [Rxjava]
category: Android

---



# 什么是函数式编程

"函数式编程"是一种"编程范式"（programming paradigm），也就是如何编写程序的方法论。

例子：

```
(1 + 2) * 3 - 4

# 传统编程过程
var a = 1 + 2;
var b = a * 3;
var c = b - 4;

# 函数式编程
var result = subtract(multiply(add(1,2), 3), 4);
```

# 封装一个带有接口回调的函数

某个第三方库的初始化函数。

```
    @Override
    public Observable<Object> setUp() {
        final OpenIABHelper openIABHelper = new OpenIABHelper();
        Observable<Object> objectObservable = Observable.create(new ObservableOnSubscribe<Object>() {
            @Override
            public void subscribe(final ObservableEmitter<Object> emitter) throws Exception {
                if (openIABHelper.setUpState()) {
                    // 已经初始化成功
                    emitter.onNext(new Object());
                    emitter.onComplete();
                }else {
                    openIABHelper.setUp(new OpenIABHelper.SetUpCallback() {
                        @Override
                        public void result(boolean result) {
                            if (result) {
                                emitter.onNext(new Object());
                                emitter.onComplete();
                            }else {
                                emitter.onError(new IabException("error"));
                            }
                        }
                    });
                }
            }
        });
        return objectObservable;
    }
```

> 这里不能使用 emitter.onNext(null); 。 这样做也表示失败，触发 RxJava error


usage:

```
        Disposable subscribe1 = openIAB.setUp().subscribe(new Consumer<Object>() {
            @Override
            public void accept(Object o) throws Exception {
                Log.d(TAG, "accept: setUp success");
            }
        }, new Consumer<Throwable>() {
            @Override
            public void accept(Throwable throwable) throws Exception {
                Log.d(TAG, "accept: setUp error");
            }
        });
```

# 封装一个会抛出异常的同步方法

>因为会抛出异常所以不同使用 Observable.just()

被封装的方法 `OpenIABHelper`：

```
    public String queryInventory() throws IabException {
        Log.d(TAG, "queryInventory: ");
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return "String from OpenIABHelper";
    }
```


封装 `OpenIAB`：

```
    @Override
    public Observable<String> queryInventory() {
        final OpenIABHelper openIABHelper = new OpenIABHelper();
        return Observable.defer(new Callable<ObservableSource<String>>() {
            @Override
            public ObservableSource<String> call() throws Exception {
                try {
                    return Observable.just(openIABHelper.queryInventory());
                } catch (IabException e) {
                    return Observable.error(e);
                }
            }
        });
    }
```

usage:

```
        Disposable subscribe = openIAB.queryInventory().subscribe(new Consumer<String>() {
            @Override
            public void accept(String s) throws Exception {
                Log.d(TAG, "accept: success:::" + s);

            }
        }, new Consumer<Throwable>() {
            @Override
            public void accept(Throwable throwable) throws Exception {
                Log.d(TAG, "accept: error:::");
            }
        });
```










参考：

- [http://www.ruanyifeng.com/blog/2012/04/functional_programming.html](http://www.ruanyifeng.com/blog/2012/04/functional_programming.html)
- [http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2016/0413/4141.html](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2016/0413/4141.html)

