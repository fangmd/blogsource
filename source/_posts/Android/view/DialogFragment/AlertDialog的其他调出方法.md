---
title: AlertDialog调出方法 
date: 2016-04-02 12:18:12
tags: view
category: android

---

# AlertDialog调出方法

<!--more-->

参考：[知乎](https://www.zhihu.com/question/37929529/answer/74220125)


如何在无关`Activity`的场景比如`Service`、`Receiver`调出`AlertDialog`

	public class DialogContainerActivity extends Activity {
	
	    private static DialogContextProvider mContextProvider;
	
	    public static void show(DialogContextProvider contextProvider) {
	        mContextProvider = contextProvider;
	        Intent intent = new Intent(App.getInstance() /*全局 Application 实例*/, DialogContainerActivity.class);
	        intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
	        App.getApp().startActivity(intent);
	    }
	
	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        mContextProvider.getContext(this);
	    }
	
	    public interface DialogContextProvider {
	        void getContext(Activity activity);
	    }
	}