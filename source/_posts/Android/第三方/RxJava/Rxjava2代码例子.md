---
title: Rxjava2 代码例子
date: 2016-11-24 13:18:12
tags: [Rxjava2]
category: Android

---

# Backpressure 背压

背压是指在异步场景中，被观察者发送事件速度远快于观察者的处理速度的情况下，一种告诉上游的被观察者降低发送速度的策略。

简单的说：发射数据太快，观察者来不及处理。

强调两点：

1. 背压策略的一个前提是异步环境，也就是说，被观察者和观察者处在不同的线程环境中。
2. 背压（Backpressure）并不是一个像 flatMap 一样可以在程序中直接使用的操作符，他只是一种控制事件流速的策略。

使用 Flowable 处理背压


# 使用的基本流

1. 创建 Observable：被观察者
2. 创建 Observer：观察者
3. 订阅事件 subscribe：Observable 和 Observer 通过 subscribe() 方法实现订阅关系

# SimpleExample

	Observable.just("observable")
			.subscribeOn(Schedulers.io())
			.observeOn(AndroidSchedulers.mainThread())
			.subscribe(new Observer<String>() {
				@Override
				public void onSubscribe(Disposable d) {
					// Disposable 对象用于取消订阅
				}

				@Override
				public void onNext(String value) {

				}

				@Override
				public void onError(Throwable e) {

				}

				@Override
				public void onComplete() {

				}
			});


使用 `Disposable` 对象取消订阅：

	if (!d.isDisposed()) {
		d.dispose();
	}

>注意: 调用dispose()并不会导致上游不再继续发送事件, 上游会继续发送剩余的事件.

# Map 操作符
将被观察者发射的数据转化后发射给观察者。

	Observable<List<ApiUser>> observable = Observable.create(new ObservableOnSubscribe<List<ApiUser>>() {
		@Override
		public void subscribe(ObservableEmitter<List<ApiUser>> e) throws Exception {
			// 代码判断是否订阅被取消了
			if (!e.isDisposed()) {
				e.onNext(Utils.getApiUserList());
				e.onComplete();
			}
		}
	});

	Observer<List<User>> observer = new Observer<List<User>>() {
		@Override
		public void onSubscribe(Disposable d) {

		}

		@Override
		public void onNext(List<User> value) {

		}

		@Override
		public void onError(Throwable e) {

		}

		@Override
		public void onComplete() {

		}
	};

	Function<List<ApiUser>, List<User>> mapper = new Function<List<ApiUser>, List<User>>() {
		@Override
		public List<User> apply(List<ApiUser> apiUsers) throws Exception {
			return Utils.convertApiUserListToUserList(apiUsers);
		}
	};

	observable.subscribeOn(Schedulers.io())
			.observeOn(AndroidSchedulers.mainThread())
			.map(mapper)
			.subscribe(observer);

## ObservableEmitter 发射器
RxJava2 中使用发射器向观察者发送数据。


继承自 `Emitter`

	public interface ObservableEmitter<T> extends Emitter<T> {

		void setDisposable(Disposable d);
		void setCancellable(Cancellable c);
		boolean isDisposed();
		ObservableEmitter<T> serialize();

	}

发射方法在 `Emitter`:

	void onNext(T value);
	void onError(Throwable error);
	void onComplete();

一些发射规则：

1. 可以发送无限个 onNext, Observer 也可以接收无限个 onNext.
2. 当发送了一个onComplete后继续发送 onNext, 而 Observable 收到 onComplete 事件之后将不再继续接收事件.
3. 当发送了一个onError后继续发送 onNext, 而 Observable 收到 onError 事件之后将不再继续接收事件.
4. 可以不发送onComplete或onError.
5. 最为关键的是onComplete和onError必须唯一并且互斥, 即不能发多个onComplete, 也不能发多个onError, 也不能先发一个onComplete, 然后再发一个onError, 反之亦然

# flatMap

>flatMap并不保证事件的顺序

## 使用例子

解决嵌套请求

	api.register(new RegisterRequest())            //发起注册请求
		.subscribeOn(Schedulers.io())               //在IO线程进行网络请求
		.observeOn(AndroidSchedulers.mainThread())  //回到主线程去处理请求注册结果
		.doOnNext(new Consumer<RegisterResponse>() {
			@Override
			public void accept(RegisterResponse registerResponse) throws Exception {
				//先根据注册的响应结果去做一些操作
			}
		})
		.observeOn(Schedulers.io())                 //回到IO线程去发起登录请求
		.flatMap(new Function<RegisterResponse, ObservableSource<LoginResponse>>() {
			@Override
			public ObservableSource<LoginResponse> apply(RegisterResponse registerResponse) throws Exception {
				return api.login(new LoginRequest());
			}
		})
		.observeOn(AndroidSchedulers.mainThread())  //回到主线程去处理请求登录的结果
		.subscribe(new Consumer<LoginResponse>() {
			@Override
			public void accept(LoginResponse loginResponse) throws Exception {
				Toast.makeText(MainActivity.this, "登录成功", Toast.LENGTH_SHORT).show();
			}
		}, new Consumer<Throwable>() {
			@Override
			public void accept(Throwable throwable) throws Exception {
				Toast.makeText(MainActivity.this, "登录失败", Toast.LENGTH_SHORT).show();
			}
		});

