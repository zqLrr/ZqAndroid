# Application

> ​		Application和Activity,Service一样,是android框架的一个系统组件，当android程序启动时系统会创建一个 application对象，用来存储系统的一些信息。通常我们是不需要指定一个Application的，这时系统会自动帮我们创建，如果需要创建自己 的Application，也很简单创建一个类继承 Application并在manifest的application标签中进行注册(只需要给Application标签增加个name属性把自己的 Application的名字定入即可)。

## 生命周期

Application的onCreate()是App的第一个入口

![img](/Users/zhangqian20/git profile/ZqAndroid/docs/png/70.png)

* onCreate() :Application创建的时候调用

  * 简单初始化，不要执行耗时操作，否则会拖慢APP启动速度
  * 定义全局常量或变量，要避免内存泄漏（可以用普通单例实现来代替）
  * 定义全局静态方法（可以用普通类实现来代替）

* onConfigurationChanged():当配置信息改变的时候会调用，如屏幕旋转、语言切换等。

* onLowMemory():当内存较低时，清理内存或提醒用户，来保证内存足够使用

* onTrimMemory():用于取代onLowmemory();

* onTerminate():Application结束的时候会调用，由系统决定调用的时机。

  Application 被杀死的情况分析：
  为了决定在内存较低的时候杀掉哪个进程, Android会根据运行在这些进程内的组件及他们的状态把进程划分成一个”重要程度层次”. 其重要的程度按以下规则排序:

  ![img](https://img-blog.csdn.net/20180716122447230?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hvdHR5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

  * Activity Process

    活动进程，也叫前台进程。Android会尝试通过回收各种资源来保证其运行状态。此类进程数量少，通常是最后被结束掉的。活

    动进程包含下面几种情况： 

    * 进程持有一个正在与用户交互的Activity(这个Activity的onResume()方法被调用)。 

    * 进程持有一个Service，这个Service处于这几种状态:

      ​	① Service与用户正在交互的Activity绑定。

      ​	② Service是在前台运行的，即它调用了 startForeground()。

      ​	③ Service正在执行它的生命周期回调函数（onCreate(), onStart(), or onDestroy()）

      ​     进程持有一个BroadcastReceiver，这个BroadcastReceiver正在执行它的 onReceive() 方法。

  * Visible Process
    可见进程，如果一个进程不含有任何前台活动的组件，不可与用户交互，但仍在屏幕上可见。可见进程包含下面几种情况：
    * 进程持有一个Activity，这个Activity不在前台，但是仍然被用户可见（处于onPause()调用后又没有调用onStop()的状态）
    * 进程持有一个Service，这个Service和一个可见的或者活动的Activity绑定。

  * Started Service Process

    启动后持有服务进程，该服务进程在没有可视化界面的情况下持续运行时。

  * Background Process

    后台进程，持有一个用户不可见的Activity（Activity的onStop()被调用，但是onDestroy()没有调用）的状态。通常有很多后台进

    程在运行。它们被保存在一个LRU(最近最少使用)列表中来确保拥有最近刚被看到的Activity的进程最后被杀死。

  * Empty Process

    空进程，不拥有入何活动组件的进程。为了改善整个系统的性能，Android会在内存中保留已经结束生命周期的应用程序，以便

    这些App能够快速启动。