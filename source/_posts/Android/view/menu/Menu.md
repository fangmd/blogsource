---
title: Menu
date: 2016-04-03 09:23:14
tags: menu
categories: android

---



# Menu

## 涉及的API

- MenuInflater
- MenuItem
- ActionMode

<!--more-->


## Options Menu 系统菜单
>3.0以后菜单出现在`app标题bar处`，以前是在底部出现的。

>可以在activity和fragment中定义，两个在同一个界面出现的时候会合并，activity的在前，fragment的菜单在后，可以使用`android:orderInCategory`属性来分类


### 0 创建xml菜单项
>在`menu`文件夹下创建菜单项的xml文件

### 1 重写`onCreateOptionsMenu()`方法
将xml文件中的菜单项加载到菜单中。

	@Override 
	public boolean onCreateOptionsMenu(Menu menu) {
	    MenuInflater inflater = getMenuInflater();
	    inflater.inflate(R.menu.game_menu, menu);
	    return true; 
	} 

加载菜单项：

1. 动态code：`menu.add()`
2. 静态xml

### 添加菜单点击事件
	@Override 
	public boolean onOptionsItemSelected(MenuItem item) {
	    // Handle item selection 
	    switch (item.getItemId()) {
	        case R.id.new_game:
	            newGame(); 
	            return true; 
	        case R.id.help:
	            showHelp(); 
	            return true; 
	        default: 
	            return super.onOptionsItemSelected(item);
	    } 
	} 

**返回值：**如果处理了点击事件要返回`true`，没有就`super.onOptionsItemSelected(item)`默认放回是`false`

**点击事件分发：**当activity中包含fragment的时候，如果fragment中的菜单项也加入了菜单中，那么点击事件在处理的时候先执行activity中的`onOptionsItemSelected(MenuItem item)`如果没有对对这个点击事件返回`true`表示没有处理，就会调用fragment的`onOptionsItemSelected(MenuItem item)`；


### 动态改变菜单项
通常情况下`onCreateOptionsMenu()`方法只会执行一次，除非出现异常使菜单无效，所以在这个方法中一般只对菜单进行初始化。要动态改变菜单项应使用`onPrepareOptionsMenu()`；

使用`invalidateOptionsMenu()`方法让系统执行`onPrepareOptionsMenu()`方法来对菜单进行改变


### 给系统菜单添加图标
>反射

	private void setIconEnable(Menu menu, boolean enable){  
		try{  
			//MenuBuilder.class
			Class<?> clazz = Class.forName("com.android.internal.view.menu.MenuBuilder");  
			Method m = clazz.getDeclaredMethod("setOptionalIconsVisible", boolean.class);  
			m.setAccessible(true); 
			//MenuBuilder实现Menu接口，创建菜单时，传进来的menu其实就是MenuBuilder对象(java的多态特征)  
			m.invoke(menu, enable);  
	              
		} catch (Exception e)   
	        {  
	            e.printStackTrace();  
	        }  
	}



## Contextual(Context)  Menu 上下文菜单

### 分类
- 普通悬浮上下文菜单
- 多选上下文菜单 (结合`ActionMode`)

### 创建普通上下文菜单
1. 控件注册上下文菜单:view可以是`listview`、`gridview`

	view.registerForContextMenu()
	unregisterForContextMenu(View view)

给View注册上下文菜单，长按这个View就会触发菜单的创建方法

2. 在`activity`或者`fragment`中实现`onCreateContextMenu()`，当长按已注册的控件的时候系统会调用这个方法。

		@Override 
		public void onCreateContextMenu(ContextMenu menu, View v, ContextMenuInfo menuInfo) {
		    super.onCreateContextMenu(menu, v, menuInfo);
		    MenuInflater inflater = getMenuInflater();
		    inflater.inflate(R.menu.context_menu, menu);
		}
参数2：用户点击的控件；参数3：包含了控件的其他信息(比如`AdapterView`中的子控件触发菜单的时候，参数包含了`position`信息)

### 添加菜单项的点击事件
>和`option Menu`相同


	@Override 
	public boolean onContextItemSelected(MenuItem item) {
	    AdapterContextMenuInfo info = (AdapterContextMenuInfo) item.getMenuInfo();
	    switch (item.getItemId()) {
	        case R.id.edit:
	            editNote(info.id);
	            return true; 
	        case R.id.delete:
	            deleteNote(info.id);
	            return true; 
	        default: 
	            return super.onContextItemSelected(item);
	    } 
	} 