# concatMap
和 flatMap 一样，不过可以保证事件的顺序



# zip

合并两个 Observerable

## 实例，将连个请求的数据合并

	Observable<UserBaseInfoResponse> observable1 =                                            
	        api.getUserBaseInfo(new UserBaseInfoRequest()).subscribeOn(Schedulers.io());      

	Observable<UserExtraInfoResponse> observable2 =                                           
	        api.getUserExtraInfo(new UserExtraInfoRequest()).subscribeOn(Schedulers.io());    

	Observable.zip(observable1, observable2,                                                  
	        new BiFunction<UserBaseInfoResponse, UserExtraInfoResponse, UserInfo>() {         
	            @Override                                                                     
	            public UserInfo apply(UserBaseInfoResponse baseInfo,                          
	                                  UserExtraInfoResponse extraInfo) throws Exception {     
	                return new UserInfo(baseInfo, extraInfo);                                 
	            }                                                                             
	        }).observeOn(AndroidSchedulers.mainThread())                                      
	        .subscribe(new Consumer<UserInfo>() {                                             
	            @Override                                                                     
	            public void accept(UserInfo userInfo) throws Exception {                      
	                //do something;                                                           
	            }                                                                             
	        });

# defer




# Disposable 防止内存泄露

需要用到的对象： `CompositeDisposable`

使用：

	private final CompositeDisposable disposables = new CompositeDisposable();

	public void mine() {
		Observable<String> observable = Observable.create(new ObservableOnSubscribe<String>() {
			@Override
			public void subscribe(ObservableEmitter<String> e) throws Exception {
				if (e.isDisposed()) return;
				SystemClock.sleep(2000);
				e.onNext("next");
				e.onComplete();
			}
		});

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

		observable.subscribeOn(Schedulers.io())
				.observeOn(AndroidSchedulers.mainThread())
				.subscribeWith(observer);  //  从源码看  subscribeWith 和 subscribe 没有区别，除了返回值：subscribeWith 会返回 observer 本身，这里也可以使用 subscribe

		disposables.add(observer);


	}

	@Override
	protected void onDestroy() {
		super.onDestroy();
		disposables.clear(); // do not send event after activity has been destroyed
	}


## 可以利用这个封装一个防止泄露的框架

BasePresenter:

	public abstract class BasePresenter {

	    CompositeDisposable mCompositeDisposable = new CompositeDisposable();

	    public void unSubscribe() {
	        mCompositeDisposable.clear();
	    }


	    public void register(Disposable disposable) {
	        mCompositeDisposable.add(disposable);
	    }


	}

SimplePresenter extends BasePresenter:

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

尽量在使用 functions 包下的简化接口时要谨慎，因为他们不具备异常处理方法，可以使用类似下面的封装简化代码：

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

使用：

	BaseDisposableObserver<Long> baseDisposableObserver = new BaseDisposableObserver<Long>() {
		@Override
		public void onNext(Long value) {
			Log.d(TAG, "onNext: ");
		}
	};

	//.... 对于不需要处理的异常让其父类捕获，需要就重写 onError 方法即可
	.subscribe(baseDisposableObserver);



# take

设置 Observable 能够发射（ onNext ）的最大消息数，超过的不会被接收到

	observable.subscribeOn(Schedulers.io())
			.observeOn(AndroidSchedulers.mainThread())
			.take(3)
			.subscribe(observer);

# timer 延时操作

	Observable<Long> observable = Observable.timer(2, TimeUnit.SECONDS);

# interval 循环执行

	Observable.interval(0, 2, TimeUnit.SECONDS)

# reduce

将所有的发送数据进行处理，返回一个统一的数据。

	Observable.just(1, 2, 3, 4).reduce(new BiFunction<Integer, Integer, Integer>() {
		@Override
		public Integer apply(Integer t1, Integer t2) {
			return t1 + t2;
		}
	}).subscribe(getObserver());

	//结果：10

# buffer

	Observable<List<String>> buffered = Observable.just("one", "two", "three", "four", "five").buffer(3, 1);

	// 3 means,  it takes max of three from its start index and create list
	// 1 means, it jumps one step every time
	// so the it gives the following list
	// 1 - one, two, three
	// 2 - two, three, four
	// 3 - three, four, five
	// 4 - four, five
	// 5 - five

	buffered.subscribe(getObserver());

# filter 过滤

	Observable.just(1, 2, 3, 4, 5, 6)
			.filter(new Predicate<Integer>() {
				@Override
				public boolean test(Integer integer) throws Exception {
					return integer % 2 == 0;
				}
			})
			.subscribe(getObserver());

# skip 跳过

