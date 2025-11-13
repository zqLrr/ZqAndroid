# Android 回调机制原理分析

## Android 监控回调的方式

### 1.Activity#OnBackPressedDispatcher()/OnBackInvokedDispatcher()

```kotlin
//onCreate
onBackPressedDispatcher.addCallback(this) {
    //自己实现退出操作
}
//onDestory

```

### 2.Activity#onKeyDown



### 3.view.setOnTouchListener  

通过实现ACTION_DOWN和ACTION_UP之间的距离判断返回

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





### Activity#OnBackPressedDispatcher实现原理



