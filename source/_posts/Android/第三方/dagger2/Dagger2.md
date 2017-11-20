---
title: Dagger
date: 2016-12-04 12:18:12
tags: Dagger
category: android

---


# 集成

[Github 地址](https://github.com/google/dagger)

```
// Add Dagger dependencies
dependencies {
  compile 'com.google.dagger:dagger:2.x'
  annotationProcessor 'com.google.dagger:dagger-compiler:2.x'
}
```

如果在使用 gradle 2.2 以下的 gradle，还需要集成 [https://bitbucket.org/hvisser/android-apt.](https://bitbucket.org/hvisser/android-apt)

# 概念

- @Module: 把这个类表识为 dagger 的 module，是提供依赖注入时所需对象实例的类
- @Inject: 用来标注目标类的依赖和依赖的构造函数
- @Component: Components 从根本上来说就是一个注入器，也可以说是 @Inject 和 @Module 的桥梁，它的主要作用就是连接这两个部分。 Components 可以提供所有定义了的类型的实例，比如：我们必须用 @Component 注解一个接口然后列出所有的@Modules组成该组件，如 果缺失了任何一块都会在编译的时候报错。所有的组件都可以通过它的 modules 知道依赖的范围。
- @Provides：标识函数作为注入提供者，函数名并不重要，它只依赖于所提供的类类型
- @Singleton：如果标识为Singleton，那这个函数会一直返回相同的对象实例，这比常规的单例好很多。否则，每次注入类型都会得到一个新的实例。在这个例子中，由于我们没有创建新实例，而是返回已经存在的实例，因此即使不把函数标识为 Singleton ，每次调用还是会返回相同的实例的，但这样能够更好地说明提供者到底做了什么。 Application 实例是唯一的。
- @Scope: Scopes可是非常的有用，Dagger2 可以通过自定义注解限定注解作用域。后面会演示一个例子，这是一个非常强大的特点，因为就如前面说的一样，没 必要让每个对象都去了解如何管理他们的实例。在scope的例子中，我们用自定义的 @PerActivity注解一个类，所以这个对象存活时间就和 activity的一样。简单来说就是我们可以定义所有范围的粒度(@PerFragment, @PerUser, 等等)。
- Qualifier: 当类的类型不足以鉴别一个依赖的时候，我们就可以使用这个注解标示。例如：在Android中，我们会需要不同类型的context，所以我们就可以定义 qualifier注解“@ForApplication”和“@ForActivity”，这样当注入一个context的时候，我们就可以告诉 Dagger我们想要哪种类型的context。


# dagger2 最简单的使用例子

## 一个需要被注入到 activity 的对象
```java
public class Poetry {
    public String mPoetry;

    @Inject
    public Poetry() {
        mPoetry = "live is short";
    }
}
```

## activity component
```java
@Component
public interface BaseUseComponent {

    /**
     * 需要用到这个连接器的对象，就是这个对象里面有需要注入的属性（被标记为@Inject的属性）
     * 这里inject表示注入的意思，这个方法名可以随意更改
     */
    void inject(BaseUseActivity baseUseActivity);
}
```

创建完 Component 后需要编译一遍工程，dagger2 会自动生成一个 DaggerBaseUseComponent 类。

Component 中每个方法的参数对象，表示这些对象里面需要注入一些其他对象。

## activity
```java
public class BaseUseActivity extends AppCompatActivity {

    //添加@Inject注解，表示这个mPoetry是需要注入的
    @Inject
    Poetry mPoetry;

    private TextView mTV;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_base_use);

        // 使用 Dagger2 生成的类 生成组件进行构造，并注入
        BaseUseComponent baseUseComponent = DaggerBaseUseComponent.builder()
                .build();
        baseUseComponent.inject(this);

        initView();
    }

    private void initView() {
        mTV = (TextView) findViewById(R.id.tv_base_use);
        mTV.setText(mPoetry.mPoetry);
    }
}
```

这种不通过 `@Module` 的注解方式有很多的局限性：

1. `@Inject` 只能注解一个构造方法，对于有多个构造方法的对象不适用
2. 对于第三方库中的对象，没法添加 `@Inject`

## 使用 Module 注入一个 Gson 对象
```java
@Module
public class BaseUseModule {

    @Provides
    Gson provideGson(){
        return new Gson();
    }
}
```

Module 类，提供被注解的对象

将 Module 和 Component 关联

```java
@Component(modules = BaseUseModule.class)
public interface BaseUseComponent {

    /**
     * 需要用到这个连接器的对象，就是这个对象里面有需要注入的属性（被标记为@Inject的属性）
     * 这里inject表示注入的意思，这个方法名可以随意更改
     */
    void inject(BaseUseActivity baseUseActivity);
}
```

## Compoent 依赖多个 Module 对象

如果 Poetry 类有多个构造方法的话，就需要创建额外的 Module：

```java
@Module
public class PoetryModule {

    @Provides
    Poetry providePoetry(){
        return new Poetry();
    }

    @Provides
    Poetry providePoetry(String poetry){
        return new Poetry(poetry);
    }
}
```


## @Scope 
标记当前生成对象的使用范围，标识一个类型的注射器只实例化一次，在同一个作用域内，只会生成一个实例，然后在此作用域内共用一个实例。这样看起来很像单例模式，我们可以查看@Singleton其实就是@Scope的一个默认实现而已。当然，你得是同一个Component对象来生成，这点我们应该可以理解的吧。
我们可以通过自定义Scope来组织Component的作用域，使得每个Component的作用域清晰明了，各施其职。

## 组织 Component


如果其他 Component 需要 ApplicationComponent 中的全局属性的时候，就需要建立两者之间的 依赖方式。

1. 要有一个全局的Component(可以叫ApplicationComponent),负责管理整个app的全局类实例
2. 每个页面对应一个Component，比如一个Activity页面定义一个Component，一个Fragment定义一个Component。当然这不是必须的，有些页面之间的依赖的类是一样的，可以公用一个Component。

### 依赖方式

一个Component可以依赖一个或多个Component，并拿到被依赖Component暴露出来的实例，Component的dependencies属性就是确定依赖关系的实现。


```java
@Component(dependencies = ApplicationComponent.class, modules = {BaseUseModule.class, PoetryModule.class})
public interface BaseUseComponent {
    //...
```

### 包含方式

SubComponent

### 继承方式

## Qualifier
（限定符）就是解决依赖注入迷失问题







# Application dagger2 化

## 创建 Application 的鉴别注解  

```java
@Qualifier @Retention(RetentionPolicy.RUNTIME)
public @interface ForApplication {

}
```

## 创建 Module

创建一个 BaseApplication 的依赖注入：

```java
@Module
public class AppModule {
    private final App mApplication;

    public AppModule(App application) {
        mApplication = application;
    }

    @Provides
    @Singleton
    @ForApplication
    Context providerApplication(){
        return mApplication;
    }
}
```

## 创建 Component


```java
@Singleton
@Component(modules = ApplicationModule.class)
public interface ApplicationComponent {
    void inject(App app);

    void inject(MainActivity mainActivity);
}
```

这个时候编译一遍工程  mac 下快捷键：`command+f9`

dagger2 会自动生成一个类：

```java
public final class DaggerApplicationComponent implements ApplicationComponent {
  private Provider<LocationManager> provideLocationManagerProvider;

  private MembersInjector<App> appMembersInjector;

  private MembersInjector<MainActivity> mainActivityMembersInjector;

  private DaggerApplicationComponent(Builder builder) {
    assert builder != null;
    initialize(builder);
  }

  public static Builder builder() {
    return new Builder();
  }

  @SuppressWarnings("unchecked")
  private void initialize(final Builder builder) {

    this.provideLocationManagerProvider =
        DoubleCheck.provider(
            ApplicationModule_ProvideLocationManagerFactory.create(builder.applicationModule));

    this.appMembersInjector = App_MembersInjector.create(provideLocationManagerProvider);

    this.mainActivityMembersInjector =
        MainActivity_MembersInjector.create(provideLocationManagerProvider);
  }

  @Override
  public void inject(App app) {
    appMembersInjector.injectMembers(app);
  }

  @Override
  public void inject(MainActivity mainActivity) {
    mainActivityMembersInjector.injectMembers(mainActivity);
  }

  public static final class Builder {
    private ApplicationModule applicationModule;

    private Builder() {}

    public ApplicationComponent build() {
      if (applicationModule == null) {
        throw new IllegalStateException(
            ApplicationModule.class.getCanonicalName() + " must be set");
      }
      return new DaggerApplicationComponent(this);
    }

    public Builder applicationModule(ApplicationModule applicationModule) {
      this.applicationModule = Preconditions.checkNotNull(applicationModule);
      return this;
    }
  }
}

```

## 将 Application 注册到 Component

```java
    @Override
    public void onCreate() {
        super.onCreate();

        mApplicationComponent = DaggerApplicationComponent.builder()
                .applicationModule(new ApplicationModule(this))
                .build();

        mApplicationComponent.inject(this);
    }
```






# google todo mvp Dagger

DemoPresenter:

```java
    final class StatisticsPresenter implements StatisticsContract.Presenter {

        private final TasksRepository mTasksRepository;

        private final StatisticsContract.View mStatisticsView;

        @Inject
        StatisticsPresenter(TasksRepository tasksRepository,
                                   StatisticsContract.View statisticsView) {
            mTasksRepository = tasksRepository;
            mStatisticsView = statisticsView;
        }

        @Inject
        void setupListeners() {
            mStatisticsView.setPresenter(this); // 将 presenter 对象传给 view， 通过 module 获取 view 对象，通过 view 对象的  
                                                //setPresenter 方法将 presenter 对象传给 view
        }

        @Override
        public void start() {
            loadStatistics();
        }

        ......
    }
```

DemoPresenterModule:

    @Module
    public class StatisticsPresenterModule {

        private final StatisticsContract.View mView;

        public StatisticsPresenterModule(StatisticsContract.View view) {
            mView = view;
        }

        @Provides
        StatisticsContract.View provideStatisticsContractView() {
            return mView;
        }
    }

DemoComponent:

    @FragmentScoped
    @Component(dependencies = TasksRepositoryComponent.class, modules = StatisticsPresenterModule.class)
    public interface StatisticsComponent {
        void inject(StatisticsActivity statisticsActivity);
    }


DemoActivity:

    // 属性：
    @Inject StatisticsPresenter mStatiticsPresenter;

    // 注入代码
    DaggerStatisticsComponent.builder()
        .statisticsPresenterModule(new StatisticsPresenterModule(statisticsFragment)) // 给 presenter 注入 view
        .tasksRepositoryComponent(((ToDoApplication) getApplication()) // model
        .getTasksRepositoryComponent())
        .build()
        .inject(this);


# 使用scope时规则

1. component的dependencies与component自身的scope不能相同，即组件之间的scope不同
2. Singleton的组件不能依赖其他scope的组件，只能其他scope的组件依赖Singleton的组件。
3. 没有scope的不能依赖有scope的组件

    ```
    Error:(21, 1) 错误: com.android.example.devsummit.archdemo.di.component.MyTestComponent (unscoped) cannot depend on scoped components:
    @com.android.example.devsummit.archdemo.di.scope.ActivityScope com.android.example.devsummit.archdemo.di.component.MyTestComponentX
    ```

4. 一个component不能同时有多个scope(Subcomponent除外)

    ```
    Error:Execution failed for task ‘:app:compileDebugJavaWithJavac’.
    java.lang.IllegalArgumentException: com.android.example.devsummit.archdemo.di.component.MyTestComponent was annotated with more than one @Scope annotation
    ```
5. component关联的Model中的任何一个被构造的对象有scope，则该整个component要加上这个scope。在暴露或者注入时（不暴露且不注入时，既不使用它构造对象时，不报错），会有如下错误

    ```
    Error:(21, 1) 错误: com.android.example.devsummit.archdemo.di.component.MyTestComponent (unscoped) may not reference scoped bindings:
    @Provides @com.android.example.devsummit.archdemo.di.scope.ActivityScope com.android.example.devsummit.archdemo.vo.User com.android.example.devsummit.archdemo.di.module.TestModule.user()
    ```

# MVP Dagger2



参考：

- [google_mvp_dagger](https://github.com/googlesamples/android-architecture)
- [https://asce1885.gitbooks.io/android-rd-senior-advanced/content/androidzhong_de_yi_lai_zhu_ru_ff1a_dagger_han_shu_ku_de_shi_yong_ff08_er_ff09.html](https://asce1885.gitbooks.io/android-rd-senior-advanced/content/androidzhong_de_yi_lai_zhu_ru_ff1a_dagger_han_shu_ku_de_shi_yong_ff08_er_ff09.html)
- [http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0519/2892.html](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0519/2892.html)
- [http://www.jianshu.com/p/94d47da32656](http://www.jianshu.com/p/94d47da32656)
- [http://www.jianshu.com/p/65737ac39c44](http://www.jianshu.com/p/65737ac39c44)
- [http://limuzhi.com/2016/03/06/Android%E6%B3%A8%E5%85%A5%E6%A1%86%E6%9E%B6Dagger2%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/](http://limuzhi.com/2016/03/06/Android%E6%B3%A8%E5%85%A5%E6%A1%86%E6%9E%B6Dagger2%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/)
