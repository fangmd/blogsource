---
title: RxSwift
date: 2017-05-19 13:18:12
tags: ios
category: ios

---

RxSwift

我看到过的比较好的文章

- [官方 Github](https://github.com/ReactiveX/RxSwift)
- [http://www.jianshu.com/p/431f645cb805](http://www.jianshu.com/p/431f645cb805)

    看了后，对很多概念豁然开朗

- [https://github.com/DianQK/LearnRxSwift](https://github.com/DianQK/LearnRxSwift)
- [RxSwift下如何实现基于MJRefresh的上下拉刷新？](http://www.jianshu.com/p/fff7ef50dbb1)

    很可惜作者没有给 Demo，从这里知道了如何实现 ViewModel 让 View 更新 UI， ViewModel 如何发消息给 View

<!--more-->

----- 个人纪录 -----

# Rx 序列基本操作

## 基本概念

核心概念就是一个观察者(Observer)订阅一个可观察序列(Observable)。观察者对 Observable 发射的数据或数据序列作出响应。

Observable 发射的事件：

```
enum Event<Element>  {
    case Next(Element)      // 序列的下一个元素
    case Error(ErrorType)   // 序列因为某些错误终止
    case Completed          // 正常的序列技术
}
```


```
class Observable<Element> {
    func subscribe(observer: Observer<Element>) -> Disposable
}

protocol ObserverType {
    func on(event: Event<Element>)
}
```


## Observable

Observable 创建方式：

```
asObservable 返回一个序列
create 使用 Swift 闭包的方式创建序列
deferred 只有在有观察者订阅时，才去创建序列
empty 创建一个空的序列，只发射一个 .Completed
error 创建一个发射 error 终止的序列
toObservable 使用 SequenceType 创建序列
interval 创建一个每隔一段时间就发射的递增序列
never 不创建序列，也不发送通知
just 只创建包含一个元素的序列。换言之，只发送一个值和 .Completed
of 通过一组元素创建一个序列
range 创建一个有范围的递增序列
repeatElement 创建一个发射重复值的序列
timer 创建一个带延迟的序列
```

#### create

使用 swift 闭包的方式创建序列

```
let myJust = { (singleElement: Int) -> Observable<Int> in
    return Observable.create { observer in
        observer.on(.Next(singleElement))
        observer.on(.Completed)
        
        return NopDisposable.instance
    }
}

_ = myJust(5)
    .subscribe { event in
        print(event)
}
```

#### timer

```
let timerSequence = Observable<Int>.timer(1, period: 1, scheduler: MainScheduler.instance)

_ = timerSequence.subscribe { event in
    print(event)
}
```

### bindTo

#### 0

将 UITextField 中文字的变化发送到 UILabel 显示

```
// public func bindTo<O>(_ observer: O) -> Disposable where O : ObserverType, O.E == Self.E

textField.rx.text
    .bind(to: label.rx.text)
    .addDisposableTo(disposeBag)
```

```
A bindTo B, A 给 B 发消息
```

>UITextField的rx_text属性为ControlProperty类型，实现了ControlPropertyType，所以不仅是观察者类型，还是被观察者类型，UILabel中的rx_text只是单纯的观察者类型。

#### 1

Variable 是一个神奇的东西，可以做数据发射源，也可以做接受源

```
// public func bindTo(_ variable: RxSwift.Variable<Self.E>) -> Disposable

这里 A bindTo B（Variable） ， 将 A 中的数据发射到 B
```

### Driver

在功能上它类似被观察者（Observable），而它本身也可以与被观察者相互转换（Observable: asDriver, Driver: asObservable）

特点：

- 不会发射出错误(Error)事件
- 对它的观察订阅是发生在主线程(UI线程)的
- 自带shareReplayLatestWhileConnected






## Observer


### UIBindingObserver

```
//  MARK: - 绑定方法
func binding() {
    textField.rx_text
        .bindTo(label.rx_sayHelloObserver)
        .addDisposableTo(disposeBag)
}
//  MARK: - 视图控件扩展
private extension UILabel {
    var rx_sayHelloObserver: AnyObserver<String> {
        return UIBindingObserver(UIElement: self, binding: { (label, string) in
            label.text = "Hello \(string)"
        }).asObserver()
    }
}
```


在RxCocoa框架中，某些地方也用到了UIBindingObserver，如UILable中的rx_text：

```
public var rx_text: AnyObserver<String> {
    return UIBindingObserver(UIElement: self) { label, text in
        label.text = text
    }.asObserver()
}
```




## Subject

可以把 Subject 当作一个桥梁（或者说是代理）,Subject 既是 Observable 也是 Observer

Subject 有以下几种：

```
PublishSubject 只发射给观察者订阅后的数据。
ReplaySubject
BehaviorSubject
Variable
```

### PublishSubject

```
let publishSubject = PublishSubject<String>()

publishSubject.subscribe { e in
    print("Subscription: 1, event: \(e)")
    }.addDisposableTo(disposeBag)

publishSubject.on(.Next("a"))
publishSubject.on(.Next("b"))

publishSubject.subscribe { e in /// 我们可以在这里看到，这个订阅只收到了两个数据，只有 "c" 和 "d"
    print("Subscription: 2, event: \(e)")
    }.addDisposableTo(disposeBag)

publishSubject.on(.Next("c"))
publishSubject.on(.Next("d"))
```

会存在数据丢失的情况，在特定的情境下会需要用到这个。如果需要延迟订阅并且可以获取到全部发射的数据可以使用 ReplaySubject


### ReplaySubject

和 PushblishSubject 不同，不论观察者什么时候订阅， ReplaySubject 都会发射完整的数据给观察者。

### BehaviorSubject

会接受在订阅前最近发射的一个数据，以及后面发射的所有数据

### Variable 常用


## DisposeBag

当一个 Observable （被观察者）被观察订阅后，就会产生一个 Disposable 实例，通过这个实例，我们就能进行资源的释放了。

建议所有的 Disposable 都加入到 DisposeBag 中进行管理

```
textField.rx_text
    .bindTo(label.rx_sayHelloObserver)
    .addDisposableTo(self.disposeBag)
```



## 错误处理

### retry

在操作发生错误的时候，重新订阅一次序列。

```
retry(2) // 最多尝试两次。
```

### catchError

当出现 Error 时，用一个新的序列替换。


```
let sequenceThatFails = PublishSubject<Int>()
let recoverySequence = Observable.of(100, 200)


 sequenceThatFails
        .catchError { error in
            return recoverySequence
        }
        .subscribe {
            print($0)
        }
```

### catchErrorJustReturn

遇到错误，返回一个值替换这个错误。


# 切换线程


```
sequence1
  .observeOn(backgroundScheduler) // 切换到后台线程
  .map { n in
      print("在 background scheduler 执行")
  }
  .observeOn(MainScheduler.instance) // 切换到主线程
  .map { n in
      print("在 main scheduler")
  }
```

- 调用 observeOn 指定接下来的操作在哪个线程。
- 调用 subscribeOn 决定订阅者的操作执行在哪个线程。

```
extension ObservableType {

    public func observeOn(scheduler: ImmediateSchedulerType) -> RxSwift.Observable<Self.E>

    public func subscribeOn(scheduler: ImmediateSchedulerType) -> RxSwift.Observable<Self.E>
}
```

## 封装线程转换代码

```
import RxSwift

public enum TScheduler {
    case Main
    case Serial(DispatchQueueSchedulerQOS)
    case Concurrent(DispatchQueueSchedulerQOS)
    case Operation(NSOperationQueue)
    
    
    public func scheduler() -> ImmediateSchedulerType {
        switch self {
        case .Main:
            return MainScheduler.instance
        case .Serial(let QOS):
            return SerialDispatchQueueScheduler(globalConcurrentQueueQOS: QOS)
        case .Concurrent(let QOS):
            return ConcurrentDispatchQueueScheduler(globalConcurrentQueueQOS: QOS)
        case .Operation(let queue):
            return OperationQueueScheduler(operationQueue: queue)
        }
    }
}
```

```
extension ObservableType {
    
    @warn_unused_result(message="http://git.io/rxs.uo")
    public func observeOn(scheduler: TScheduler) -> RxSwift.Observable<Self.E> {
        return observeOn(scheduler.scheduler())
    }
    
}
```

调用：

```
NSURLSession.sharedSession()
    .rx_data(request)
    .map { UIImage(data: $0) }
    .observeOn(.Main) // 切换到主线程
    .bindTo(imageView.rx_image) // 在主线程设置 `image`
    .addDisposableTo(disposeBag)
```

# RxCocoa

















