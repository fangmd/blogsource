---
title: 双击back退出应用程序
date: 2016-04-04 09:23:14
tags: features
categories: android

---

# 双击back退出应用程序

参考：[http://blog.csdn.net/yelangjueqi/article/details/9472705](http://blog.csdn.net/yelangjueqi/article/details/9472705)

## 最优方法

<!--more-->
    @Override
    public boolean onKeyDown(int keyCode, KeyEvent event) {
        if (keyCode == KeyEvent.KEYCODE_BACK) {
            if (System.currentTimeMillis() - mExitTime > 2000) {
                Toast.makeText(MainActivity.this, "再次点击退出应用", Toast.LENGTH_SHORT).show();
                mExitTime = System.currentTimeMillis();
            } else {
                System.exit(0);
            }
            return true;
        }
        return super.onKeyShortcut(keyCode, event);
    }

## 方法二：

	@Override  
	public boolean onKeyDown(int keyCode, KeyEvent event) {  
		if (keyCode == KeyEvent.KEYCODE_BACK) {  
		if (isQuit == false) {  
                isQuit = true;  
                Toast.makeText(getBaseContext(), "再按一次返回键退出程序",  
                        Toast.LENGTH_SHORT).show();  
                TimerTask task = null;  
                task = new TimerTask() {  
                    public void run() {  
                        isQuit = false;  
                    }  
                };  
                timer.schedule(task, 2000);  
            } else {  
                finish();  
                System.exit(0);  
            }  
        }  
        return false;  
    }

## 方法三：

```java
    Handler mHandler = new Handler() {  
        @Override  
        public void handleMessage(Message msg) {  
            super.handleMessage(msg);  
            isQuit = false;  
        }  
    };  
  
    @Override  
    public boolean onKeyDown(int keyCode, KeyEvent event) {  
        if (keyCode == KeyEvent.KEYCODE_BACK) {  
            if (!isQuit) {  
                isQuit = true;  
                Toast.makeText(getApplicationContext(), "再按一次退出程序",  
                        Toast.LENGTH_SHORT).show();  
                // 利用handler延迟发送更改状态信息  
                mHandler.sendEmptyMessageDelayed(0, 2000);  
            } else {  
                finish();  
                System.exit(0);  
            }  
        }  
        return false;  
    } 
```