### 创建多选上下文菜单

1. 实现`ActionMode.Callback`接口

		private ActionMode.Callback mActionModeCallback = new ActionMode.Callback() {
		 
		    // Called when the action mode is created; startActionMode() was called 
		    @Override 
		    public boolean onCreateActionMode(ActionMode mode, Menu menu) {
		        // Inflate a menu resource providing context menu items 
		        MenuInflater inflater = mode.getMenuInflater();
		        inflater.inflate(R.menu.context_menu, menu);
		        return true; 
		    } 
		 
		    // Called each time the action mode is shown. Always called after onCreateActionMode, but 
		    // may be called multiple times if the mode is invalidated. 
		    @Override 
		    public boolean onPrepareActionMode(ActionMode mode, Menu menu) {
		        return false; // Return false if nothing is done 
		    } 
		 
		    // Called when the user selects a contextual menu item 
		    @Override 
		    public boolean onActionItemClicked(ActionMode mode, MenuItem item) {
		        switch (item.getItemId()) {
		            case R.id.menu_share:
		                shareCurrentItem(); 
		                mode.finish(); // Action picked, so close the CAB
		                return true; 
		            default: 
		                return false; 
		        } 
		    } 
		 
		    // Called when the user exits the action mode 
		    @Override 
		    public void onDestroyActionMode(ActionMode mode) {
		        mActionMode = null; 
		    } 
		}; 

2. 执行`startActionMode()`当需要调用菜单时

	单个view：

		someView.setOnLongClickListener(new View.OnLongClickListener() { 
		    // Called when the user long-clicks on someView 
		    public boolean onLongClick(View view) {
		        if (mActionMode != null) { 
		            return false; 
		        } 
		 
		        // Start the CAB using the ActionMode.Callback defined above 
		        mActionMode = getActivity().startActionMode(mActionModeCallback); 
		        view.setSelected(true);
		        return true; 
		    } 
		});

	listView和gridview：

		ListView listView = getListView();
		listView.setChoiceMode(ListView.CHOICE_MODE_MULTIPLE_MODAL); 
		listView.setMultiChoiceModeListener(new MultiChoiceModeListener() { 
	 
		    @Override 
		    public void onItemCheckedStateChanged(ActionMode mode, int position,
		                                          long id, boolean checked) {
		        // Here you can do something when items are selected/de-selected, 
		        // such as update the title in the CAB 
		    } 
		 
		    @Override 
		    public boolean onActionItemClicked(ActionMode mode, MenuItem item) {
		        // Respond to clicks on the actions in the CAB 
		        switch (item.getItemId()) {
		            case R.id.menu_delete:
		                deleteSelectedItems(); 
		                mode.finish(); // Action picked, so close the CAB
		                return true; 
		            default: 
		                return false; 
		        } 
		    } 
		 
		    @Override 
		    public boolean onCreateActionMode(ActionMode mode, Menu menu) {
		        // Inflate the menu for the CAB 
		        MenuInflater inflater = mode.getMenuInflater();
		        inflater.inflate(R.menu.context, menu);
		        return true; 
		    } 
		 
		    @Override 
		    public void onDestroyActionMode(ActionMode mode) {
		        // Here you can make any necessary updates to the activity when 
		        // the CAB is removed. By default, selected items are deselected/unchecked. 
		    } 
		 
		    @Override 
		    public boolean onPrepareActionMode(ActionMode mode, Menu menu) {
		        // Here you can perform updates to the CAB due to 
		        // an invalidate() request 
		        return false; 
		    } 
		});  

## Popup Menu

## 创建和使用Popup Menu
1. 创建对象

		PopupMenu popup = new PopupMenu(this, v);
参数1：Context； 参数2：出现位置，以view为锚点

2. 菜单项生成

		MenuInflater inflater = popup.getMenuInflater();
		inflater.inflate(R.menu.actions, popup.getMenu());
3. 调用菜单

		popup.show();

4. 添加菜单项点击监听

		popup.setOnMenuItemClickListener(this);

		Override 
		public boolean onMenuItemClick(MenuItem item) {
		    switch (item.getItemId()) {
		        case R.id.archive:
		            archive(item);
		            return true; 
		        case R.id.delete:
		            delete(item);
		            return true; 
		        default: 
		            return false; 
		    } 
		} 

## Menu Group

[http://developer.android.com/guide/topics/ui/menus.html#groups](http://developer.android.com/guide/topics/ui/menus.html#groups)
