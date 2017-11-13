---
title: Android Architecture Components 官方文档学习记录
date: 2017-11-10 12:18:12
tags: [Android 架构]
category: android

---

# Guide to App Architecture 

![官方文档地址](https://developer.android.com/topic/libraries/architecture/guide.html#common_architectural_principles)

这个 guide 写给那些想要构建好的架构的 Android 开发者

## Common problems faced by app developers

you should not store any app data or state in your app components

## Commom architectural principles


1. 注意在 App 中分离代码（业务逻辑代码和UI代码分离）

所有和 UI 无关的代码不应该出现在 Activity 和 Fragment 中。

保持 Activity 和 Fragment 整洁干净，以避免生命周期倒致相关的问题。


2. 让 model 来驱动 UI（drive your UI from a model）

Model 是组件，用于存储应用使用到的数据，它独立于 View 和 Android 应用的相关组件

保证 UI 代码中不处理业务逻辑


## Recommended app architecture

下面是使用例子讲解 如何使用 Architecture Components 来创建 Android 架构。

### 创建 UI 界面 （Building the user interface）


ViewModel: 为提供 UI 界面的数据的组件，它和 View 相互不知道（隔离）

例子涉及的三个文件:

- `UserProfileViewModel.class`
- `user_profile.xml`
- `UserProfileFragment.java`


```java
public class UserprofileViewModel extends ViewModel{
    private String userId;

    private LiveData<User> user;
    //private User user;

    public void init(String userId){
        this.userId = userId;
    }

    public User getUser(){
        return user;
    }

    
}
```

```java
public class UserProfileFragment extends Fragment{
    private static final String UID_KEY = "uid";
    private UserProfileViewModel viewModel;

    @Override
    public void onActivityCreated(@Nullable Bundle savedInstanceState){
        super.onActivityCreated(savedInstanceState);
        String userId = getArguments().getString(UID_KEY);
        viewModel = ViewModelProviders.of(this).get(UserProfileViewModel.class);
        viewModel.init(userId);
        viewModel.getUser().observe(this, user -> {
            // update UI

        });        
    }

    @Override
    public View onCreateView(LayoutInflater inflater,
                @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        return inflater.inflate(R.layout.user_profile, container, false);
    }

}
```

下面引入 LiveData, 让 ViewModel 中的数据可以加载到 Fragment 中。

`LiveData`: 实现一个观察者模式。让 ViewModel 中数据加载完成后触发 Fragment 更新UI。


LiveData 可以使用 RxJava 代替，但是 LiveData 有个优点就是不需要 取消订阅，LiveData 会在 Fragment 销毁的时候 解除关联。

另外：ViewModel 的生命周期是独立于 Fragment 的 [The lifecycle of a ViewModel](https://developer.android.com/topic/libraries/architecture/viewmodel.html#the_lifecycle_of_a_viewmodel)

### Fetching data

ViewModel 如何获取数据

使用 Repository 组件代理数据的获取

Webservice 是通过 Retrofit 实现的


```java
public class UserRepository {
    private Webservice webservice;
    // ...
    public LiveData<User> getUser(int userId) {
        // This is not an optimal implementation, we'll fix it below
        final MutableLiveData<User> data = new MutableLiveData<>();
        webservice.getUser(userId).enqueue(new Callback<User>() {
            @Override
            public void onResponse(Call<User> call, Response<User> response) {
                // error case is left out for brevity
                data.setValue(response.body());
            }
        });
        return data;
    }
}
```

这样做将 数据获取 和 数据持有类 隔离。数据持有类不需要知道数据是如何获取的。


#### managing dependencies between components:

UserRepository 依赖 Webservice, 如果通过构造方法的方式创建 Webservice 对象的化就需要知道它类中的构造方法结构。并且 UserRepository 不是唯一一个需要 Webservice 的类。如果没有需要 Webservice 的类都通过构造方法创建这个对象，就会导致代码重复。

两种方法解决这个问题：

1. 依赖注入（Dependency Injection）: 使用 Dagger2 实现，推荐使用
2. Service LocatorL： 比 DI 简单


### Connection ViewModel and the repository

```java
public class UserProfileViewModel extends ViewModel {
    private LiveData<User> user;
    private UserRepository userRepo;

    @Inject // UserRepository parameter is provided by Dagger 2
    public UserProfileViewModel(UserRepository userRepo) {
        this.userRepo = userRepo;
    }

    public void init(String userId) {
        if (this.user != null) {
            // ViewModel is created per Fragment so
            // we know the userId won't change
            return;
        }
        user = userRepo.getUser(userId);
    }

    public LiveData<User> getUser() {
        return this.user;
    }
}
```


### Caching data

```java
@Singleton  // informs Dagger that this class should be constructed once
public class UserRepository {
    private Webservice webservice;
    // simple in memory cache, details omitted for brevity
    private UserCache userCache;
    public LiveData<User> getUser(String userId) {
        LiveData<User> cached = userCache.get(userId);
        if (cached != null) {
            return cached;
        }

        final MutableLiveData<User> data = new MutableLiveData<>();
        userCache.put(userId, data);
        // this is still suboptimal but better than before.
        // a complete implementation must also handle the error cases.
        webservice.getUser(userId).enqueue(new Callback<User>() {
            @Override
            public void onResponse(Call<User> call, Response<User> response) {
                data.setValue(response.body());
            }
        });
        return data;
    }
}
```


UserCache 是一个自定义的类，统一保存缓存。


### 数据可持续化 Persisting data

Room：一个数据库框架

基本使用代码：

```java
@Entity
class User {
  @PrimaryKey
  private int id;
  private String name;
  private String lastName;
  // getters and setters for fields
}

@Dao  // data access object (DAO).
public interface UserDao {
    @Insert(onConflict = REPLACE)
    void save(User user);
    @Query("SELECT * FROM user WHERE id = :userId")
    LiveData<User> load(String userId);
}


@Database(entities = {User.class}, version = 1)
public abstract class MyDatabase extends RoomDatabase {
    public abstract UserDao userDao();
}

@Database(entities = {User.class}, version = 1)
public abstract class MyDatabase extends RoomDatabase {
    public abstract UserDao userDao();
}
```

由于上面 `load` 方法返回的是 `LiveData` 所以数据库中如果数据变化，会马上反应在 UI 上。


在 UserRepository 中使用 Room：

```java
@Singleton
public class UserRepository {
    private final Webservice webservice;
    private final UserDao userDao;
    private final Executor executor;

    @Inject
    public UserRepository(Webservice webservice, UserDao userDao, Executor executor) {
        this.webservice = webservice;
        this.userDao = userDao;
        this.executor = executor;
    }

    public LiveData<User> getUser(String userId) {
        refreshUser(userId);
        // return a LiveData directly from the database.
        return userDao.load(userId);
    }

    private void refreshUser(final String userId) {
        executor.execute(() -> {
            // running in a background thread
            // check if user was fetched recently
            boolean userExists = userDao.hasUser(FRESH_TIMEOUT);
            if (!userExists) {
                // refresh the data
                Response response = webservice.getUser(userId).execute();
                // TODO check for error etc.
                // Update the database.The LiveData will automatically refresh so
                // we don't need to do anything else here besides updating the database
                userDao.save(response.body());
            }
        });
    }
}
```

通过上面的改变，我们可以注意到数据源变了，但是 ViewModel 和 UI界面 （UserProfileViewModel or UserProfileFragment）的代码一点没动。

而且这样做通过一个 fake UserRepository 可以方便测试 UserProfileViewModle

有时候我们需要获取最新的数据，有时候可以使用旧的数据，下面由两种方法处理这种情况：

1. 改变 getUser 方法，让它每次获取都直接从网络中获取最新数据
2. 提供一个新的方法直接获取网络数据，在需要的时候调用，比如 用户执行下拉刷新的时候。

#### Single source of truth




### Testing

- User Interface & Interactions: 使用 Espresso 测试 UI，
- ViewModel: 使用 JUnit 测试
- UserRepository: 使用 JUnit 测试
- UserDao: using instrumentation tests
- Webservice: MockWebServer
- Testing Artifacts


### The final architecture

![Android Architecture 架构图](Android官方架构图)

## Guiding principles


## Addendum: exposing network status


# 添加官方架构组件到项目中 Adding Components to your Project 

添加源：`build.gradle`

```
allprojects {
    repositories {
        jcenter()
        maven { url 'https://maven.google.com' }
    }
}
```

详细：[https://developer.android.com/topic/libraries/architecture/adding-components.html](https://developer.android.com/topic/libraries/architecture/adding-components.html)

1. Lifecycles
2. LiveData, ViewModel
3. Room
4. Paging

# 使用 Lifecycles 组件捕获生命周期 Handling Lifecycles with Lifecycle-Aware Components

下面是一个简单的例子，将 Activity 生命周期引导到另一个类中：

```java
class MyLocationListener {
    public MyLocationListener(Context context, Callback callback) {
        // ...
    }

    void start() {
        // connect to system location service
    }

    void stop() {
        // disconnect from system location service
    }
}

class MyActivity extends AppCompatActivity {
    private MyLocationListener myLocationListener;

    @Override
    public void onCreate(...) {
        myLocationListener = new MyLocationListener(this, (location) -> {
            // update UI
        });
    }

    @Override
    public void onStart() {
        super.onStart();
        myLocationListener.start();
        // manage other components that need to respond
        // to the activity lifecycle
    }

    @Override
    public void onStop() {
        super.onStop();
        myLocationListener.stop();
        // manage other components that need to respond
        // to the activity lifecycle
    }
}
```

上面代码的缺点：

1. 在 UI 复杂的时候会有很多 接口回调 方法去更新 UI
2. 无法保证 MyLocationListener 组件在 Activity stop 的时候已经启动


`android.arch.lifecycle` 包中提供了实现方面功能的类

## Lifecycle

Lifecycle 是一个类，它持有了 activity 和 fragment 的生命周期，并且允许其他类去 监听 生命周期状态。

Lifecycle 使用两个枚举来跟踪生命周期的状态：

1. Event：生命周期改变事件
2. State: 组件当前的状态

![Lifecycle-map]()

生命周期的观察者，需要实现 LifecycleObserver 接口，通过给方法添加注解的方式定义方法所监听生命周期Event.

简单例子：

```java
public class MyObserver implements LifecycleObserver {
    @OnLifecycleEvent(Lifecycle.Event.ON_RESUME)
    public void connectListener() {
        ...
    }

    @OnLifecycleEvent(Lifecycle.Event.ON_PAUSE)
    public void disconnectListener() {
        ...
    }
}

myLifecycleOwner.getLifecycle().addObserver(new MyObserver());
```

myLifecycleOwner 是一个实现了 LifecycleOwner 接口的类，也是被观察生命周期的对象。


## LifecycleOwner


### Implementing a custom LifecycleOwner

Fragments and Activities in Support Library 26.1.0 and later already implement the LifecycleOwner interface.

Support Library 26.1.0 中的 Activity 和 Fragment 已经主动实现了 LifecycleOwner 接口，但是如果你需要自己实现的话可以通过下面的代码：

```java
public class MyActivity extends Activity implements LifecycleOwner {
    private LifecycleRegistry mLifecycleRegistry;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        mLifecycleRegistry = new LifecycleRegistry(this);
        mLifecycleRegistry.markState(Lifecycle.State.CREATED);
    }

    @Override
    public void onStart() {
        super.onStart();
        mLifecycleRegistry.markState(Lifecycle.State.STARTED);
    }

    @NonNull
    @Override
    public Lifecycle getLifecycle() {
        return mLifecycleRegistry;
    }
}
```

## Best pratices for lifecycle-aware components

Keep your UI controllers (activities and fragments) as lean as possible. They should not try to acquire their own data; instead, use a ViewModel to do that, and observe a LiveData object to reflect the changes back to the views.

1. 保证 UI类（activity , fragment）干净，不要在这里写数据获取的代码，应该使用 ViewModel 来获取数据，使用 LiveData 对象更新 UI界面。
2. Try to write data-driven UIs where your UI controller’s responsibility is to update the views as data changes, or notify user actions back to the ViewModel.
3. Put your data logic in your ViewModel class. ViewModel should serve as the connector between your UI controller and the rest of your app. Be careful though, it isn't ViewModel's responsibility to fetch data (for example, from a network). Instead, ViewModel should call the appropriate component to fetch the data, then provide the result back to the UI controller.
4. Use Data Binding to maintain a clean interface between your views and the UI controller. This allows you to make your views more declarative and minimize the update code you need to write in your activities and fragments. If you prefer to do this in the Java programming language, use a library like Butter Knife to avoid boilerplate code and have a better abstraction.
5. If your UI is complex, consider creating a presenter class to handle UI modifications. This might be a laborious task, but it can make your UI components easier to test.
6. Avoid referencing a View or Activity context in your ViewModel. If the ViewModel outlives the activity (in case of configuration changes), your activity leaks and isn't properly disposed by the garbage collector.



## Use cases for lifecycle-aware components

1. Switching between coarse and fine-grained location updates. 
2. Stopping and starting video buffering. 
3. Starting and stopping network connectivity. 
4. Pausing and resuming animated drawables. 


## Handling on stop events



# LiveData

## The advantages of using LiveData


## Works with LiveData objects


### Create LiveData objects

### Observer LiveData objects

### Update LiveData objects

### Use LiveData with Room


## Extend LiveData


## Transform LiveData

### Creating new transformations

## Merge multiple LiveDataa sources


