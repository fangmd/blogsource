---
title: Android后台定时任务实现方案
date: 2016-09-21 13:18:12
tags: [Android, 定时任务]
category: Android

---

# 方案一 适合需要唤醒cpu的任务比如定位

Service, AlarmManager, PendingIntent，BroardcastReciever

在服务中创建AlarmManager，PendingIntent：

    AlarmManager alarmManager = (AlarmManager)getSystemService(Context.ALARM_SERVICE);
    Intent intent2 = new Intent(this, AutoUpdateReceiver.class
    PendingIntent pi = PendingIntent.getBroadcast(this, 0, intent2, 0);
    alarmManager.set(AlarmManager.RTC_WAKEUP, System.currentTimeMillis()+timeSpan, pi); // 执行一次； 周期执行使用：alarmManager.setRepeating();

BroadcastReciever:

    public class AutoUpdateReceiver extends BroadcastReceiver {  

        @Override  
        public void onReceive(Context context, Intent intent) {  
            // 定时任务处理
            // 重复任务
            alarmManager.set(AlarmManager.RTC_WAKEUP, System.currentTimeMillis()+timeSpan, pi);
        }  
    }  

<!--more-->

## 在Android6.0 中需要使用：

    alarmManager.setAlarmClock(。。。);

或者：

    setAndAllowWhileIdle() setExactAndAllowWhileIdle()；// Neither setAndAllowWhileIdle() nor setExactAndAllowWhileIdle() can fire alarms more than once per 9 minutes, per app.

### 模拟Doze状态测试app

1. 运行应用
2. 关掉屏幕（app还处于活跃状态）
3. 运行以下命令强制系统进入Doze状态

        $ adb shell dumpsys battery unplug
        $ adb shell dumpsys deviceidle step

    重复上面的命令，知道看到 IDM
4. 观察任务是否正常

# 方案二

获取锁，实现WakeLock，太耗电了。

参考：

- [https://my.oschina.net/lorcan/blog/539208](https://my.oschina.net/lorcan/blog/539208)
- [https://developer.android.com/training/monitoring-device-state/doze-standby.html](https://developer.android.com/training/monitoring-device-state/doze-standby.html)
