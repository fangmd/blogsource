---
title: Dialog官方文档总结
date: 2016-03-14 21:38:14
tags: Dialog
categories: android

---

# DialogFragment 不会触发 Activity 的 onPause 方法

1. onPause: Called when the system is about to start resuming another activity. 
2. onPause() is called when the device goes to sleep or when a dialog appears. 

普通的 Dialog 创建的时候是基于 透明Activity 的所以会触发 onPause 方法

但是 DialogFragment 不会创建 透明Activity 所以不会触发 onPause 方法


# Dialog官方文档总结
>file:///C:/MyProgram/androidprogram/adt-bundle-windows-x86_64-20140702/sdk/docs/guide/topics/ui/dialogs.html

<!--more-->

## 使用 DialogFragment 来管理Dialog
1. 创建一个类继承DialogFragment
2. 重写onCreateDialog()方法，这个方法内创建Dialog，并将创建好的Dialog返回

		public Dialog onCreateDialog(Bundle savedInstanceState)
		
3. 在需要的地方(activity)创建这个类的实例，调用`show()`方法就可以让DialogFragment出现。



## 创建Dialog的方法
### AlertDialog.Builder
1. Dialog分三个区域
	1. Title
	2. Content area：不能同时放message和list
	3. Action buttons:最多三个

2. 创建AlertDialog.Builder对象

		AlertDialog.Builder builder = new AlertDialog.Builder(getActivity());
3. AlertDialog.Builder里面的方法
	1. setTitle()
	2. setMessage()
	3. setPositiveButton()
	4. setNegativeButton()
	5. setNeutralButton()
	3. create()
	
4. 添加Button
	1. setPositiveButton()
	2. setNegativeButton()
	3. setNeutralButton()

