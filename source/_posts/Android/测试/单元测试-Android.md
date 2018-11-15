---
title: 单元测试-Android
date: 2017-03-15 13:18:12
tags: [单元测试, android]
category: android

---

# Android 单元测试基本介绍

## 测试分类

Android 单元测试分为两种：

1. 运行在 JVM 上的测试(local unit tests)
2. 需要 Android 环境的测试（instrumentation tests）
3. Mockito

## 相关框架

测试使用的框架：

1. AndroidJUnitRunner：local unit

    获取 Android 相关 Api 通过 InstrumentationRegistry 获取 context

2. espresso：UI 测试

## 测试目录结构

- `app/src/test/java`: 基于 jvm 的测试代码
- `app/src/androidTest/java`: 需要运行在 Android 环境的测试代码

## gradle 配置

```

defaultConfit{
    testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
}

dependence:

    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'com.android.support.test:runner:1.0.2'
    androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.2'
```

# 基于 jvm 的单元测试

Android 的测试基于 JUnit

运行 jvm 中就能测试，不需要调用 Android API 的测试

通常使用场景：

1. 测试工具类

## Create a Local Unit Test Class 创建测试用例
例子

随意创建一个待测试的类：
```java
public class Calculator {

    public double sum(double a, double b){
        return 0;
    }

    public double subtract(double a, double b){
        return 0;
    }

    public double divide(double a, double b){
        return 0;
    }

    public double multiply(double a, double b){
        return 0;
    }
}
```
在类名上 点击右键-》选择 go to --》 选 test --》勾选 （所有方法，勾选 setUp/@Before）
选择生成 test文件的位置：test

生成：

```java
    private Calculator mCalculator;

    @Before
    public void setUp() throws Exception {
        mCalculator = new Calculator();
    }

    @Test
    public void sum() throws Exception {
        assertEquals(6d, mCalculator.sum(1d, 5d), 0);
    }

    @Test
    public void subtract() throws Exception {
        assertEquals(1d, mCalculator.subtract(5d, 4d), 0);
    }a

    @Test
    public void divide() throws Exception {
        assertEquals(4d, mCalculator.divide(20d, 5d), 0);
    }

    @Test
    public void multiply() throws Exception {
        assertEquals(10d, mCalculator.multiply(2d, 5d), 0);
    }

}
```

<!--more-->

在类名上点击右键--》Run CalculatorTest, 测试用例执行。

# Instrumented tests 运行在 Android 设备上的测试

表示与 UI 或者 Android 有关的测试

需要运行在 Android 环境下的测试，测试的时候会被集成到 APK 安装到手机中

## Set Up Testing Enviroment

测试文件的位置：`module-name/src/test/java/`,这个文件Android Studio 已经帮我们建好了。

在 `build.gradle` 中添加测试框架依赖：(其中 junit 一般是已经存在的，mockito 框架为可选项)

```
defaultConfig {
       ..... more stuff
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
    }


dependencies {
    // Required -- JUnit 4 framework
    testCompile 'junit:junit:4.12'
    // Optional -- Mockito framework
    testCompile 'org.mockito:mockito-core:1.10.19'
}
```

## 测试类需要注解 @RunWith(AndroidJUnit4.class)

AndroidJUnit4 继承 JUnit4

## Building Local Unit Tests




### Mock Android dependencies



#### Error: "Method ... not mocked"

```
android {
  ...
  testOptions {
    unitTests.returnDefaultValues = true
  }
}
```


## Building Instrumented Unit Test



### Set Up Your Testing Environment

测试文件位置：`module-name/src/androidTest/java/`

```

configurations.all {
    resolutionStrategy {
        force 'com.android.support:support-annotations:23.1.1'
    }
}


dependencies {
    androidTestCompile 'com.android.support:support-annotations:24.0.0'
    androidTestCompile 'com.android.support.test:runner:0.5'
    androidTestCompile 'com.android.support.test:rules:0.5'
    // Optional -- Hamcrest library
    androidTestCompile 'org.hamcrest:hamcrest-library:1.3'
    // Optional -- UI testing with Espresso
    androidTestCompile 'com.android.support.test.espresso:espresso-core:2.2.2'
    // Optional -- UI testing with UI Automator
    androidTestCompile 'com.android.support.test.uiautomator:uiautomator-v18:2.1.2'
}
```


### 测试例子


## Testing UI for a Single App

### Espresso 基本介绍

1. 获取 UI 控件方法：`onView`,`onData`
2. 定义控件的行为：通过`ViewInteraction.perform()`,`DataInteraction.perform()`方法将行为作为参数传递
3. 使用 `ViewAssertions` 的方法来检查界面的变化