设置跳过发射的数据的个数

	getObservable()
			.subscribeOn(Schedulers.io())
			.observeOn(AndroidSchedulers.mainThread())
			.skip(2)
			.subscribe(getObserver());

# replay 重复发射


# repeat 重复

在接收到 onComplete 事件后触发

## repeatWhen

# retry 重复

参数 times：表示重试次数
```java
public final Observable<T> retry(long times) {
```

接收到.onError()事件后触发重订阅

## retryWhen

### retryWhen 操作符实现错误重试机制


RetryWithDelay:

	public class RetryWithDelay implements Function<Flowable<Throwable>, Publisher<?>> {

	    private final int maxRetries;
	    private final int retryDelayMillis;
	    private int retryCount;

	    public RetryWithDelay(int maxRetries, int retryDelayMillis) {
	        this.maxRetries = maxRetries;
	        this.retryDelayMillis = retryDelayMillis;
	    }


	    @Override
	    public Publisher<?> apply(Flowable<Throwable> throwableFlowable) throws Exception {
	        Flowable<Throwable> delay = throwableFlowable.delay(retryDelayMillis, TimeUnit.MILLISECONDS);
	        return delay;
	    }
	}

usage:

	mDisposable = Flowable.create(new LongConnectFlowableOnSubscribe(), BackpressureStrategy.BUFFER)
			  .subscribeOn(Schedulers.io())
			  .observeOn(AndroidSchedulers.mainThread())
			  .retryWhen(new RetryWithDelay(3, 3000))
			  .repeat()
			  .subscribe(new Consumer<String>() {
				  @Override
				  public void accept(String s) throws Exception {
					  dealMsg(s);
				  }
			  }, throwable -> {
				  LoggerUtils.e(throwable.getMessage());
			  });





# concat 将多个被观察者顺序执行

	Observable.concat(aObservable, bObservable)
	                .subscribe(getObserver());

	// aObservable 发射完数据后，bObservable发射数据

# merge 结合多个被观察者

和concat比无序，接收到的数据可能是穿插的

	Observable.merge(aObservable, bObservable)
			.subscribe(getObserver());


# defer 延迟


	Observable.defer(new Callable<ObservableSource<? extends String>>() {
	            @Override
	            public ObservableSource<? extends String> call() throws Exception {
	                return Observable.just(brand);
	            }
	        });

	// 通过上面创建的 Observable

	//....修改 brand 有效

	brandDeferObservable
                .subscribe(getObserver()); // 获取到的数据是修改后的 brand

# distinct 保证发射的数据不重复

	getObservable().distinct() .subscribe(getObserver());


# last 最后的数据

观察者只会收到被观察者最后发射的数据，可以设置默认值

	getObservable().last("A1") // the default item ("A1") to emit if the source ObservableSource is empty
			 .subscribe(getObserver());

# sample

每隔 [时间] 发送里时间点最近那个事件，


## Obserable 基本类型

## Observable

## Maybe



## Single 发送一个消息

	Single.just("Amit")...

## CompletableObserver

	Completable completable = Completable.create(new CompletableOnSubscribe() {
		@Override
		public void subscribe(CompletableEmitter e) throws Exception {
			//
		}
	});

`CompletableEmitter` 只能发射结束事件和异常事件 `onComplete`,`onError`

用于执行单任务

## Flowable
在RxJava1.x中背压控制是由Observable完成的，而在RxJava2.x中将其独立了出来，取名为Flowable。

通过Flowable我们可以自定义背压处理策略

	Flowable.create(new FlowableOnSubscribe<Integer>() {

			  @Override
			  public void subscribe(FlowableEmitter<Integer> e) throws Exception {

				  for(int i=0;i<10000;i++){
					  e.onNext(i);
				  }
				  e.onComplete();
			  }
		  }, FlowableEmitter.BackpressureMode.ERROR) //指定背压处理策略，抛出异常
				  .subscribeOn(Schedulers.computation())
				  .observeOn(Schedulers.newThread())
				  .subscribe(new Consumer<Integer>() {
					  @Override
					  public void accept(Integer integer) throws Exception {
						  Log.d("JG", integer.toString());
						  Thread.sleep(1000);
					  }
				  }, new Consumer<Throwable>() {
					  @Override
					  public void accept(Throwable throwable) throws Exception {
						  Log.d("JG",throwable.toString());
					  }
				  });

或者：

	Flowable.range(1,10000)
				   .onBackpressureDrop()
				   .subscribe(integer -> Log.d("JG",integer.toString()));



参考：

- [Rxjava2代码例子](https://github.com/amitshekhariitbhu/RxJava2-Android-Samples)
- [Rxjava2和Rxjava1的不同点](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2016/0907/6604.html)
- [背压](http://gold.xitu.io/post/582d413c8ac24700619cceed)
- [https://gold.xitu.io/post/5848d96761ff4b0058c9d3dc](https://gold.xitu.io/post/5848d96761ff4b0058c9d3dc)
