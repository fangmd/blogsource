---
title: Android Architecture Components 官方文档学习记录
date: 2017-11-10 12:18:12
tags: [Android 架构]
category: android

---

![官方文档地址](https://developer.android.com/topic/libraries/architecture/guide.html#common_architectural_principles)

这个 guide 写给那些想要构建好的架构的 Android 开发者

# Common problems faced by app developers

you should not store any app data or state in your app components

# Commom architectural principles


1. 注意在 App 中分离代码（业务逻辑代码和UI代码分离）

所有和 UI 无关的代码不应该出现在 Activity 和 Fragment 中。

保持 Activity 和 Fragment 整洁干净，以避免生命周期倒致相关的问题。


2. 让 model 来驱动 UI（drive your UI from a model）

Model 是组件，用于存储应用使用到的数据，它独立于 View 和 Android 应用的相关组件

保证 UI 代码中不处理业务逻辑


# Recommended app architecture

下面是使用例子讲解 如何使用 Architecture Components 来创建 Android 架构。

## 创建 UI 界面 （Building the user interface）


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

## Fetching data

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


### managing dependencies between components:

UserRepository 依赖 Webservice, 如果通过构造方法的方式创建 Webservice 对象的化就需要知道它类中的构造方法结构。并且 UserRepository 不是唯一一个需要 Webservice 的类。如果没有需要 Webservice 的类都通过构造方法创建这个对象，就会导致代码重复。

两种方法解决这个问题：

1. 依赖注入（Dependency Injection）: 使用 Dagger2 实现，推荐使用
2. Service LocatorL： 比 DI 简单


## Connection ViewModel and the repository

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


## Caching data

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


## 数据可持续化 Persisting data

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

![https://developer.android.com/topic/libraries/architecture/guide.html#recommended_app_architecture](https://developer.android.com/topic/libraries/architecture/guide.html#recommended_app_architecture)



## Testing

## The final architecture

# Guiding principles

# Addendum: exposing network status



