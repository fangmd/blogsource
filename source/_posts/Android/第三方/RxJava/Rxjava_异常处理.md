---
title: Rxjava 异常处理
date: 2016-10-10 13:18:12
tags: [Rxjava]
category: Android

---


# 处理方法

## onErrorResumeNext

instructs an Observable to emit a sequence of items if it encounters an error

当发生错误的时候，让Observable发射一个预先定义好的数据并正常地终止

## onErrorReturn

instructs an Observable to emit a particular item when it encounters an error

## onExceptionResumeNext

类似于OnErrorResume,不同之处在于其会对onError抛出的数据类型做判断，如果是Exception，也会使用另外一个Observable代替原Observable继续发射数据，否则会将错误分发给Subscriber。

instructs an Observable to continue emitting items after it encounters an exception (but not another variety of throwable)

<!--more-->

## retry

Retry操作符在发生错误的时候会重新进行订阅,而且可以重复多次

if a source Observable emits an error, resubscribe to it in the hopes that it will complete without error

1. public final Observable<T> retry():无限重试,直到成功.
2. public final Observable<T> retry(final long count)：指定重试次数
3. public final Observable<T> retry(Func2<Integer, Throwable, Boolean> predicate):传入一个Fun2,里面有两个入参,一个表示目前参数的次数,另一个就是Observable.onError里的类型,返回值true表示继续重试,false表示不重试,Subscriber.onError会被调用.

## retryWhen

接收onError的throwable作为参数，并根据定义好的函数返回一个Observable，如果这个Observable发射一个数据，就会重新订阅。

if a source Observable emits an error, pass that error to another Observable to determine whether to resubscribe to the source

    public final Observable<T> retryWhen(final Func1<? super Observable<? extends Throwable>, ? extends Observable<?>> notificationHandler);
    public final Observable<T> retryWhen(final Func1<? super Observable<? extends Throwable>, ? extends Observable<?>> notificationHandler, Scheduler scheduler);

返回的Observable<?>所要发送的事件决定了重试是否会发生:

- 如果发送的是onCompleted或者onError事件，将不会触发重订阅。
- 如果它发送onNext事件，则触发重订阅（不管onNext实际上是什么事件）。







参考：

- [官方文档](http://reactivex.io/documentation/operators/catch.html)
- [https://github.com/ReactiveX/RxJava/wiki/Error-Handling-Operators](https://github.com/ReactiveX/RxJava/wiki/Error-Handling-Operators)
- [中文文章](http://www.jianshu.com/p/78030954745c)
- [http://mushuichuan.com/2015/12/11/rxjava-operator-5/](http://mushuichuan.com/2015/12/11/rxjava-operator-5/)
