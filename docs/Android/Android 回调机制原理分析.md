# Android 回调机制原理分析

## Android 监控回调的方式

### 1.Activity#OnBackPressedDispatcher()/OnBackInvokedDispatcher()

```kotlin
//onCreate

 // 1️⃣ 创建 OnBackPressedCallback
backCallback = object : OnBackPressedCallback(true) {
  override fun handleOnBackPressed() {
    // 当返回事件触发时调用
    Toast.makeText(
      this@BackDemoActivity,
      "返回事件被捕获（手势或返回键）",
      Toast.LENGTH_SHORT
    ).show()
  }
}
onBackPressedDispatcher.addCallback(backCallback) 
//onDestory
backCallback.remove()
```

### 2.Activity#onKeyDown

```kotlin

```

### 3.view.setOnTouchListener  

通过实现ACTION_DOWN和ACTION_UP之间的距离判断是否手势返回

```kotlin
var startX = 0f
val edgeWidth = 80 // 边缘触发范围 px

view.setOnTouchListener { _, event ->
    when (event.action) {
        MotionEvent.ACTION_DOWN -> startX = event.x
        MotionEvent.ACTION_UP -> {
            if (startX < edgeWidth && event.x - startX > 200) {
                Log.d("GestureBack", "检测到自定义左滑返回手势")
                activity.onBackPressedDispatcher.onBackPressed()
            }
        }
    }
    true
}

```

### Activity#OnBackPressedDispatcher代码原理分析

Activity 持有一个OnBackPressedDispatcher,是一个回调返回器，在onBackPressed()中执行

```java
//Activity.class
@Override
@MainThread
public void onBackPressed() {
 	mOnBackPressedDispatcher.onBackPressed();
}
```

 然后接下来会执行mOnBackPressedDispatcher 中包含的所有callback,是倒序队列来执行callback，若callback是true，就执行这个callback,然后返回，若没有，则执行默认的mFallbackOnBackPressed，即系统默认的方法。

```java
//mOnBackPressedDispatcher.class 
@MainThread
    public void onBackPressed() {
        Iterator<OnBackPressedCallback> iterator =
                mOnBackPressedCallbacks.descendingIterator();
        while (iterator.hasNext()) {
            OnBackPressedCallback callback = iterator.next();
            if (callback.isEnabled()) {
                callback.handleOnBackPressed();
                return;
            }
        }
        if (mFallbackOnBackPressed != null) {
            mFallbackOnBackPressed.run();
        }
    }
```

在看一下onBackPressed（）是如何执行的,发现是KeyEvent.KEYCODE_BACK 时候执行的

```java
public boolean onKeyDown(int keyCode, KeyEvent event)  {
  if (keyCode == KeyEvent.KEYCODE_BACK) {
    if (getApplicationInfo().targetSdkVersion
        >= Build.VERSION_CODES.ECLAIR) {
      event.startTracking();
    } else {
      onBackPressed();
    }
    return true;
  }
}
```

在看一下OnBackPressedDispatcher#addCallback() 和移除callback()的源码实现，为什么看这个呢？学习一下他们如何实现add 一次 就达到移除一次的目的。

```java
//OnBackPressedDispatcher.class
@MainThread
@NonNull
Cancellable addCancellableCallback(@NonNull OnBackPressedCallback onBackPressedCallback) {
  mOnBackPressedCallbacks.add(onBackPressedCallback);
  OnBackPressedCancellable cancellable OnBackPressedCancellable(onBackPressedCallback);
  onBackPressedCallback.addCancellable(cancellable);
  return cancellable;
}
```

返回一个Cancellable,供使用者移除。

这里不是只加了一个onBackPressedCallback,而是同时加了一个OnBackPressedCallback中的Cancellable

```java
//OnBackPressedDispatcher.java
ArrayDeque<OnBackPressedCallback> mOnBackPressedCallbacks = new ArrayDeque<>();
//OnBackPressedCallback.java
CopyOnWriteArrayList<Cancellable> mCancellables = new CopyOnWriteArrayList<>();
```

这里是为了实现OnBackPressedCallback 和 OnBackPressedDispatcher 实现多对多，每add 一次OnBackPressedCallback，就加一次Cancellable，这样移除的时候，可以实现add一次，remove一次相对应。

移除的时候，有以下几种方式。

```java
1.Cancellable#cancle() //合理用法
2.OnBackPressedCallback#remove() // callback 只有自己用
3.OnBackPressedCallback#setEnabled(false) // 不建议用
```

```java
 private class OnBackPressedCancellable implements Cancellable {
        private final OnBackPressedCallback mOnBackPressedCallback;
        OnBackPressedCancellable(OnBackPressedCallback onBackPressedCallback) {
            mOnBackPressedCallback = onBackPressedCallback;
        }

        @Override
        public void cancel() {
            mOnBackPressedCallbacks.remove(mOnBackPressedCallback);
            mOnBackPressedCallback.removeCancellable(this);
        }
    }
```

cancle流程是以下：

```java
cancellable.cancel()
     ↓
dispatcher.remove(callback)
     ↓
mOnBackPressedCallback.remove(callback)
```

OnBackPressedCallback#remove() 这个方法可以控制所有添加OnBackPressedCallback的次数一次清空。

```java
@MainThread
public final void remove() {
  for (Cancellable cancellable: mCancellables) {
    cancellable.cancel();
  }
}
```

总结一下：

方案一：系统源码，官方推荐，需要自己注意反注册

方案二：视图栈可以管控，但要依赖视图栈的处理，尽量保证不要出问题

方案三：不建议。

