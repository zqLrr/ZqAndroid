# Android 面试整理

资料整理

https://juejin.cn/post/7267737437953720359

## Android 基础

### 1.MVP 和MVVM 的区别

### 2.如何实现下拉刷新

考点：

* 自定义View 
* View绘制流程
* requestLayout
* 事件分发

SmartFreshLayout +  Coordationation + AppBar +  TabViews + viewPager +RecycleView

SmartFreshLayout+ RecycleView 实现

### 3.View缓存 View.Startup

### 4.Android 进程间通信的方式

### 5.handler 机制概述

handler 是如何造成内存泄漏的

handler 主线程为什么不会死锁和ANR

### 6.设计模式概述，源码中使用了哪些设计模式

### 7.Activity的生命周期，onDestory() 是否会延迟触发

要理解生命周期不是线性的

### 8.自定义View 

* View的绘制流程

  onMeasure() onLayout() onDraw()

  MeasureSpec

* 自定义View的方式

  | 类型                  | 定义                                                         | 使用场景                     |
  | --------------------- | ------------------------------------------------------------ | ---------------------------- |
  | 自定义组合控件        | 多个控件组合成为一个新的控件，方便多处复用                   | 如TitleBar                   |
  | 继承系统View控件      | 继承自TextView等系统控件，在系统控件的基础功能上进行扩展     | 对常用的系统控件作自定义扩展 |
  | 继承系统ViewGroup控件 | 继承自LinearLayout等系统控件，在系统控件的基础功能上进行扩展 | 对常用布局进行扩展           |
  | 直接继承View          | 不复用系统控件，继承View进行功能定义                         | 自定义View                   |
  | 直接继承ViewGroup     | 不复用系统控件，继承ViewGroup进行新的布局                    | 自定义ViewGroup              |

* 自定义属性的方式：TypedArray

### 9.Service的启动模式

### 10.Activity的启动流程和启动模式

### 11.事件分发机制

Activity->ViewGroup->View

| 方法名称                | 作用                                                   |
| ----------------------- | ------------------------------------------------------ |
| dispatchTouchEvent()    | 分发点击事件                                           |
| onInterceptTouchEvent() | 拦截事件（只在GroupView）                              |
| onTouch()               | View中OnTouchListener接口中定义的方法(GroupView和View) |
| onTouchEvent()          | 点击事件                                               |

注意：**子View中的监听事件优先级： OnTouchListener---->OnTouchEvent---->--->onLongClickListener OnClickListener**

取消事件发生情况：

1. 触摸事件被父视图拦截
2. 触摸事件序列中断
   * 系统弹出对话框、通知栏或其他窗口。
   * 用户切换到其他应用，导致当前应用的触摸事件被中断。
   * 设备进入锁屏状态。
3. 触摸事件被系统强制终止
4. 设备硬件问题或环境变化

事件冲突 举一个例子

* ScrollView和RecycleView的滑动冲突

  - **`ScrollView` 的滑动行为**：`ScrollView` 会负责整个内容区域的滑动，它希望能够捕获并处理滑动事件。
  - **`RecyclerView` 的滑动行为**：`RecyclerView` 是一个滚动容器，拥有自己的滑动功能。当 `RecyclerView` 可以滚动时，它会尝试消费垂直的滑动事件。

  当用户尝试滑动屏幕时，如果 `RecyclerView` 的内容足够少，它会将事件传递给 `ScrollView`，导致 `ScrollView` 滑动。如果 `RecyclerView` 内容足够多并能够滚动，`RecyclerView` 会尝试处理滑动事件。但是，在一些情况下，`RecyclerView` 可能无法正确消费滑动事件，或者 `ScrollView` 会错误地拦截滑动事件，导致 **滑动冲突**。

  解决方案

  1.使用 `NestedScrollView` 和 `RecyclerView` 的嵌套滚动支持，禁用RecycleView的滑动，从而只让ScrollView滑动

  2.**使用 `NestedScrollView` 与 `RecyclerView` 的共同滚动**,在RecyclerView滑动到底部后再允许NestedScrollView滑动

  ```java
  recyclerView.setOnTouchListener((v, event) -> {
      if (!recyclerView.canScrollVertically(1)) { // 当 RecyclerView 滚到底部时
          nestedScrollView.requestDisallowInterceptTouchEvent(false); // 允许 ScrollView 处理
      } else {
          nestedScrollView.requestDisallowInterceptTouchEvent(true); // 禁止 ScrollView 处理
      }
      return false;
  });
  ```

​		3.展开RecyclerView,设置warp_content,来使RecyclerView先执行滑动

