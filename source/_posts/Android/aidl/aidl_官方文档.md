---
title: AIDL 官方文档
date: 2016-07-22 13:18:12
tags: [aidl, 官方文档]
category: android

---

AIDL (Android Interface Definition Language):用于进程间通讯.

# 定义一个AIDL接口

1. Create the `.aidl` 文件:这个文件内定义了相关的接口方法
2. Implement the interface:实现上面定义的接口文件, 会自动生成`Stub`类并且实现了上面接口中定义的方法.
3. Expose the interface to client:向客户端提供接口, 创建一个`Service` 重写`onBind()`方法返回`Stub`类对象.

## Create the .aidl 文件

这个文件中定义一些方法,通过这些方法的返回值,获得数据, 默认支持的数据类型可以为:

1. 基本数据类型
2. String
3. CharSequence
4. List
5. Map

其他自定义的类,需要导入.

## Implement the interface
运行工程后会生成相应的`.java`文件,这个文件中有个内部类`Stub`

`Stub`类中有个方法`asInterface()`返回`IBinder`(在服务端调用`onServiceConnected()`方法的参数)

实现`.aidl`中定义的方法需要继承`Binder`接口(Stub)实现里面的方法,

	private final IRemoteService.Stub mBinder = new IRemoteService.Stub() {
	    public int getPid(){ 
		return Process.myPid(); 
	    } 
	    public void basicTypes(int anInt, long aLong, boolean aBoolean,
		float aFloat, double aDouble, String aString) {
		// Does nothing 
	    } 
	}; 

这里的`mBinder`是`Stub`的实现类,

## Expose the interface to clients
通过服务来实现: 服务端

```
	public class RemoteService extends Service {
	    @Override 
	    public void onCreate() { 
				super.onCreate(); 
	    }
	 
	    @Override 
	    public IBinder onBind(Intent intent) {
		// Return the interface 
		return mBinder;
	    } 
	 
	    private final IRemoteService.Stub mBinder = new IRemoteService.Stub() {
		public int getPid(){ 
		    return Process.myPid(); 
		} 
		public void basicTypes(int anInt, long aLong, boolean aBoolean,
		    float aFloat, double aDouble, String aString) {
		    // Does nothing 
		} 
	    }; 
	}
```

客户端: 

```
	IRemoteService mIRemoteService;
	private ServiceConnection mConnection = new ServiceConnection() {
	    // Called when the connection with the service is established 
	    public void onServiceConnected(ComponentName className, IBinder service) {
		// Following the example above for an AIDL interface, 
		// this gets an instance of the IRemoteInterface, which we can use to call on the service 
		mIRemoteService = IRemoteService.Stub.asInterface(service);
	    } 
	 
	    // Called when the connection with the service disconnects unexpectedly 
	    public void onServiceDisconnected(ComponentName className) {
		Log.e(TAG, "Service has unexpectedly disconnected");
		mIRemoteService = null;
	    } 
	}; 
```

# Passing Objects over IPC
在进程间传递自己定义的对象, 需要对象的类实现序列化接口`Parcelable`

# Calling an IPC Method
一个调用AIDL的类需要做的:

1. 和服务端相同的AIDL文件
2. Declare an instance of the IBinder interface (generated based on the AIDL).
3. 实现`ServiceConnection`
4. 调用`Context.bindService()`,传入`ServiceConnextion`实现类
5. 在`onServiceConnected()`方法中获取`IBinder`对象,` YourInterfaceName.Stub.asInterface((IBinder)service) `
6. 调用`IBinder`对象的方法,获取服务端的数据.
7. 断开连接:`Context.unbindService()`



		public static class Binding extends Activity {
		    /** The primary interface we will be calling on the service. */ 
		    IRemoteService mService = null;
		    /** Another interface we use on the service. */ 
		    ISecondary mSecondaryService = null;
		 
		    Button mKillButton;
		    TextView mCallbackText;
		 
		    private boolean mIsBound;
		 
		    /** 
		     * Standard initialization of this activity.  Set up the UI, then wait 
		     * for the user to poke it before doing anything. 
		     */ 
		    @Override 
		    protected void onCreate(Bundle savedInstanceState) {
			super.onCreate(savedInstanceState);
		 
			setContentView(R.layout.remote_service_binding);
		 
			// Watch for button clicks. 
			Button button = (Button)findViewById(R.id.bind);
			button.setOnClickListener(mBindListener);
			button = (Button)findViewById(R.id.unbind);
			button.setOnClickListener(mUnbindListener);
			mKillButton = (Button)findViewById(R.id.kill);
			mKillButton.setOnClickListener(mKillListener);
			mKillButton.setEnabled(false);
		 
			mCallbackText = (TextView)findViewById(R.id.callback);
			mCallbackText.setText("Not attached.");
		    } 
		 
		    /** 
		     * Class for interacting with the main interface of the service. 
		     */ 
		    private ServiceConnection mConnection = new ServiceConnection() {
			public void onServiceConnected(ComponentName className,
				IBinder service) {
			    // This is called when the connection with the service has been 
			    // established, giving us the service object we can use to 
			    // interact with the service.  We are communicating with our 
			    // service through an IDL interface, so get a client-side 
			    // representation of that from the raw service object. 
			    mService = IRemoteService.Stub.asInterface(service);
			    mKillButton.setEnabled(true);
			    mCallbackText.setText("Attached.");
		 
			    // We want to monitor the service for as long as we are 
			    // connected to it. 
			    try { 
				mService.registerCallback(mCallback);
			    } catch (RemoteException e) {
				// In this case the service has crashed before we could even 
				// do anything with it; we can count on soon being 
				// disconnected (and then reconnected if it can be restarted) 
				// so there is no need to do anything here. 
			    } 
		 
			    // As part of the sample, tell the user what happened. 
			    Toast.makeText(Binding.this, R.string.remote_service_connected,
				    Toast.LENGTH_SHORT).show();
			} 
		 
			public void onServiceDisconnected(ComponentName className) {
			    // This is called when the connection with the service has been 
			    // unexpectedly disconnected -- that is, its process crashed. 
			    mService = null;
			    mKillButton.setEnabled(false);
			    mCallbackText.setText("Disconnected.");
		 
			    // As part of the sample, tell the user what happened. 
			    Toast.makeText(Binding.this, R.string.remote_service_disconnected,
				    Toast.LENGTH_SHORT).show();
			} 
		    }; 
		 
		    /** 
		     * Class for interacting with the secondary interface of the service. 
		     */ 
		    private ServiceConnection mSecondaryConnection = new ServiceConnection() {
			public void onServiceConnected(ComponentName className,
				IBinder service) {
			    // Connecting to a secondary interface is the same as any 
			    // other interface. 
			    mSecondaryService = ISecondary.Stub.asInterface(service);
			    mKillButton.setEnabled(true);
			} 
		 
			public void onServiceDisconnected(ComponentName className) {
			    mSecondaryService = null;
			    mKillButton.setEnabled(false);
			} 
		    }; 
		 
		    private OnClickListener mBindListener = new OnClickListener() {
			public void onClick(View v) {
			    // Establish a couple connections with the service, binding 
			    // by interface names.  This allows other applications to be 
			    // installed that replace the remote service by implementing 
			    // the same interface. 
			    Intent intent = new Intent(Binding.this, RemoteService.class);
			    intent.setAction(IRemoteService.class.getName());
			    bindService(intent, mConnection, Context.BIND_AUTO_CREATE);
			    intent.setAction(ISecondary.class.getName());
			    bindService(intent, mSecondaryConnection, Context.BIND_AUTO_CREATE);
			    mIsBound = true;
			    mCallbackText.setText("Binding.");
			} 
		    }; 
		 
		    private OnClickListener mUnbindListener = new OnClickListener() {
			public void onClick(View v) {
			    if (mIsBound) {
				// If we have received the service, and hence registered with 
				// it, then now is the time to unregister. 
				if (mService != null) {
				    try { 
				        mService.unregisterCallback(mCallback);
				    } catch (RemoteException e) {
				        // There is nothing special we need to do if the service 
				        // has crashed. 
				    } 
				} 
		 
				// Detach our existing connection. 
				unbindService(mConnection);
				unbindService(mSecondaryConnection);
				mKillButton.setEnabled(false);
				mIsBound = false;
				mCallbackText.setText("Unbinding.");
			    } 
			} 
		    }; 
		 
		    private OnClickListener mKillListener = new OnClickListener() {
			public void onClick(View v) {
			    // To kill the process hosting our service, we need to know its 
			    // PID.  Conveniently our service has a call that will return 
			    // to us that information. 
			    if (mSecondaryService != null) {
				try { 
				    int pid = mSecondaryService.getPid();
				    // Note that, though this API allows us to request to 
				    // kill any process based on its PID, the kernel will 
				    // still impose standard restrictions on which PIDs you 
				    // are actually able to kill.  Typically this means only 
				    // the process running your application and any additional 
				    // processes created by that app as shown here; packages 
				    // sharing a common UID will also be able to kill each 
				    // other's processes. 
				    Process.killProcess(pid);
				    mCallbackText.setText("Killed service process.");
				} catch (RemoteException ex) {
				    // Recover gracefully from the process hosting the 
				    // server dying. 
				    // Just for purposes of the sample, put up a notification. 
				    Toast.makeText(Binding.this,
				            R.string.remote_call_failed,
				            Toast.LENGTH_SHORT).show();
				} 
			    } 
			} 
		    }; 
		 
		    // ---------------------------------------------------------------------- 
		    // Code showing how to deal with callbacks. 
		    // ---------------------------------------------------------------------- 
		 
		    /** 
		     * This implementation is used to receive callbacks from the remote 
		     * service. 
		     */ 
		    private IRemoteServiceCallback mCallback = new IRemoteServiceCallback.Stub() {
			/** 
			 * This is called by the remote service regularly to tell us about 
			 * new values.  Note that IPC calls are dispatched through a thread 
			 * pool running in each process, so the code executing here will 
			 * NOT be running in our main thread like most other things -- so, 
			 * to update the UI, we need to use a Handler to hop over there. 
			 */ 
			public void valueChanged(int value) {
			    mHandler.sendMessage(mHandler.obtainMessage(BUMP_MSG, value, 0));
			} 
		    }; 
		 
		    private static final int BUMP_MSG = 1;
		 
		    private Handler mHandler = new Handler() { 
			@Override public void handleMessage(Message msg) { 
			    switch (msg.what) { 
				case BUMP_MSG: 
				    mCallbackText.setText("Received from service: " + msg.arg1); 
				    break; 
				default: 
				    super.handleMessage(msg); 
			    } 
			} 
		 
		    }; 
		} 	


