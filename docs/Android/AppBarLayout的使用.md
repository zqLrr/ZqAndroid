## AppBarLayout的使用

layout_scrollFlags：

* scroll：子View会跟随滚动事件一起发生移动而滚出或滚进屏幕

  * 如果使用了其他值，必定要使用这个值才能起作用
  * 如果在这个子View前面的任何其他子View没有设置这个值，那么这个子View的设置将失去作用。
  * 如果不设置，则不会跟随滑动

* enterAlways

  * 往下滑动，子View会先展示

* enterAlwaysCollapsed

  * 往下滑动，滚动view会先展示

* enterAlways｜enterAlwaysCollapsed

  * 当enterAlways附加enterAlwaysCollapsed，会有一个最小高度

* exitUntilCollapsed

  * 这里也涉及到最小高度。发生向上滚动事件时，子View向上滚动直至最小高度，然后Scrolling View开始滚动。也就是，子View不会完全退出屏幕。

* snap

  * 子View不会存在局部显示的情况，滚动到子View的部分高度，当我们松开手指时，子View要么向上全部滚出屏幕，要么向下全部滚进屏幕。

* snapMargins

  * snapMargins是必须配合snap一起使用的额外的flag。如果设置的话，这个View将会被snap到它的顶部外边距和它的底部外边距的位置，而不是这个View自身的上下边缘。




## 滑动recycleView时的行为：

RecycleView#onInterceptTouchEvent

RecycleView#startNestedScroll

ViewParent#onNestedScroll

CoordinatorLayout#onStartNestedScroll

AppBarLayout$Behavior#onStartNestedScroll

## 滑动AppBar时的行为：

CoondinatorLayout  会判断是否需要拦截滑动事件，若需要拦截，使用每个子View#Behavior#onTouchEvent(),若不需要拦截，子View#Behavior#onInterceptTouchEvent

RecycleView#onTouchEvent

 ViewParentCompat#onNestedPreScroll

CoordinatorLayout#onNestedPreScroll()

HeaderBehavior#scroll

AppBarLayout$BaseBehavior#setHeaderTopBottomOffset



``` 
mDidAcceptNestedScrollTouch = false
```

