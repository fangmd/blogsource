---
title: 看了一些 RxJava 的文章我依然不知道的事
date: 2016-11-27 13:18:12
tags: [Rxjava2]
category: Android

---


>以下代码基于 RxJava2

# 内存泄露处理

基本代码：

    private final CompositeDisposable disposables = new CompositeDisposable();

    public void doSomeWork2() {
        // 1. 创建一个普通的 observable
        Observable<String> observable = Observable.create(new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(ObservableEmitter<String> e) throws Exception {
                if (e.isDisposed()) return;
                SystemClock.sleep(2000);
                e.onNext("next");
                e.onComplete();
            }
        });

        // 2. 创建一个可被 CompositeDisposable 管理的 observer
        DisposableObserver<String> observer = new DisposableObserver<String>() {
            @Override
            public void onNext(String value) {

            }

            @Override
            public void onError(Throwable e) {

            }

            @Override
            public void onComplete() {

            }
        };

        // 3. 订阅事件
        observable.subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(observer);

        // 4. 将需要被 CompositeDisposable 管理的 observer 加入到管理集合中
        disposables.add(observer);
    }

在 Activity／Fragment 销毁生命周期中取消异步操作防止内存泄露：

    @Override
    protected void onDestroy() {
        super.onDestroy();
        // 将所有的 observer 取消订阅
        disposables.clear();
    }

# 在 mvp 的项目中处理内存泄露

基于 [google todo-mvp-rxjava ](https://github.com/googlesamples/android-architecture/tree/todo-mvp-rxjava/) 搭建项目 mvp 框架：


BasePresenter :

	public abstract class BasePresenter {

	    CompositeDisposable mCompositeDisposable = new CompositeDisposable();

	    public void unSubscribe() {
	        mCompositeDisposable.clear();
	    }


	    public void register(Disposable disposable) {
	        mCompositeDisposable.add(disposable);
	    }


	}

一个例子 SimplePresenter extends BasePresenter :

	public class SimplePresenter extends BasePresenter {
	    private static final String TAG = SimplePresenter.class.getSimpleName();


	    //.....

	    public void startWork() {

	        DisposableObserver<Long> observer = new DisposableObserver<Long>() {
	            @Override
	            public void onNext(Long value) {
	                Log.d(TAG, "onNext: ");
	            }

	            @Override
	            public void onError(Throwable e) {

	            }

	            @Override
	            public void onComplete() {

	            }
	        };
	        Observable.interval(2000, TimeUnit.MILLISECONDS)
	                .subscribeOn(Schedulers.io())
	                .observeOn(AndroidSchedulers.mainThread())
	                .subscribe(observer);

	        register(observer);

	    }

	}

**注意：**在使用 RxJava 做异步操作的时候，调用 `register(observer);` 注册 observer 到 CompositeDisposable 中统一管理。

Activity/Fragment:

	@Override
	protected void onDestroy() {
		super.onDestroy();
		mSimplePresenter.unSubscribe();
	}

# 慎用 functions

functions：Action，Consumer，Function 等

原因：这些方法只会对 `onNext` 事件进行处理，如果 observable 中出现异常产生了 `onError` 事件就会导致 app 奔溃。

解决方案：

1. 不用简化方法
2. 自己封装一个：

封装一个类似下面的基类：

    public abstract class BaseDisposableObserver<T> extends DisposableObserver<T> {

        private static final String TAG = BaseDisposableObserver.class.getSimpleName();

        @Override
        public void onError(Throwable e) {
            Log.e(TAG, "onError: " + e.getMessage());
        }

        @Override
        public void onComplete() {

        }
    }

使用的时候和 functions 是一样的：（这样下面的代码就不会导致app奔溃了）

    BaseDisposableObserver<Long> baseDisposableObserver = new BaseDisposableObserver<Long>() {
        @Override
        public void onNext(Long value) {
            Log.d(TAG, "onNext: ");
        }
    };

    Observable.interval(2000, TimeUnit.MILLISECONDS)
            .filter(new Predicate<Long>() {
                @Override
                public boolean test(Long aLong) throws Exception {
                    Log.d(TAG, "test: along:" + aLong);

                    if (aLong > 2) {
                        throw new Exception("sdf");
                    }

                    return true;
                }
            })
    //                .subscribeOn(Schedulers.io())
    //                .observeOn(AndroidSchedulers.mainThread())
            .subscribe(baseDisposableObserver);


>RxJava 看的还不多，如有错误还请指出

参考

- [google todo-mvp-rxjava ](https://github.com/googlesamples/android-architecture/tree/todo-mvp-rxjava/)