* Coordanation + AppBarLayout + RecyclerView + 横向RecyclerView
  * 在点击到横向RecyclerView时，RecyclerView 发生了滑动，而不是AppBarLayout
  * 原因分析
    * AppBarLayout 的Behavior 判断是否接受滑动的时候 是通过判断是否是竖向的，横向判断为不接受嵌套滚动，从而导致了R ecyclerView来滑动。

### 12.Apk的打包编译过程

看神图：https://mp.weixin.qq.com/s?__biz=MzA5MzI3NjE2MA==&mid=2650265470&idx=1&sn=b0a8a269958339a9e33b6777572c97bb&chksm=88632611bf14af079ce3ee1df90dc4e830aa3c106a12def378a5c957ef948f0a1802cd51d404&scene=27

### 13.如何管理三方依赖，如何解决三方依赖冲突

### 14.存储管理方式，分清共有目录和私有目录

### 15.Gson 源码解析，Adapter的使用和原理，fromjson的原理

### 16.注解处理器的使用，在打包哪个过程处理的，可以结合ARouter讲 MRouter 也可以

### 17.跨平台开发

* RN
* Flutter
* Weex

### 18.包体积大小优化，减少包体积的方案

* 无用资源的查找和删除

### 19.Activity/Fragment的生命周期回调

### 20.ANR 是怎么产生的

### 21.图片大小是如何计算的

### 22.Android VSYNC和surface机制和窗口原理

### 23.Leakcanary的原理

### 24.RecycleView的缓存机制

* 内存缓存机制
* 内存波动角度
* 分析itemView绘制流程

### 25.RequestLayout 和 invalidate()的区别

| **特性**       | **`requestLayout`**           | **`invalidate`**       |
| -------------- | ----------------------------- | ---------------------- |
| **触发目的**   | 重新布局                      | 重新绘制内容           |
| **调用的方法** | `onMeasure()` 和 `onLayout()` | `onDraw()`             |
| **涉及范围**   | 包括子视图及父视图            | 仅限自身视图           |
| **场景适用**   | 尺寸、位置变化                | 外观内容变化           |
| **性能消耗**   | 较高（可能引发多次布局计算）  | 较低（仅触发绘制流程） |

### 26.启动模式和taskAffinity 的关系

**`taskAffinity`**

`taskAffinity` 指定 `Activity` 所属的默认任务栈，主要用于控制 `singleTask` 和 `singleInstance` 模式的 `Activity` 进入哪个任务栈。默认情况下，`Activity` 和应用的 `taskAffinity` 相同。

例如：

- 不同应用的 `Activity` 可能会共享相同的 `taskAffinity`，从而出现在同一任务栈中。
- 修改 `taskAffinity` 可以让 `singleTask` `Activity` 进入不同的任务栈，而不是应用默认的任务栈。

`singleTask` + `默认 taskAffinity`

- `singleTask` 的 `Activity` 进入已有的 `taskAffinity` 对应的任务栈。
- 如果任务栈中已有该 `Activity`，则会复用，并清除其上方的所有 `Activity`。

**示例：**

```
xml


复制编辑
<activity
    android:name=".SingleTaskActivity"
    android:launchMode="singleTask" />
```

**行为：**

- 如果 `SingleTaskActivity` 不在任务栈中，则创建新实例。
- 如果它已经在任务栈中，则会回到该 `Activity`，并清除其上的所有 `Activity`

`singleInstance`

- `singleInstance` 只能在独立的任务栈中运行，它的 `taskAffinity` 影响的是它独立任务栈的标识。
- 其他 `Activity` 不能进入这个任务栈。

### 27.Instrumentation 的作用

Android 提供的自动化测试框架 需要打出一个自动化测试包

## 28.sp是否支持多进程 多进程下有什么问题

Sp已经废弃了多进程，原因是因为 

（1） 文件系统不会主动通知其他进程，只有当下次读取时才能发现

（2）文件锁的持有会导致数据不一致，仍然存在竞争条件

（3）频繁读取xml,导致性能降低

### 29.LayoutInflater.inflate()的流程

### 30.同步屏障机制 系统中哪些使用了同步屏障机制

### 31.view能否在子线程更新

### 32.主线程耗时操作为什么会造成丢帧

### 33.RecycleView的复用机制 会有几级缓存

### 34.ThreadHandler 的原理 和是否需要退出

35.WebSocket的原理和使用

36.三方库线程池怎么管理

37.低内存怎么测，低内存怎么恢复

38.自定义View 的Action.Down Action.Move Action.Up 传递过程是不同的吗

39.Glide的源码分析 缓存和生命周期

Glide缓存级别

* ActiveResource
* MemoryCache
* ResourceCacheGenerator
* DataCacheGenerator
* SourceGenerator

