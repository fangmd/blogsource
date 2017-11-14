

## LiveData

LiveData: 一个可以被观察的数据持有类。不想普通的可被观察类，它具有绑定生命周期的特性。



### The advantages of using LiveData


- 可以确保你的 UI界面 符合数据 state
- 不会造成内存泄漏
- 在 Activity stop 的时候不会奔溃
- 不需要手动处理生命周期
- 可以自动更新最新数据
- Proper configuration changes
- 可以实现资源共享


### Work with LiveData objects

#### Create LiveData objects

LiveData 对象通常会作为 ViewModel 的成员变量。

例子：

```java
public class NameViewModel extends ViewModel {

// Create a LiveData with a String
private MutableLiveData<String> mCurrentName;

    public MutableLiveData<String> getCurrentName() {
        if (mCurrentName == null) {
            mCurrentName = new MutableLiveData<String>();
        }
        return mCurrentName;
    }

// Rest of the ViewModel...
}
```


#### Observe LiveData objects

通常在 `onCreate` 方法中写 观察LiveData 的相关代码，原因：

1. 减轻 `onResume()` 方法的负担
2. 确保数据尽量早的显示


例子：

```java
public class NameActivity extends AppCompatActivity {

    private NameViewModel mModel;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        // Other code to setup the activity...

        // Get the ViewModel.
        mModel = ViewModelProviders.of(this).get(NameViewModel.class);

        // Create the observer which updates the UI.
        final Observer<String> nameObserver = new Observer<String>() {
            @Override
            public void onChanged(@Nullable final String newName) {
                // Update the UI, in this case, a TextView.
                mNameTextView.setText(newName);
            }
        };

        // Observe the LiveData, passing in this activity as the LifecycleOwner and the observer.
        mModel.getCurrentName().observe(this, nameObserver);
    }
}
```

在 LiveData 中的数据变化的时候 nameObserver 的中的方法就会触发。

`mModel.getCurrentName().observe(this, nameObserver);`, 第一个参数让 LiveData 和 Activity 的生命周期关联，之后就不需要担心内存泄漏的问题了。


#### Update LiveData objects

user `setValue(T)`, `postValue(T)` change LiveData.

>setValue method must be called from main thread, but postValue can be called from worker thread.

例子：

```java
mButton.setOnClickListener(new OnClickListener() {
    @Override
    public void onClick(View v) {
        String anotherName = "John Doe";
        mModel.getCurrentName().setValue(anotherName);
    }
});
```


#### Use LiveData with Room 


### Extend LiveData

example:

```java
public class StockLiveData extends LiveData<BigDecimal> {
    private StockManager mStockManager;

    private SimplePriceListener mListener = new SimplePriceListener() {
        @Override
        public void onPriceChanged(BigDecimal price) {
            setValue(price);
        }
    };

    public StockLiveData(String symbol) {
        mStockManager = new StockManager(symbol);
    }

    @Override
    protected void onActive() {
        mStockManager.requestPriceUpdates(mListener);
    }

    @Override
    protected void onInactive() {
        mStockManager.removeUpdates(mListener);
    }
}
```

让多个 activity 和 Fragment， services 共享 LiveData：

```java
public class StockLiveData extends LiveData<BigDecimal> {
    private static StockLiveData sInstance;
    private StockManager mStockManager;

    private SimplePriceListener mListener = new SimplePriceListener() {
        @Override
        public void onPriceChanged(BigDecimal price) {
            setValue(price);
        }
    };

    @MainThread
    public static StockLiveData get(String symbol) {
        if (sInstance == null) {
            sInstance = new StockLiveData(symbol);
        }
        return sInstance;
    }

    private StockLiveData(String symbol) {
        mStockManager = new StockManager(symbol);
    }

    @Override
    protected void onActive() {
        mStockManager.requestPriceUpdates(mListener);
    }

    @Override
    protected void onInactive() {
        mStockManager.removeUpdates(mListener);
    }
}
```

使用上面这个共享的 LiveData

```java
public class MyFragment extends Fragment {
    @Override
    public void onActivityCreated(Bundle savedInstanceState) {
        StockLiveData.get(getActivity()).observe(this, price -> {
            // Update the UI.
        });
    }
}
```


### Transform LiveData


- map 操作符，改变 Observer 获取的数据：

```java
LiveData<User> userLiveData = ...;
LiveData<String> userName = Transformations.map(userLiveData, user -> {
    user.name + " " + user.lastName
});
```

- switchMap，改变 LiveData对象 并返回 LiveData 对象（不会创建新的 LiveData）

```java
private LiveData<User> getUser(String id) {
  ...;
}

LiveData<String> userId = ...;
LiveData<User> user = Transformations.switchMap(userId, id -> getUser(i
```


Transform 的一个妙用：

错误案例：

```java
class MyViewModel extends ViewModel {
    private final PostalCodeRepository repository;
    public MyViewModel(PostalCodeRepository repository) {
       this.repository = repository;
    }

    private LiveData<String> getPostalCode(String address) {
       // DON'T DO THIS
       return repository.getPostCode(address);
    }
}
```

上面的代码 `getPostalCode` 方法每次调用的时候就会创建新的 LiveData，UI 组件就需要重新和新的 LiveData 进行绑定。

正确做法：

```java
class MyViewModel extends ViewModel {
    private final PostalCodeRepository repository;
    private final MutableLiveData<String> addressInput = new MutableLiveData();
    public final LiveData<String> postalCode =
            Transformations.switchMap(addressInput, (address) -> {
                return repository.getPostCode(address);
             });

  public MyViewModel(PostalCodeRepository repository) {
      this.repository = repository
  }

  private void setInput(String address) {
      addressInput.setValue(address);
  }
}
```


#### Create new transformations

### Merge multiple LiveData sources

## ViewModel

### Implements a ViewModel

### The lifecycle of a ViewModel

### Share data between fragments

### Replacing Loaders with ViewModel


