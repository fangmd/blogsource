---
title: MediaPlayer 
date: 2016-04-18 16:18:12
tags: MediaPlayer
category: program

---

# MediaPlayer

![状态图](http://i.imgur.com/dMoKjTu.gif)

MediaPlayer中的方法都要严格按照上面执行(方法在特定的状态下才能执行)，否在会报错。

---

1. 如果在`reset()`方法前调用：`getCurrentPosition()`, `getDuration()`, `getVideoHeight()`, `getVideoWidth()`, `setAudioStreamType(int)`, `setLooping(boolean)`, `setVolume(float, float)`, `pause()`, `start()`, `stop()`, `seekTo(int)`, `prepare()` or `prepareAsync()`如果用户设置了`OnErrorListener.onError()`也不会执行；如果在`reset()`方法后马上运行上面的方法就会执行错误。
2. 建议在`MediaPlayer`对象不会再使用的时候调用`release()`方法释放资源，因为可能它使用了一些单例资源比如硬件加速
3. 。。

----
通常一些播放的控制操作会失败由于很多原因：

- 音频文件格式不支持回退
- 流连接断开
- 。。

所以错误报告和复原在这样的情况下是很重要的。

MediaPlayer提供了错误接口：`setOnErrorListener(android.media.MediaPlayer.OnErrorListener)`

- 即使应用没有捕获错误，MediaPlayer还是会在遇到错误的时候马上执行这个方法。
- 一般在这个里面执行`reset()`来重新让`MediaPlayer`进入空闲状态。
- 推荐在编程的时候设置这个接口
- 在重复执行`setDataSource`， `prepare()`,` prepareAsync()`的时候会抛`IllegalStateException`异常

---

调用`setDataSource(FileDescriptor)`, or `setDataSource(String)`, or `setDataSource(Context, Uri)`, or `setDataSource(FileDescriptor, long, long)`, or `setDataSource(MediaDataSource)`让MediaPlayer从`Idle`状态
进入`Initialized`状态

- An IllegalStateException is thrown if setDataSource() is called in any other state.
- It is good programming practice to always look out for IllegalArgumentException and IOException that may be thrown from the overloaded setDataSource methods.

---

- 可以设置缓存`setOnBufferingUpdateListener(OnBufferingUpdateListener)`
- 在`start`状态下调用`start()`方法不会产生影响
