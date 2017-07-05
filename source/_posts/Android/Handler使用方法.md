---
title: Handler 使用方法记录
date: 2016-03-18 21:38:14
tags: Handler
categories: android

---



# Handler 使用方法记录

**涉及API**

- `Handler`
- `Looper`
- `Message`
- `Binder`

### Handler对象的创建方法
>Handler对象的创建前提是当前Thread中有Looper

1

	Handler handler = new Handler(){
        @Override
        public void handleMessage(Message msg) {
            super.handleMessage(msg);
        }
    };
2 一般用这个，传入`this` activity手动添加`implements`

	Handler handler = new Handler(new Handler.Callback() {
		@Override
		public boolean handleMessage(Message msg) {
			return true;
		}
	});	
### Message的操作
**创建方法**：

1. 创建新消息，这种方法效率极低，不推荐使用

		Message message = new Message();
2. 从消息池中取出一个消息，如果消息为空会创建新的

		Message message = Message.obtain();
3. 通过handler对象取出消息，消息的目标为handler

		Message message = handler.obtainMessage();

**Message类型设置**：
		
		message.what = 2; 

**存入要传递的数据：**

1. 存入简单数据：两个int一个object

		message.arg1 = 12;
		message.arg2 = 13;
		message.obj = 一个对象;

2. 存入复杂数据

		Bundle data = new Bundle();
		message.setData(data);

**发送消息：**

1. 发送有目标的Message对象

		message.sendToTarget();

2. 通过handler发送没有目标的Message对象

		handler.sendMessage(message)


### Looper操作
>子线程中要使用Looper必须自己创建，它通过一个局部线程变量`ThreadLocal`和线程以键值对的形式关联。

**创建方法：**在Handler创建之前执行

	Looper.prepare();

**开启死循环处理消息列队中的Message**

	Looper.loop();


## Handler主线程内部通信的例子
>实现效果：让Activity中的一个TextView实时更新时间


关键代码

Activity中的属性：

    private TextView mTextView;
    private Handler mHandler;
    private SimpleDateFormat mDateFormat = new SimpleDateFormat("HH:mm:ss", Locale.CHINA);
onCreate()方法内代码：

	mTextView = (TextView) findViewById(R.id.main_time);
	mHandler = new Handler(this);
	mHandler.sendEmptyMessage(1);
Activity实现`Handler.Callback`接口

    @Override
    public boolean handleMessage(Message msg) {
        switch (msg.what) {
            case 1:
                mTextView.setText(mDateFormat.format(new Date()));
                int item = mPager.getCurrentItem();
                if (item == mList.size() - 1) {
                    mPager.setCurrentItem(0, false);
                } else {
                    mPager.setCurrentItem(item + 1);
                }

                mHandler.sendEmptyMessageDelayed(1, 500);
                break;
        }
        return true; //??
    }

最终效果：

![时间会刷新](http://i.imgur.com/ZQxTZMr.png)

## Handler从主线程传送消息给子线程
1. 在主线程中声明`Handler`
2. 在子线程中赋值`Handler`实现`Handler.Callback`接口重写里面的方法
3. 在主线程中创建`Message`并设置目标为该`Handler`发送数据。
4. 子线程中`Handler.Callback`接口内部的方法处理数据。

## 从子线程传消息到主线程
1. 在主线程中声明`Handler`并赋值
2. 让`activity`实现`Handler.Callback`重写方法
3. 在子线程成中获取`Handler`对象并传递数据
4. 主线程`activity`中`Handler.Callback`里面的方法处理从子线程中传过来的数据。
