---
title: DataBinding
date: 2016-04-22 08:00:35
tags: DataBinding
categories: android

---

# DataBinding
>代替findViewById的官方方案

官方文档：[http://developer.android.com/intl/zh-cn/tools/data-binding/guide.html#data_binding_layout_files](http://developer.android.com/intl/zh-cn/tools/data-binding/guide.html#data_binding_layout_files)

1. 它在支持包中，可以兼容低版本(到Android 2.1 API 7)
2. 必须使用`Gradle 1.5.0-alphal`以上编译

## 0 配置环境
>导入支持包

在`build.gradle`文件中添加：

	android {
    	....
    	dataBinding {
    	    enabled = true
    	}
	}

<!--more-->
## 1 一个简单的例子

### 创建一个数据实体
	public class User {
	   public final String firstName;
	   public final String lastName;
	   public User(String firstName, String lastName) {
	       this.firstName = firstName;
	       this.lastName = lastName;
	   }

		// getter/setter
	}


### DataBinding适用的布局文件
	
	<?xml version="1.0" encoding="utf-8"?>
	<layout xmlns:android="http://schemas.android.com/apk/res/android">
	   <data>
	       <variable name="user" type="com.example.User"/>
	   </data>
	   <LinearLayout
	       android:orientation="vertical"
	       android:layout_width="match_parent"
	       android:layout_height="match_parent">
	       <TextView android:layout_width="wrap_content"
	           android:layout_height="wrap_content"
	           android:text="@{user.firstName}"/>
	       <TextView android:layout_width="wrap_content"
	           android:layout_height="wrap_content"
	           android:text="@{user.lastName}"/>
	   </LinearLayout>
	</layout>

这里的`<variable name="user" type="com.example.User"/>`代表这个布局中会用到的数据，使用数据方式：`android:text="@{user.lastName}"`

### 代码中绑定数据

	@Override
	protected void onCreate(Bundle savedInstanceState) {
	   super.onCreate(savedInstanceState);
	   ActivityMainBinding binding = DataBindingUtil.setContentView(this, R.layout.activity_main);
	   User user = new User("Test", "User");
	   binding.setUser(user);
	}

在布局中设置了绑定后，会自动生成一个类：`ActivityMainBinding`(类名和布局名有关)，这个类里面封装了数据和布局文件中控件的对应关系，会自动将实体类中的属性数据加载到控件中。

到此执行工程就可以看到数据自动加载到了控件上。

**如果：**`DataBindingUtil.setContentView(this, R.layout.activity_main);`**这段代码报错，不用管，不影响编译，后面的部分可以解决；**，或者让`User类`继承
`extends BaseObservable`这个类

如果要把数据绑定到`ListView`或者`RecyclerView`中会需要下列代码：

	ListItemBinding binding = ListItemBinding.inflate(layoutInflater, viewGroup, false);
	//or
	ListItemBinding binding = DataBindingUtil.inflate(layoutInflater, R.layout.list_item, viewGroup, false);

---

### 绑定事件

	android:onClick
	android:onLongClick

在上面的例子上添加代码

数据对象代码`User.class`：

	public class User {
		。。。
    	public void onClickFirstName(View view) {
    	    Log.d(TAG, "onClickFirstName: 点击了FirstView");
    	}
	}

布局文件中`activity_main.xml`第一个`TextView`添加下列代码：

	android:onClick="@{user.onClickFirstName}"

## 布局中其他设置

### Import

#### 普通用法

	<data>
	    <import type="android.view.View"/>
	</data>

#### 别名
如果同时导入多个包，有同名的话可以给一个包取别名：

	<import type="android.view.View"/>
	<import type="com.example.real.estate.View"
	        alias="Vista"/>

>注：android studio 对DataBinding的自动补全功能支持的还不完善，需要手打这些代码。

#### 导入自定义的工具类

	<data>
	    <import type="com.example.MyStringUtils"/>
	    <variable name="user" type="com.example.User"/>
	</data>
	…
	<TextView
	   android:text="@{MyStringUtils.capitalize(user.lastName)}"
	   android:layout_width="wrap_content"
	   android:layout_height="wrap_content"/>

自动导入的包：`java.lang.*`

#### 一个例子，更具User是否是Vip 控制控件的显示

添加代码`User.class`:

	private boolean isVip;

    public boolean getIsVip() {
        return isVip;
    }

    public void setVip(boolean vip) {
        isVip = vip;
    }

布局文件添加一个控件`activity_main.xml`：

	<TextView
	            android:layout_width="wrap_content"
	            android:layout_height="wrap_content"
	            android:text="VIP"
	            android:visibility="@{user.getIsVip? View.VISIBLE : View.GONE}"
	            android:textColor="#f00"
	            android:textSize="20sp"/>

测试过：`user.getIsVip`和`user.isVip`都可行；

![1]() ![2]()

这样就可以在代码中控制一个控件是否显示了：`user.setVip(false/true);`

### Variables

	<data>
	    <import type="android.graphics.drawable.Drawable"/>
	    <variable name="user"  type="com.example.User"/>
	    <variable name="image" type="Drawable"/>
	    <variable name="note"  type="String"/>
	</data>

### 自定义绑定后自动生成的类
>貌似没什么用

	<data class="ContactItem">
	    ...
	</data>

	<data class=".ContactItem">
	    ...
	</data>

	<data class="com.example.ContactItem">
	    ...
	</data>

### Includes

	<?xml version="1.0" encoding="utf-8"?>
	<layout xmlns:android="http://schemas.android.com/apk/res/android"
	        xmlns:bind="http://schemas.android.com/apk/res-auto">
	   <data>
	       <variable name="user" type="com.example.User"/>
	   </data>
	   <LinearLayout
	       android:orientation="vertical"
	       android:layout_width="match_parent"
	       android:layout_height="match_parent">
	       <include layout="@layout/name"
	           bind:user="@{user}"/>
	       <include layout="@layout/contact"
	           bind:user="@{user}"/>
	   </LinearLayout>
	</layout>

一个实体对象同时将数据加载到不同的布局文件中

另外：不支持作为`merge`标签的直接子元素使用：

	<?xml version="1.0" encoding="utf-8"?>
	<layout xmlns:android="http://schemas.android.com/apk/res/android"
	        xmlns:bind="http://schemas.android.com/apk/res-auto">
	   <data>
	       <variable name="user" type="com.example.User"/>
	   </data>
	   <merge>
	       <include layout="@layout/name"
	           bind:user="@{user}"/>
	       <include layout="@layout/contact"
	           bind:user="@{user}"/>
	   </merge>
	</layout>

### 各种表达式的标识符都能使用

	android:text="@{String.valueOf(index + 1)}"
	android:visibility="@{age &lt; 13 ? View.GONE : View.VISIBLE}"
	android:transitionName='@{"image_" + id}'

	// 	如果user的displayName为null就使用lastName
	android:text="@{user.displayName ?? user.lastName}"

	// 同上
	android:text="@{user.displayName != null ? user.displayName : user.lastName}"

### 集合数据

	<data>
	    <import type="android.util.SparseArray"/>
	    <import type="java.util.Map"/>
	    <import type="java.util.List"/>
	    <variable name="list" type="List&lt;String>"/>
	    <variable name="sparse" type="SparseArray&lt;String>"/>
	    <variable name="map" type="Map&lt;String, String>"/>
	    <variable name="index" type="int"/>
	    <variable name="key" type="String"/>
	</data>
	…
	android:text="@{list[index]}"
	…
	android:text="@{sparse[index]}"
	…
	android:text="@{map[key]}"

### 字符串值

	android:text='@{map["firstName"]}'
	android:text="@{map[`firstName`}"
	android:text="@{map[&quot;firstName&quot;]}"

### 资源使用

	android:padding="@{large? @dimen/largePadding : @dimen/smallPadding}"
	android:text="@{@string/nameFormat(firstName, lastName)}"
	android:text="@{@plurals/banana(bananaCount)}"
	android:text="@{@plurals/orange(orangeCount, orangeCount)}"

## 数据对象

有三种数据类型： `Observable objects`, `observable fields`, and `observable collections`.

### Observable objects
实体类实现`Observable`

    @Bindable
    public String getFirstName() {
        return firstName;
    }

    public void setFirstName(String firstName) {
        this.firstName = firstName;
        notifyPropertyChanged(com.example.doublefang.databindingdemo2.BR.firstName);
    }

    public String getLastName() {
        return lastName;
    }

    @Bindable
    public void setLastName(String lastName) {
        this.lastName = lastName;
        notifyPropertyChanged(com.example.doublefang.databindingdemo2.BR.lastName);
    }

需要设置`@Bindable`注解，设置在getter和setter都可以，这样设置后对象中数据改变后，与其绑定的视图才会马上更新；

### ObservableFields

	private static class User {
	   public final ObservableField<String> firstName =
	       new ObservableField<>();
	   public final ObservableField<String> lastName =
	       new ObservableField<>();
	   public final ObservableInt age = new ObservableInt();
	}

对变量进行操作：

	user.firstName.set("Google");
	int age = user.age.get();

### Observable Collections

	ObservableArrayMap<String, Object> user = new ObservableArrayMap<>();
	user.put("firstName", "Google");
	user.put("lastName", "Inc.");
	user.put("age", 17);

布局文件：

	<data>
	    <import type="android.databinding.ObservableMap"/>
	    <variable name="user" type="ObservableMap&lt;String, Object>"/>
	</data>
	…
	<TextView
	   android:text='@{user["lastName"]}'
	   android:layout_width="wrap_content"
	   android:layout_height="wrap_content"/>
	<TextView
	   android:text='@{String.valueOf(1 + (Integer)user["age"])}'
	   android:layout_width="wrap_content"
	   android:layout_height="wrap_content"/>

.......






