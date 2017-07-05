---
title: Rxjava 学习记录
date: 2016-09-06 13:18:12
tags: [Rxjava]
category: Android

---


# 概念

Rxjava的异步实现,通过一种扩展的观察者模式.

![http://ww3.sinaimg.cn/mw1024/52eb2279jw1f2rx4446ldj20ga03p74h.jpg](http://ww3.sinaimg.cn/mw1024/52eb2279jw1f2rx4446ldj20ga03p74h.jpg)

Observable:被观察者

Observer:观察者

onEvent(params):被观察者发生变化后执行的方法

## RxJava的观察者模式
1. Observable
2. Observer
3. subscribe:订阅(事件): Observable 和 Observer 通过 subscribe() 方法实现订阅关系
4. 执行方法: `onNext()`,`onCompleted()`,`onError()`

![http://ww3.sinaimg.cn/mw1024/52eb2279jw1f2rx46dspqj20gn04qaad.jpg](http://ww3.sinaimg.cn/mw1024/52eb2279jw1f2rx46dspqj20gn04qaad.jpg)

<!--more-->

## 基本实现

### 创建Observer,或者 Subscriber(建议使用)

		Observer<String> observer = new Observer<String>() {
		    @Override
		    public void onNext(String s) {
			Log.d(tag, "Item: " + s);
		    }

		    @Override
		    public void onCompleted() {
			Log.d(tag, "Completed!");
		    }

		    @Override
		    public void onError(Throwable e) {
			Log.d(tag, "Error!");
		    }
		};

除了 Observer 接口之外，RxJava 还内置了一个实现了 Observer 的抽象类：Subscriber。 Subscriber 对 Observer 接口进行了一些扩展，但他们的基本使用方式是完全一样的, `Subscriber`扩展了两个方法:

- `onStart()`:它会在 subscribe 刚开始，而事件还未发送之前被调用，可以用于做一些准备工作
- `unsubscribe()`:取消订阅, `isUnsubscribed()`判断状态,在`subscribe()`后`Observable`会持有`Subscriber`的引用,这个引用如果不能及时被释放，将有内存泄露的风险。所以最好保持一个原则：要在不再使用的时候尽快在合适的地方（例如 onPause() onStop() 等方法中）调用 unsubscribe() 来解除引用关系，以避免内存泄露的发生。

### 创建 Observable
使用`create'()`方法创建Observable,并定义事件触发规则.

	Observable observable = Observable.create(new Observable.OnSubscribe<String>() {
	    @Override
	    public void call(Subscriber<? super String> subscriber) {
		subscriber.onNext("Hello");
		subscriber.onNext("Hi");
		subscriber.onNext("Aloha");
		subscriber.onCompleted();
	    }
	});

`OnSubscribe`对象作为参数传入,这个对象会被保存在`Observable`对象中, 当`Observable`对象被订阅的时候,`OnSubscribe`对象中的`call`方法就会执行.

创建`Observable`的其他方法:

`just(T ...)`:

	Observable observable = Observable.just("Hello", "Hi", "Aloha");
	// 将会依次调用：
	// onNext("Hello");
	// onNext("Hi");
	// onNext("Aloha");
	// onCompleted();

`from(T[]) / from(Iterable<? extends T>) `: 将传入的数组或 Iterable 拆分成具体对象后，依次发送出来:

	String[] words = {"Hello", "Hi", "Aloha"};
	Observable observable = Observable.from(words);
	// 将会依次调用：
	// onNext("Hello");
	// onNext("Hi");
	// onNext("Aloha");
	// onCompleted();

### Subscribe 订阅
链接订阅者和被订阅者:

	observable.subscribe(observer);
	// 或者：
	observable.subscribe(subscriber);

上面是一个完整的写法,RxJava还有不完整的写法:

	Action1<String> onNextAction = new Action1<String>() {
	    // onNext()
	    @Override
	    public void call(String s) {
		Log.d(tag, s);
	    }
	};
	Action1<Throwable> onErrorAction = new Action1<Throwable>() {
	    // onError()
	    @Override
	    public void call(Throwable throwable) {
		// Error handling
	    }
	};
	Action0 onCompletedAction = new Action0() {
	    // onCompleted()
	    @Override
	    public void call() {
		Log.d(tag, "completed");
	    }
	};

	// 自动创建 Subscriber ，并使用 onNextAction 来定义 onNext()
	observable.subscribe(onNextAction);
	// 自动创建 Subscriber ，并使用 onNextAction 和 onErrorAction 来定义 onNext() 和 onError()
	observable.subscribe(onNextAction, onErrorAction);
	// 自动创建 Subscriber ，并使用 onNextAction、 onErrorAction 和 onCompletedAction 来定义 onNext()、 onError() 和 onCompleted()
	observable.subscribe(onNextAction, onErrorAction, onCompletedAction);

## 线程控制 Scheduler

### 基本使用API

- Schedulers.immediate(): 直接在当前线程运行，相当于不指定线程。这是默认的 Scheduler。
- Schedulers.newThread(): 总是启用新线程，并在新线程执行操作。
- Schedulers.io():内部实现是是用一个无数量上限的线程池，可以重用空闲的线程
- Schedulers.computation(): 注意不要把io操作放在这里,同样的不要把计算操作放在io中
- AndroidSchedulers.mainThread()

		Observable.just(1, 2, 3, 4)
		    .subscribeOn(Schedulers.io()) // 指定 subscribe() 发生在 IO 线程
		    .observeOn(AndroidSchedulers.mainThread()) // 指定 Subscriber 的回调发生在主线程
		    .subscribe(new Action1<Integer>() {
			@Override
			public void call(Integer number) {
			    Log.d(tag, "number:" + number);
			}
		    });



## 变换
将事件序列中的对象或整个序列进行加工处理，转换成不同的事件或事件序列。

使用操作符进行变换

### map
将一个事件转换成另一个事件.

	Observable.just("images/logo.png") // 输入类型 String
	    .map(new Func1<String, Bitmap>() {
		@Override
		public Bitmap call(String filePath) { // 参数类型 String
		    return getBitmapFromPath(filePath); // 返回类型 Bitmap
		}
	    })
	    .subscribe(new Action1<Bitmap>() {
		@Override
		public void call(Bitmap bitmap) { // 参数类型 Bitmap
		    showBitmap(bitmap);
		}
	    });

### flatMap
将输入的数据转化成多个`Observable`对象,将这个对象使用一个`Observable`对象包装起来发送给`Subscriber`进行处理.

	Student[] students = ...;
	Subscriber<Course> subscriber = new Subscriber<Course>() {
	    @Override
	    public void onNext(Course course) {
		Log.d(tag, course.getName());
	    }
	    ...
	};
	Observable.from(students)
	    .flatMap(new Func1<Student, Observable<Course>>() {
		@Override
		public Observable<Course> call(Student student) {
		    return Observable.from(student.getCourses());
		}
	    })
	    .subscribe(subscriber);

### 变换的原理：lift()
变换的本质是针对事件序列的处理和再发送。

## 例子
打印字符:

	String[] names = ...;
	Observable.from(names)
	    .subscribe(new Action1<String>() {
		@Override
		public void call(String name) {
		    Log.d(tag, name);
		}
	    });

由id取图片:

	int drawableRes = ...;
	ImageView imageView = ...;
	Observable.create(new OnSubscribe<Drawable>() {
	    @Override
	    public void call(Subscriber<? super Drawable> subscriber) {
		Drawable drawable = getTheme().getDrawable(drawableRes));
		subscriber.onNext(drawable);
		subscriber.onCompleted();
	    }
	}).subscribe(new Observer<Drawable>() {
	    @Override
	    public void onNext(Drawable drawable) {
		imageView.setImageDrawable(drawable);
	    }

	    @Override
	    public void onCompleted() {
	    }

	    @Override
	    public void onError(Throwable e) {
		Toast.makeText(activity, "Error!", Toast.LENGTH_SHORT).show();
	    }
	});


# 使用场景

## 定时
	Observable.timer(2, TimeUnit.SECONDS)  
		      .subscribe(new Observer<Long>() {  
		          @Override  
		          public void onCompleted() {  
		              log.d ("completed");  
		          }  

		          @Override  
		          public void onError(Throwable e) {  
		              log.e("error");  
		          }  

		          @Override  
		          public void onNext(Long number) {  
		              log.d ("hello world");  
		          }  
		      });  

## 循环执行

	       Observable.interval(1000, TimeUnit.MILLISECONDS)
		        .observeOn(Schedulers.io())
		        .subscribeOn(AndroidSchedulers.mainThread())
		        .subscribe(new Action1<Long>() {
		            @Override
		            public void call(Long aLong) {
		                mRectanglePro.setProgress(++mStartProgress);
		            }
		        });

## throttleFirst 防止按钮重复点击

	RxView.clicks(button)  
	              .throttleFirst(1, TimeUnit.SECONDS)  
	              .subscribe(new Observer<Object>() {  
	                  @Override  
	                  public void onCompleted() {  
	                        log.d ("completed");  
	                  }  

	                  @Override  
	                  public void onError(Throwable e) {  
	                        log.e("error");  
	                  }  

	                  @Override  
	                  public void onNext(Object o) {  
	                       log.d("button clicked");  
	                  }  
	              });  

## debounce 做textSearch 做搜索
当n个节点发生的时间太靠近，debounce回自动过滤掉n＋1个节点


比如在做百度地址联想的时候，可以使用debounce减少频繁的网络请求。避免每输入（删除）一个字就做一次联想：

    RxTextView.textChangeEvents(inputEditText)
        .debounce(400, TimeUnit.MILLISECONDS)
        .observeOn(AndroidSchedulers.mainThread())
        .subscribe(new Observer<TextViewTextChangeEvent>(){

            ...

            @Override
            public void onNext(){
                logd(format("Searching for %s", onTextChangeEvent.text().toString()));
            }
        })

## combineLatext 合并最近的n个结点

例如：注册的时候所有输入信息（邮箱、密码、电话号码等）合法才点亮注册按钮。

	Observable<CharSequence> _emailChangeObservable = RxTextView.textChanges(_email).skip(1);  
	Observable<CharSequence> _passwordChangeObservable = RxTextView.textChanges(_password).skip(1);  
	Observable<CharSequence>   _numberChangeObservable = RxTextView.textChanges(_number).skip(1);  

	Observable.combineLatest(_emailChangeObservable,  
	              _passwordChangeObservable,  
	              _numberChangeObservable,  
	              new Func3<CharSequence, CharSequence, CharSequence, Boolean>() {  
	                  @Override  
	                  public Boolean call(CharSequence newEmail,  
	                                      CharSequence newPassword,  
	                                      CharSequence newNumber) {  

	                      Log.d("xiayong",newEmail+" "+newPassword+" "+newNumber);  
	                      boolean emailValid = !isEmpty(newEmail) &&  
	                                           EMAIL_ADDRESS.matcher(newEmail).matches();  
	                      if (!emailValid) {  
	                          _email.setError("Invalid Email!");  
	                      }  

	                      boolean passValid = !isEmpty(newPassword) && newPassword.length() > 8;  
	                      if (!passValid) {  
	                          _password.setError("Invalid Password!");  
	                      }  

	                      boolean numValid = !isEmpty(newNumber);  
	                      if (numValid) {  
	                          int num = Integer.parseInt(newNumber.toString());  
	                          numValid = num > 0 && num <= 100;  
	                      }  
	                      if (!numValid) {  
	                          _number.setError("Invalid Number!");  
	                      }  

	                      return emailValid && passValid && numValid;  

	                  }  
	              })//  
	              .subscribe(new Observer<Boolean>() {  
	                  @Override  
	                  public void onCompleted() {  
	                      log.d("completed");  
	                  }  

	                  @Override  
	                  public void onError(Throwable e) {  
	                     log.d("Error");  
	                  }  

	                  @Override  
	                  public void onNext(Boolean formValid) {  
	                     _btnValidIndicator.setEnabled(formValid);    
	                  }  
	              });  


# Other

## 如何让 RxJava异常的时候  继续执行 onnext(一般在循环任务的时候使用)
[http://stackoverflow.com/questions/28969995/how-to-ignore-error-and-continue-infinite-stream](http://stackoverflow.com/questions/28969995/how-to-ignore-error-and-continue-infinite-stream)

[RxJava 异常处理](https://github.com/ReactiveX/RxJava/wiki/Error-Handling-Operators)

### 方法一：很low不推荐

使用try。。catch把循环里可能的异常都捕获，就不会执行onerror了

	Observable<Object> observable = Observable.create(new Observable.OnSubscribe<Object>() {
		@Override
		public void call(Subscriber<? super Object> subscriber) {
			int cnt = 0;
			while (cnt < 3) {
				try {
					switch (cnt) {
						case 0:
							subscriber.onNext("next 0");
							break;
						case 1:
	//                            subscriber.onError(new Exception("haha"));
							Integer.parseInt("asd");
							break;
						case 2:
							subscriber.onNext("next 2");
							break;
							default:
						}
					} catch (Exception e) {

					} finally {
						cnt++;
					}
				}
			}
		});

		observable.subscribe(new Subscriber<Object>() {
			@Override
			public void onCompleted() {

			}

			@Override
			public void onError(Throwable e) {
				Log.d(TAG, "onError: ------" + e.getMessage());
			}

			@Override
			public void onNext(Object o) {
				Log.d(TAG, "onNext: " + o.toString());
			}
		});

### 方法二  retry()

结果是一只打印：MainActivity: onNext: next 0；

	mObservable = Observable.create(new Observable.OnSubscribe<Object>() {
		@Override
		public void call(Subscriber<? super Object> subscriber) {
			int cnt = 0;
			while (cnt < 3) {
				switch (cnt) {
					case 0:
						subscriber.onNext("next 0");
						break;
					case 1:
	//                            subscriber.onError(new Exception("haha"));
						Integer.parseInt("asd");
						break;
					case 2:
						subscriber.onNext("next 2");
						break;
					default:
				}
				cnt++;
			}
		}
	});

	mObservable.retry().subscribe(new Subscriber<Object>() {
		@Override
		public void onCompleted() {

		}

		@Override
		public void onError(Throwable e) {
			Log.d(TAG, "onError: ------" + e.getMessage());
		}

		@Override
		public void onNext(Object o) {
			Log.d(TAG, "onNext: " + o.toString());
		}
	});


## rx.exceptions.missingbackpressureexception

	mObservable.onBackpressureDrop()。。。

## exception thrown on scheduler.worker thread. add onerror handling

Application:

	RxJavaPlugins.getInstance().registerErrorHandler(new RxJavaErrorHandler() {
	    @Override
	    public void handleError(final Throwable throwable) {
	        new ErrorHandler().call(throwable);
	    }
	});

更好的解决方案：

>What we did at work was make a subclass of observer that has a default implementation of onError that you can still override if need be. Seems to solve your use case.

创建一个自定义抽象类继承 observer ，实现里面的 onError，在后面的使用中都使用自定义类的继承类。


	public abstract class Demo implements Observer {

	    @Override
	    public void onError(Throwable e) {
			//logd
	    }


	}


[http://stackoverflow.com/questions/28627806/global-errorhandling-solution-with-rxjava-only-when-onerror-is-not-implemented](http://stackoverflow.com/questions/28627806/global-errorhandling-solution-with-rxjava-only-when-onerror-is-not-implemented)


参考:
- [https://gank.io/post/560e15be2dca930e00da1083#toc_28](https://gank.io/post/560e15be2dca930e00da1083#toc_28)
- [http://blog.csdn.net/lzyzsd/article/details/41833541](http://blog.csdn.net/lzyzsd/article/details/41833541)
- [http://blog.csdn.net/theone10211024/article/details/50435325](http://blog.csdn.net/theone10211024/article/details/50435325)
