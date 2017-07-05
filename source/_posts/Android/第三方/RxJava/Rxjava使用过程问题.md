---
title: Rxjava 使用过程问题
date: 2016-10-17 13:18:12
tags: [Rxjava]
category: Android

---


# Subscriber 不能重复使用， Observer 可以重复使用
[http://stackoverflow.com/questions/29415522/reuse-subscriber](http://stackoverflow.com/questions/29415522/reuse-subscriber)

原因：

>You cannot reuse Subscriber, because it implements Subscription, which has an isUnsubscribed field which, once set to true, will never become false again, so Subscription is not reusable.

>Observer, on the other hand, does not contain any information about the subscription status, so you can reuse it. Each time you subscribe an Observer to an Observable, the RxJava implementation will wrap it inside a new Subscriber for you.


<!--more-->