40.JetPacket都用过哪些

41.MVVM 中viewModel 是如何实现LifeCycle的

42.ActivityThread的作用

*  **启动应用进程的真正入口**
*  **负责管理四大组件的生命周期回调**
*  负责主线程Looper的创建和消息循环
*  负责Application对象的创建
*  负责和AMS通讯
*  负责资源、ContextImpl、ContentResolver等初始化

43.viewModel是否实现生命周期

viewModel没有实现生命周期，而是依靠Activity的ModelStore来监控生命周期的 只监控了onDestory，而是通过`ViewModelStore`和`Lifecycle`间接关联，`ViewModelStore`依赖`Activity`或`Fragment`的生命周期，但又能跨配置变更复用，从而实现“**生命周期感知但比界面活得更久**”。

44.LifeCycle是怎么实现的

* 通过LifyCycleRegistry在生命周期通过改变状态实现的

45.Parcable 和Serialize 的区别

* 实现难度上
* 序列化速度
* 内存占用
* 使用场景
* 支持平台上

46.AIDL使用过吗？是怎么实现的

47.热启动和冷启动的区别

48.类加载机制 和热加载有关

49.Handler机制 idleHandler  View.post 和handler.post 区别

50.MMKV 和SP的优缺点

51.crash 抓取方法

Native Crash 如何处理，处理方式是什么

52.异步消息的内存泄漏 

53.OOM的类型都有哪些，如何处理

* 堆内存溢出 **Java Heap OOM**

* 栈内存溢出 **Stack OOM**

* 元空间内存溢出 

* Native 内存溢出  **Native Memory OOM**

* 直接分配内存溢出 **Direct Memory OOM**


54.sp的原理 commit 和 apply

55.ViewHolder 更新时 期望局部更新 怎么处理

56.布局嵌套过深会有什么问题



## Java 基础

1.Java 集合框架

2.Java多线程，线程池的构成

3.GC 有哪些算法，算法流程

4.GC什么时候会去回收一个对象

5.内存泄漏怎么检测，可以说Leakcanry

6.hashcode() 和equals() 的区别

7.hashMap put和set原理

8.ConcurrentHashMap  线程安全原理

9.JVM 内存模型

共享和私有区域

* 程序计数器（Program Counter Register）：线程私有的内存区域，用于存储当前线程执行的字节码的行号指示器。每个线程都有自己的程序计数器，线程切换时，程序计数器也会切换到下一个线程对应的指令地址。

* Java虚拟机栈（Java Virtual Machine Stack）：线程私有的内存区域，用于存储方法调用时的数据和部分结果。每个方法在执行时都会创建一个栈帧（Stack Frame），用于存储局部变量表、操作数栈、动态链接信息和方法返回地址等。

* 本地方法栈（Native Method Stack）：与Java虚拟机栈类似，但用于存储本地方法（如使用JNI编写的方法）的调用数据。

* 堆（Heap）：所有线程共享的内存区域，用于存储对象实例和数组。Java中几乎所有的对象实例都是在这里分配内存的。

* 方法区（Method Area）：所有线程共享的内存区域，用于存储类信息、常量、静态变量等数据。它类似于C++中的全局/静态存储区。

​        运行时常量池（Runtime Constant Pool）：是方法区的一部分，用于存储编译期生成的各种字面量和符号引用。

10.双亲委派原则

11.SSL的请求流程

12.Java内存是如何保证多态的

13.Synchoronized的用法

14.Volatile 用法 为什么不保证原子性

15.三次握手和四次挥手 后两次挥手的作用 or 为什么是三次或者四次

16.okHttp 责任链机制的原理、为什么要使用责任链

okHttp 的dispatch 是怎么实现的

17.ok库中HTTP 1.1 和2.0的区别

18.ok库中链接池的复用和销毁机制

19.OOM的场景有哪些 为什么线程创建时会造成OOM

20.MVVM 的使用 LiveData是如何保证生命周期监听的

21.volatile 关键字的使用和原理 可见性和有序性

22.System.GC() 是否一定会调用到FullGC

23.类的头对象是怎么样的

24.CAS Syncized 原理和自旋代码

25.Java new 一个对象的过程

26. 设计模式 和 接口设计六大原则
27. JDK newInstance的动态代理原理

## Kotlin 面经

1.Kotlin的优势

- 完全兼容Java
- Null safe
- 支持lambda表达式（比Java8更好）
- 支持扩展
- 体验一致的开发工具链

2.kotlin 扩展函数 反编译后怎么使用

3.kotlin协程 是如何实现协程调度的

5.by laze 和 lateinit的区别 lateinit的坑