基本的流程：
```java
onView(withId(R.id.my_view))            // withId(R.id.my_view) is a ViewMatcher
        .perform(click())               // click() is a ViewAction
        .check(matches(isDisplayed())); // matches(isDisplayed()) is a ViewAssertion

```


perform:

```java
// ViewInteraction.class
public ViewInteraction perform(final ViewAction... viewActions)
```

```java
// ViewActions.class
public static ViewAction click()

public static ViewAction typeText(String stringToBeTyped)

public static ViewAction closeSoftKeyboard()
```

#### Accessing UI Components

##### OnView 获取 ViewInteraction 对象

```java
// Espresso.class
public static ViewInteraction onView(final Matcher<View> viewMatcher)
```

##### Matcher 创建

###### 1

通过 `ViewMatchers.class` 类的静态方法获取 Matcher 对象

```java
// 
public static Matcher<View> withId(final int id)
```

###### 2 

通过 `Matchers` 类,可以设置多个约束

```java
public static <T> org.hamcrest.Matcher<T> allOf(org.hamcrest.Matcher<? super T> first, org.hamcrest.Matcher<? super T> second)
```

##### Locate a View in an AdapterView

`onData()` 获取 `DataInteration`

ex:
```java
onData(allOf(is(instanceOf(Map.class)),
        hasEntry(equalTo(LongListActivity.ROW_TEXT), is("test input")));
```



### 测试例子

创建需要被测试的UI

```java
    public void sayHello(View v){
        TextView textView = (TextView) findViewById(R.id.textView);
        EditText editText = (EditText) findViewById(R.id.editText);
        textView.setText("Hello, " + editText.getText().toString() + "!");
    }
```

```xml
    <TextView
        android:id="@+id/textView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/hello_world"/>

    <EditText
        android:id="@+id/editText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_below="@+id/textView"
        android:hint="Enter your name here"/>

    <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_below="@+id/editText"
        android:onClick="sayHello"
        android:text="Say hello!"/>
```

创建 UI 测试类

在 `androidTest` 目录中

```java
package com.nerc.unittestlearn;

import android.support.test.rule.ActivityTestRule;
import android.support.test.runner.AndroidJUnit4;
import android.test.suitebuilder.annotation.LargeTest;

import org.junit.After;
import org.junit.Before;
import org.junit.Rule;
import org.junit.Test;
import org.junit.runner.RunWith;

import static android.support.test.espresso.Espresso.onView;
import static android.support.test.espresso.action.ViewActions.click;
import static android.support.test.espresso.action.ViewActions.closeSoftKeyboard;
import static android.support.test.espresso.action.ViewActions.typeText;
import static android.support.test.espresso.assertion.ViewAssertions.matches;
import static android.support.test.espresso.matcher.ViewMatchers.withId;
import static android.support.test.espresso.matcher.ViewMatchers.withText;

/**
 * Created by double on 2017/3/4.
 */
@RunWith(AndroidJUnit4.class)
@LargeTest
public class MainActivityTest {

    private static final String STRING_TO_BE_TYPED = "Peter";

    @Rule
    public ActivityTestRule<MainActivity> mActivityRule = new ActivityTestRule<>(
            MainActivity.class);


    @Before
    public void setUp() throws Exception {

    }

    @After
    public void tearDown() throws Exception {

    }

    @Test
    public void sayHello() throws Exception {
        onView(withId(R.id.editText)).perform(typeText(STRING_TO_BE_TYPED), closeSoftKeyboard()); //line 1

        onView(withText("Say hello!")).perform(click()); //line 2

        String expectedText = "Hello, " + STRING_TO_BE_TYPED + "!";
        onView(withId(R.id.textView)).check(matches(withText(expectedText))); //line 3
    }

}
```

在类名上点击右键运行测试

# 异步单元测试

```
   private String temp = null;

    public void testAsyncTask() throws Throwable {
        final CountDownLatch signal = new CountDownLatch(1);

        // 在UI线程中执行异步操作
        runTestOnUiThread(new Runnable() {

            @Override
            public void run() {
                // 执行异步任务，这个只是例子，没有实际的可以运行的代码，大家知道就好
                new MyAsyncTask() {
                    @Override
                    protected void onPostExecute(String result) {
                        super.onPostExecute(result);
                        temp = result;
                        signal.countDown();

                    }
                }.execute();
            }
        });
        signal.await();
        assertEquals("kale", temp);
    }
```



参考：

- [https://developer.android.com/training/testing/start/index.html](https://developer.android.com/training/testing/start/index.html)
- [http://www.vogella.com/tutorials/AndroidTesting/article.html](http://www.vogella.com/tutorials/AndroidTesting/article.html)
- [https://blog.csdn.net/CZT_saisam/article/details/46637007](https://blog.csdn.net/CZT_saisam/article/details/46637007)

