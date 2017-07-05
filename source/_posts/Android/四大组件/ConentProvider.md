---
title: ContentProvider
date: 2016-04-05 11:38:14
tags: ContentProvider
categories: android

---

# ContentProvider

通过数据库，提供本app生成的数据给其他的应用调用；

通过SQLiteOpenHelper的实现类，创建数据库

## 创建一个ContentProvider的步骤

### 1 创建一个ContentProvider的实现类，并完成一下步骤
1. 定义该ContentProvider组件的唯一标识,通常用 包名+数据库名（必须是小写）
2. 为该组件中可以被其它应用访问的表定义CODE标识
3. 定义UriMatcher对象,用于生成uri
4. 在onCreate中，初始化SQLiteOpenHelper 的子类
5. 增删改查方法实现

### 2 创建该组件的访问权限和使用权限
>Manifest

	<!--  访问该组件的权限  -->
	<permission android:name="com.example.contentprovider.READ_WRITE"/>
	<!--  使用该组件的权限  -->
	<uses-permission android:name="com.example.contentprovider.READ_WRITE"/>

### 3 注册ContentProvider组件
>Manifest

	<!--
	 注册ContentProvider组件 
		android:name:ContenetProvider组件的包名类名
		android:authorities：ContenetProvider组件的唯一标识
		android:permission:访问ContenetProvider组件的权限
		android:exported:让其他应用可以访问该组件
		
	-->
	<provider android:name="com.example.gp11_day15_contentprovider.provider.UserContentProvider"
		android:authorities="com.example.contentprovider.user" 
		android:permission="com.example.contentprovider.READ_WRITE" 
		android:exported="true"/>


## 简单的ContentProvider实现类

	/**
	 * 定义ContentProvider组件，把当前应用下的私有数据库提供给外界
	 */
	public class UserContentProvider extends ContentProvider {

		// 1. 定义该ContentProvider组件的唯一标识,通常用 包名+数据库名（必须是小写）
		public static final String AUTHORITY ="com.example.contentprovider.user"; 
	
		// 2. 为该组件中可以被其它应用访问的表定义CODE标识
		public static final int CODE_USER = 1;
		public static final int CODE_ORDER = 2;
	
		// 3. 定义UriMatcher对象,用于生成uri
		public static UriMatcher uriMatcher;
	
		static{
			uriMatcher = new UriMatcher(UriMatcher.NO_MATCH);
			//为t_user表生成uri,同时和Code标识相对应
			//content://com.example.contentprovider.user/user
			uriMatcher.addURI(AUTHORITY, "user", CODE_USER);
			//为t_order表生成uri
			//content://com.example.contentprovider.user/order
			uriMatcher.addURI(AUTHORITY, "order", CODE_ORDER);
		}

		private DbHelper dbHelper;
	
		@Override
		public boolean onCreate() {
			// 4. 在onCreate中，初始化SQLiteOpenHelper 的子类
			dbHelper = new DbHelper(getContext());
			return false;
		}
	    //对表的增删改查都是由ContentProvider组件实现的， ContentResolver只是在调用ContentProvider提供的
		//增删改查的功能
		@Override
		public Cursor query(Uri uri, String[] projection, String selection,
				String[] selectionArgs, String sortOrder) {
			SQLiteDatabase db = dbHelper.getReadableDatabase();
			Cursor cursor =null;
			int code = uriMatcher.match(uri);//根据uri返回code标识
			switch(code)
			{
			   case CODE_USER:
				     cursor = db.query("t_user", projection, selection, selectionArgs, null, null, sortOrder);
				     break;
			   case CODE_ORDER:
			}
			return cursor;
		}

		@Override
		public String getType(Uri uri) {
			// TODO Auto-generated method stub
			return null;
		}

		@Override
		public Uri insert(Uri uri, ContentValues values) {
			SQLiteDatabase db = dbHelper.getWritableDatabase();
			if(uriMatcher.match(uri)==this.CODE_USER)
			{
				long id = db.insert("t_user", null, values);
			
				//把新记录的id封装成uri
				return ContentUris.withAppendedId(uri, id);
			}
			return null;
		}

		@Override
		public int delete(Uri uri, String selection, String[] selectionArgs) {
			SQLiteDatabase db = dbHelper.getWritableDatabase();
			int num = 0;
			if(uriMatcher.match(uri)==this.CODE_USER)
			{
				num = db.delete("t_user", selection, selectionArgs);
			}
			return num;
		}

		@Override
		public int update(Uri uri, ContentValues values, String selection,
				String[] selectionArgs) {
			SQLiteDatabase db = dbHelper.getWritableDatabase();
			int num = 0;
			if(uriMatcher.match(uri)==this.CODE_USER)
			{
				num = db.update("t_user", values, selection, selectionArgs);
			}
			return num;
		}

	}


## CursorLoader(会开启子线程)