5. 添加List
	1. A traditional single-choice list **单选**
		1. setItems()

			.setItems(R.array.colors_array, new DialogInterface.OnClickListener() {
	               public void onClick(DialogInterface dialog, int which) {
	               // The 'which' argument contains the index position
	               // of the selected item
	           }
		2. setAdapter(),可能还需要`Loader`异步加载数据

				public Builder setAdapter(final ListAdapter adapter, final OnClickListener listener)
	2. A persistent single-choice list (radio buttons) **单选**
		1. setSingleChoiceItems()
	3. A persistent multiple-choice list (checkboxes) **多选**
		1. setMultiChoiceItems()
		2. 这里需要创建一个属性，存储点击的项目


				private ArrayList mSelectedItems;

				.setMultiChoiceItems(R.array.toppings, null,
					                      new DialogInterface.OnMultiChoiceClickListener() {
					               @Override
					               public void onClick(DialogInterface dialog, int which,
					                       boolean isChecked) {
					                   if (isChecked) {
					                       // If the user checked the item, add it to the selected items
					                       mSelectedItems.add(which);
					                   } else if (mSelectedItems.contains(which)) {
					                       // Else, if the item is already in the array, remove it 
					                       mSelectedItems.remove(Integer.valueOf(which));
					                   }
					               }
					           })

### 创建自定义布局
方法一：

1. 创建xml布局文件
2. 使用LayoutInflater创建布局文件的View
3. `setView(..)`

		LayoutInflater inflater = getActivity().getLayoutInflater();
	    builder.setView(inflater.inflate(R.layout.dialog_signin, null))

方法二：将activity作为dialog
1. 创建一个activity
2. 设置activity的xml属性

	<activity android:theme="@android:style/Theme.Holo.Dialog" >

## Passing Events Back to the Dialog's Host， Fragment的接口回调
1. 创建的DialogFragment子类要创建一个内部接口

		public interface NoticeDialogListener {
        	public void onDialogPositiveClick(DialogFragment dialog);
       		public void onDialogNegativeClick(DialogFragment dialog);
   		}
2. DialogFragment子类的成员属性

		NoticeDialogListener mListener;
3. 重写onAttach()方法(注意选择正确的方法)，这里的接口回调没有用构造方法的形式，而是将activity作为实现类利用DialogFragment的特点传入。

		// Override the Fragment.onAttach() method to instantiate the NoticeDialogListener
	    @Override
	    public void onAttach(Activity activity) {
	        super.onAttach(activity);
	        // Verify that the host activity implements the callback interface
	        try {
	            // Instantiate the NoticeDialogListener so we can send events to the host
	            mListener = (NoticeDialogListener) activity;
	        } catch (ClassCastException e) {
	            // The activity doesn't implement the interface, throw exception
	            throw new ClassCastException(activity.toString()
	                    + " must implement NoticeDialogListener");
	        }
	    }
4. 使用这个DialogFragment的activity需要写的代码

	接口方法重写	

		@Override
	    public void onDialogPositiveClick(DialogFragment dialog) {
	        // User touched the dialog's positive button
	        ...
	    }
	
	    @Override
	    public void onDialogNegativeClick(DialogFragment dialog) {
	        // User touched the dialog's negative button
	        ...
	    }

	不必要的方法

		public void showNoticeDialog() {
	        // Create an instance of the dialog fragment and show it
	        DialogFragment dialog = new NoticeDialogFragment();
	        dialog.show(getSupportFragmentManager(), "NoticeDialogFragment");
	    }
5. 在DialogFragment子类中分配接口的方法

		mListener.onDialogPositiveClick(NoticeDialogFragment.this);

## Showing a Dialog 显示Dialog
1. 在需要调用的地方创建DialogFragment的子类实例
2. 调用`show(FragmentManager,Tag)`方法


### FragmentManager:`getFragmentManager()`
### Tag:string 类型
作用：
1. `findFragmentByTag()`，在fragment中获取`fragmentManager`，通过`FragmentManager`的`findFragmentByTag(“。。。”)`获取另一个fragment实例达到两个fragment通信的功能。
2. 在activity中启动dialog的时候调用这个方法，`tag`设置后可以在`DialogFragment`中通过`getTag()`方法获取tag从而知道是谁启动了这个dialogfragment。通过判断不同的tag，`DialogFragment`可以在`onCreateDialog()`方法中初始化不同界面的`Dialog`

## Showing a Dialog Fullscreen or as an Embedded Fragment 全屏显示Dialog，内嵌Dialog

	public void showDialog() {
	    FragmentManager fragmentManager = getSupportFragmentManager();
	    CustomDialogFragment newFragment = new CustomDialogFragment();
	    
	    if (mIsLargeLayout) {
	        // The device is using a large layout, so show the fragment as a dialog
	        newFragment.show(fragmentManager, "dialog");
	    } else {
	        // The device is smaller, so show the fragment fullscreen
	        FragmentTransaction transaction = fragmentManager.beginTransaction();
	        // For a little polish, specify a transition animation
	        transaction.setTransition(FragmentTransaction.TRANSIT_FRAGMENT_OPEN);
	        // To make it fullscreen, use the 'content' root view as the container
	        // for the fragment, which is always the root view for the activity
	        transaction.add(android.R.id.content, newFragment)
	                   .addToBackStack(null).commit();
	    }
	}
	
1. 注意里面`android.R.id.content`
2. 屏幕大小问题
	1. 在values下创建xml

					<!-- Default boolean values -->
			<resources>
			    <bool name="large_layout">false</bool>
			</resources>
	2. 在values-w820dp下创建xml

					<!-- Large screen boolean values -->
			<resources>
			    <bool name="large_layout">true</bool>
			</resources>
	3. java代码

			boolean mIsLargeLayout;
			
			@Override
			public void onCreate(Bundle savedInstanceState) {
			    super.onCreate(savedInstanceState);
			    setContentView(R.layout.activity_main);
			
			    mIsLargeLayout = getResources().getBoolean(R.bool.large_layout);
			}


## Showing an activity as a dialog on large screens
在大屏幕手机上把一个activity作为对话框形式出现的方法

设置清单文件:

	<activity android:theme="@android:style/Theme.Holo.DialogWhenLarge" >

## Dismissing a Dialog Dialog消失
1. 正常消失
	1. 点击了对话框中的button
	2. 单选的时候点击了List

2. 调用DialogFragment的`dismiss()`方法
	1. 会执行回调方法`onDismiss()`
3. 调用DialogFragment的`cancel()`方法
	1. 会执行回调方法`onCancel()`

3. 当我们要在DialogFragment消失的时候做一些操作的时候
	1. 重写`onDissmiss()`方法

4. `onCancel()方法`调用时机
	1. 点击Dialog外面
	2. 点击手机back键

	注意:点击PositiveButton的时候不会调用`onCancel()`方法

## Fragment和Activity交互
比较好的方式是：接口回调

1. 定义一个接口

		public interface DataCallback {

		    public void getData(String data);
		}

2. 让使用这个fragment的activity实现这个接口

3. 在fragment中使用接口方法

	    @Override
	    public void onAttach(Activity activity) {
		// onAttach()是合适的早期阶段进行检查MyActivity是否真的实现了接口。
		// 采用接口的方式，dialog无需详细了解MyActivity，只需了解其所需的接口函数，这是真正项目中应采用的方式。
		if (!(activity instanceof DataCallback)) {
		    throw new IllegalStateException("fragment所在的Activity必须实现Callbacks接口");
		}
		super.onAttach(activity);
	    }

	    @Override
	    public void onClick(DialogInterface dialog, int which) {
		DataCallback callback = (DataCallback) getActivity();
		callback.getData("test");
	    }

## 用DialogFragment实现二次弹窗

	FragmentTransaction ft = getFragmentManager().beginTransaction();

        /*
         * 如果不执行remove()，对话框即不会进入onDismiss()状态。会被显示在新的对话框下方，是可见的。
         * 主要考虑美观的问题，如果下面的对话框大于上面的对话框就很难看了。 对于Dialog，container为0或者null。
         */
        ft.remove(this);
        /*
         * 将当前的PromptDialogFragment加入到回退堆栈，当用户按返回键，或者通过按帮助框的Close按钮dismiss帮助框是，
         * 重新显示提示框。 对于back stack的处理，系统具有一定的智能。例如：执行两次addToStackStack()，实际不会重复压栈。
         * 有例如：注释掉remove()语句，即提示框不消失，而是在帮助框的下面。
         * 但是在实验中发现是否有addToBackStack()都不会结果有影响，系统能够分析到对象存在，不需要压栈。没有去查源代码，
         * 猜测通过mBackStackId比对来进行智能处理。
         */
        ft.addToBackStack(null);
        
        new OhterDialogFragment()
        .show(getFragmentManager(), "dialog_fragment");


## 设置出现动画

	alertDialog.getWindow().getAttributes().windowAnimations = R.style.dialogAnim;

style文件：

    <style name="dialogAnim" mce_bogus="1" parent="android:Animation">
        <item name="android:windowEnterAnimation">@anim/dialog_push_in</item>
        <item name="android:windowExitAnimation">@anim/dialog_push_out</item>
    </style>

动画xml文件：dialog_push_in

	<?xml version="1.0" encoding="utf-8"?>
	<set xmlns:android="http://schemas.android.com/apk/res/android">

	    <translate
		android:duration="@android:integer/config_mediumAnimTime"
		android:fromYDelta="100%p"
		android:toYDelta="0%p" />

	    <alpha
		android:duration="@android:integer/config_mediumAnimTime"
		android:fromAlpha="0.0"
		android:toAlpha="1.0" />

	</set>

动画xml文件：dialog_push_out

	<?xml version="1.0" encoding="utf-8"?>
	<set xmlns:android="http://schemas.android.com/apk/res/android">
		<translate
		    android:duration="@android:integer/config_mediumAnimTime"
		    android:fromYDelta="0%p"
		    android:toYDelta="100%p" />

		<alpha
		    android:duration="@android:integer/config_mediumAnimTime"
		    android:fromAlpha="1.0"
		    android:toAlpha="0.3" />
	</set>

## 自定义DialogFragment出现位置和宽高

		Window window = dialog.getWindow();
		WindowManager.LayoutParams wlp = window.getAttributes();
		wlp.gravity = Gravity.BOTTOM;
		wlp.width = WindowManager.LayoutParams.MATCH_PARENT;
		window.setAttributes(wlp);

## dialog 宽度占满屏幕实例，设置 dialog 宽高

    @NonNull
    @Override
    public Dialog onCreateDialog(Bundle savedInstanceState) {
        AlertDialog dialog = new AlertDialog.Builder(getActivity()).create();
        dialog.show();
        Window window = dialog.getWindow();
        window.setBackgroundDrawable(null);
        window.setContentView(R.layout.share_fragment);
        window.setLayout(WindowManager.LayoutParams.MATCH_PARENT, WindowManager.LayoutParams.WRAP_CONTENT); 	// 设置宽高
        WindowManager.LayoutParams attributes = window.getAttributes();
        attributes.windowAnimations = R.style.dialogAnim;	// 出现动画
        attributes.gravity = Gravity.BOTTOM;
        window.setAttributes(attributes);
        return dialog;
    }

>下面的方法有时候可行

    @NonNull
    @Override
    public Dialog onCreateDialog(Bundle savedInstanceState) {
        AlertDialog dialog = new AlertDialog.Builder(getActivity()).create();
        dialog.show();
        Window window = dialog.getWindow();
        window.setContentView(R.layout.share_fragment);
        WindowManager.LayoutParams attributes = window.getAttributes();
        attributes.windowAnimations = R.style.dialogAnim;
        attributes.gravity = Gravity.BOTTOM;
        attributes.width = WindowManager.LayoutParams.MATCH_PARENT;
        window.setAttributes(attributes);
        return dialog;
    }

## DialogFragment里面EditText调出键盘

	//只用下面这一行弹出对话框时需要点击输入框才能弹出软键盘  
	alert_leaveMsg.getWindow().clearFlags(WindowManager.LayoutParams.FLAG_ALT_FOCUSABLE_IM);  
	//加上下面这一行弹出对话框时软键盘随之弹出  
	alert_leaveMsg.getWindow().setSoftInputMode(WindowManager.LayoutParams.SOFT_INPUT_STATE_ALWAYS_VISIBLE);  

## 设置圆角Dialog

	public Dialog onCreateDialog(Bundle savedInstanceState) {

		View view = LayoutInflater.from(getContext()).inflate(R.layout.meida_dialog, null);
		AlertDialog alertDialog = new AlertDialog.Builder(getContext()).create();

		alertDialog.show();
		Window window = alertDialog.getWindow();
        	window.setBackgroundDrawable(null);
        	window.setContentView(view);
	。。。
	}

shape：

	<?xml version="1.0" encoding="utf-8"?>
	<shape xmlns:android="http://schemas.android.com/apk/res/android"
	       android:shape="rectangle">

	    <corners android:radius="10dp"/>

	    <stroke
		android:width="1dp"
		android:color="@color/colorPrimary"/>

	    <solid android:color="@android:color/white"/>


	</shape>

## 点击外面不取消Dialog

	alertDialog.setCanceledOnTouchOutside(false);

方法二:

	@Override      
	 public void onCreate(Bundle icicle)  
	 {  
	    super.onCreate(icicle);  
	    this.setCancelable(false);// 设置点击屏幕Dialog不消失  
	     int style = DialogFragment.STYLE_NORMAL, theme = 0;  
	     setStyle(style,theme);  
 	}  

## 点击 Dialog 确定或者取消按钮的时候Dialog不消失

	canCloseDialog(dialogInterface, false);//不关闭对话框  
	canCloseDialog(dialogInterface, true);//关闭对话框  

	//  关键部分在这里  
	private void canCloseDialog(DialogInterface dialogInterface, boolean close) {  
	    try {  
	         Field field = dialogInterface.getClass().getSuperclass().getDeclaredField("mShowing");  
	         field.setAccessible(true);  
	         field.set(dialogInterface, close);  
	     } catch (Exception e) {  
	         e.printStackTrace();  
	     }  
	 }  

## DialogFragment 设置背景透明


在 onStart 中设置 window 背景透明

```
    @Override
    public void onStart() {
        super.onStart();
        Window window = getDialog().getWindow();
        WindowManager.LayoutParams windowParams = window.getAttributes();
        windowParams.dimAmount = 0.0f;
        window.setAttributes(windowParams);
    }
```

下面代码设置 dialog 背景透明

```
alertDialog.getWindow().setBackgroundDrawable(new ColorDrawable(Color.TRANSPARENT));
```

