---
title: EventBus 学习记录
date: 2016-04-03 13:18:12
tags: [eventBus, 学习记录]
category: android

---

# EventBus 学习记录


## Priorities and Event Cancellation 优先级和事件取消

### Subscriber Priorities 订阅优先级
设置优先级:

	@Subscribe(priority = 1);
	public void onEvent(MessageEvent event) {
		…
	}

高优先级的方法会先收到Event,默认优先级:0.*(注:这个只在同线程中执行时有效)*

取消:

	// Called in the same thread (default)
	@Subscribe
	public void onEvent(MessageEvent event){
		// Process the event
		…

		EventBus.getDefault().cancelEventDelivery(event) ;
	}
高优先级的事件处理可以拦截事件.


## Subscriber Index 订阅者索引


## 混淆


	-keepattributes *Annotation*
	-keepclassmembers class ** {
	    @org.greenrobot.eventbus.Subscribe <methods>;
	}
	-keep enum org.greenrobot.eventbus.ThreadMode { *; }

	# Only required if you use AsyncExecutor
	-keepclassmembers class * extends org.greenrobot.eventbus.util.ThrowableFailureEvent {
	    <init>(java.lang.Throwable);
	}

## 添加processor 加快速度

	provided 'de.greenrobot:eventbus-annotation-processor:3.0.0-beta1'






