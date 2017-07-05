---
title: AIDL
date: 2016-03-23 21:03:14
tags: AIDL
categories: android

---

# AIDL 总结
>进程间通信，传递的是`IMyAidlInterface.Stub`继承类的实例

前期准备：

1. 快捷方式创建`AIDL`
2. 可能需要修改包名和class文件中的包地址
3. 在`AIDL`文件中写需要的方法
4. 将AIDL整个包文件都复制到另一个工程中

建立通信：

1. 在客户端APP通过意图建立绑定服务app的`Service`

		Intent intent = new Intent();
		intent.setClassName("com.doublefang.aidldemo", "com.doublefang.aidldemo.MyService");
		bindService(intent, this, BIND_AUTO_CREATE);
2. 获得`IMyAidlInterface.Stub`实例

    	@Override
    	public void onServiceConnected(ComponentName name, IBinder service) {
        	IMyAidlInterface instance = IMyAidlInterface.Stub.asInterface(service);

        	// 执行instance里面的方法
			// instance里面的方法都是实现`IMyAidlInterface`里面的方法
			// 所以在写需要的方法的时候，要先在接口`IMyAidlInterface`中创建相应的抽象方法
    	}
3. 客户端注意在activity销毁的时候解除绑定

		@Override
		protected void onDestroy() {
			super.onDestroy();
        	unbindService(this);
    	}

服务端：

1. 需要在Service中创建内部类：`IMyAidlInterface.Stub`的子类，并实现`IMyAidlInterface`的抽象方法
2. 在`onBind()`方法返回实例

		public IBinder onBind(Intent intent) {
			return new MyBinder();
    	}