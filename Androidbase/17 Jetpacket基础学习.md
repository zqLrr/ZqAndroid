# 17 Jetpacket基础学习

## 1.概述

> Jetpack 是一个由多个库组成的套件，可帮助开发者遵循最佳做法、减少样板代码并编写可在各种 Android 版本和设备中一致运行的代码，让开发者可将精力集中于真正重要的编码工作。

## 2.LifeCycle --使用生命周期感知型组件处理生命周期 

[官方文档](https://developer.android.google.cn/jetpack/androidx/releases/lifecycle)

[用户指南](https://developer.android.google.cn/topic/libraries/architecture/lifecycle)

### 1.概述

>在Android 9之后，我们开始使用androidx，该命名空间下包含了 [Android Jetpack](https://links.jianshu.com/go?to=https%3A%2F%2Fdeveloper.android.google.cn%2Fjetpack) 库，AppCompatActivity已经实现了LifeCycleOwner,意味着我们可以直接使用LifeCycle管理Activity和Fragment的生命周期

### 2.声明依赖

### 3.LifeCycle中的类

* Lifecycle
* DefaultLifecycleObserver
* LifecycleOwner

#### LiyeCycle 

> [`Lifecycle`](https://developer.android.google.cn/reference/androidx/lifecycle/Lifecycle) 是一个类，用于存储有关组件（如 Activity 或 Fragment）的生命周期状态的信息，并允许其他对象观察此状态。

![image-20220728211849509](/Users/zhangqian20/Library/Application Support/typora-user-images/image-20220728211849509.png)

#### DefaultLifecycleObserver

> 类可以通过实现 [`DefaultLifecycleObserver`](https://developer.android.google.cn/reference/androidx/lifecycle/DefaultLifecycleObserver) 并替换相应的方法（如 `onCreate` 和 `onStart` 等）来监控组件的生命周期状态。然后，您可以通过调用 [`Lifecycle`](https://developer.android.google.cn/reference/androidx/lifecycle/Lifecycle) 类的 [`addObserver()`](https://developer.android.google.cn/reference/androidx/lifecycle/Lifecycle#addObserver(androidx.lifecycle.LifecycleObserver)) 方法并传递观察器的实例来添加观察器。

```java
//code example
public class MyObserver implements DefaultLifecycleObserver {
    @Override
    public void onResume(LifecycleOwner owner) {
        connect()
    }

    @Override
    public void onPause(LifecycleOwner owner) {
        disconnect()
    }
}

myLifecycleOwner.getLifecycle().addObserver(new MyObserver());
```

#### LifecycleOwner

> [`LifecycleOwner`](https://developer.android.google.cn/reference/androidx/lifecycle/LifecycleOwner) 是单一方法接口，表示类具有 [`Lifecycle`](https://developer.android.google.cn/reference/androidx/lifecycle/Lifecycle)。它具有一种方法（即 [`getLifecycle()`](https://developer.android.google.cn/reference/androidx/lifecycle/LifecycleOwner#getLifecycle())）,此接口从各个类（如 `Fragment` 和 `AppCompatActivity`）抽象化 [`Lifecycle`](https://developer.android.google.cn/reference/androidx/lifecycle/Lifecycle) 的所有权，并允许编写与这些类搭配使用的组件。任何自定义应用类均可实现 [`LifecycleOwner`](https://developer.android.google.cn/reference/androidx/lifecycle/LifecycleOwner) 接口。

##### Lifecycleowner允许查询当前的生命周期状态

```java
class MyLocationListener implements DefaultLifecycleObserver {
    private boolean enabled = false;
    public MyLocationListener(Context context, Lifecycle lifecycle, Callback callback) {
       ...
    }

    @Override
    public void onStart(LifecycleOwner owner) {
        if (enabled) {
           // connect
        }
    }

    public void enable() {
        enabled = true;
        if (lifecycle.getCurrentState().isAtLeast(STARTED)) {
            // connect if not connected
        }
    }
  
    @Override
    public void onStop(LifecycleOwner owner) {
        // disconnect if connected
    }
}
```

##### 实现自定义Lifecycleowner

需要使用 [LifecycleRegistry](https://developer.android.google.cn/reference/androidx/lifecycle/LifecycleRegistry) 类，但需要将事件转发到该类。

```java
public class MyActivity extends Activity implements LifecycleOwner {
    private LifecycleRegistry lifecycleRegistry;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        lifecycleRegistry = new LifecycleRegistry(this);
        lifecycleRegistry.setCurrentState(Lifecycle.State.CREATED);
    }

    @Override
    public void onStart() {
        super.onStart();
        lifecycleRegistry.setCurrentState(Lifecycle.State.STARTED);
    }

    @NonNull
    @Override
    public Lifecycle getLifecycle() {
        return lifecycleRegistry;
    }
}
```

#### ProcessLifecycleOwner

> 管理整个应用进程的生命周期