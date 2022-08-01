# RxJAVA	基础知识

## 1、使用RxJAVA的理由：

​    1.实现了异步

​    2.代码是链式的，逻辑比较简洁

## 2、原理

观察者模式：观察者模式面向的需求是：A 对象（观察者）对 B 对象（被观察者）的某种变化高度敏感，需要在 B 变化的一瞬间做出反应。举个例子，新闻里喜闻乐见的警察抓小偷，警察需要在小偷伸手作案的时候实施抓捕。在这个例子里，警察是观察者，小偷是被观察者，警察需要时刻盯着小偷的一举一动，才能保证不会漏过任何瞬间。程序的观察者模式和这种真正的『观察』略有不同，观察者不需要时刻盯着被观察者（例如 A 不需要每过 2ms 就检查一次 B 的状态），而是采用**注册**(Register)**或者称为**订阅**(Subscribe)**的方式，告诉被观察者：我需要你的某某状态，你要在它变化的时候通知我。 Android 开发中一个比较典型的例子是点击监听器 `OnClickListener` 。对设置 `OnClickListener` 来说， `View` 是被观察者， `OnClickListener` 是观察者，二者通过 `setOnClickListener()` 方法达成订阅关系。订阅之后用户点击按钮的瞬间，Android Framework 就会将点击事件发送给已经注册的 `OnClickListener` 。采取这样被动的观察方式，既省去了反复检索状态的资源消耗，也能够得到最高的反馈速度。当然，这也得益于我们可以随意定制自己程序中的观察者和被观察者，而警察叔叔明显无法要求小偷『你在作案的时候务必通知我』。

即：观察者订阅被观察者，但是被观察者发送自己的变化给观察者。

## 3、核心概念：

### 1、Observable(被观察者)：

* create():创建一个Observable,并为它定义事件触发规则

  ```java
     Observable<String> observable = Observable.create(new ObservableOnSubscribe<String>() {
              //传入的是OnSubscribe 对象
              //当 Observable 被订阅时，OnSubscribe 的 call() 方法会自动被调用，即事件序列就会依照设定依次被触发
              @Override
              public void subscribe(@NonNull ObservableEmitter<String> emmiter) throws Exception {
                  //在这里写产生的事件
                  // 通过 ObservableEmitter类对象 产生 & 发送事件
                  // a. 定义：事件发射器
                  // b. 作用：定义需要发送的事件 & 向观察者发送事件
                  // 注：建议发送事件前检查观察者的isUnsubscribed状态，以便在没有观察者时，让Observable停止发射数据, observer为观察者对象
                  if (!observer.isUnsubscribed()) {
                      emmiter.onNext("zhangsan");
                      emmiter.onNext("lisi");
                  }
               
                 emmiter.onComplete();
              }
          });
  //常规使用
   Observable.create(new ObservableOnSubscribe<String>() {
              @Override
              public void subscribe(@NonNull ObservableEmitter<String> emmiter) throws Exception {
                      emmiter.onNext("zhangsan");
                      emmiter.onNext("lisi");
                 emmiter.onComplete();
              }
          }).subscribe(new Observer<String>() {
     //事件的整个流程
              @Override
              public void onSubscribe(@NonNull Disposable d) {
                  Log.d(TAG, "开始采用subscribe连接");
              }
  
              @Override
              public void onNext(@NonNull String s) {
                  Log.d(TAG, "onNext: Hello,"+s);
              }
  
              @Override
              public void onError(@NonNull Throwable e) {
                  Log.d(TAG, "onError: ");
              }
  
              @Override
              public void onComplete() {
                  Log.d(TAG, "onComplete:");
              }
          });
  ```

  可以看到，这里传入了一个 `OnSubscribe` 对象作为参数。`OnSubscribe` 会被存储在返回的 `Observable` 对象中，它的作用相当于一个计划表，当 `Observable` 被订阅的时候，`OnSubscribe` 的 `call()` 方法会自动被调用，事件序列就会依照设定依次触发（对于上面的代码，就是观察者`Subscriber` 将会被调用三次 `onNext()` 和一次 `onCompleted()`）。这样，由被观察者调用了观察者的回调方法，就实现了由被观察者向观察者的事件传递，即观察者模式。

* just(T...):基于create()方法，将传入的参数一次发出来

* from(T[]....)/from(Iterable<?,extends,T>):基于create()方法，将数组或Itreable拆分成具体对象后，依次发送出来

* onNext():回调方法

* onComplete()：回调方法，当不会有新的onNext()发出后，执行onComplete()

* onError()：回调方法，当事件队列异常时，会触发onError()，并终止

* onComplete()和onError()方法只会出现一个

* filter：过滤

* switchIfEmpty() 如果是空的如何处理

* map：转换数据

### 2、Observer(观察者)：

* 存在两个类：Observer和Subscriber,Subscriber是实现Observer的抽象类，实质上都是在使用后者。

* 创建Observer和Subscriber

  ```java
  Observer<String> observer = new Observer<String>() {
      @Override
      public void onNext(String s) {
          Log.d(tag, "Item: " + s);
      }
  
      @Override
      public void onCompleted() {
          Log.d(tag, "Completed!");
      }
  
      @Override
      public void onError(Throwable e) {
          Log.d(tag, "Error!");
      }
  };
  Subscriber<String> subscriber = new Subscriber<String>() {
      @Override
      public void onNext(String s) {
          Log.d(tag, "Item: " + s);
      }
  
      @Override
      public void onCompleted() {
          Log.d(tag, "Completed!");
      }
  
      @Override
      public void onError(Throwable e) {
          Log.d(tag, "Error!");
      }
  };
  ```

* 两个类的区别：Subscriber增加和实现了两个方法。

  * onStart():在subscrib刚开始，还未发送数据之前的进行数据准备，只能在subscrib所发生的线程中调用.
  * Unsurscribe():用于取消订阅。

### 3、subscribe():(实现订阅)：

* 代码实现：

  ```java
  observable.subscribe(observer);
  // 或者：
  observable.subscribe(subscriber);
  ```

* 核心源码：

  ```java
  // 注意：这不是 subscribe() 的源码，而是将源码中与性能、兼容性、扩展性有关的代码剔除后的核心代码。
  // 如果需要看源码，可以去 RxJava 的 GitHub 仓库下载。
  public Subscription subscribe(Subscriber subscriber) {
      subscriber.onStart();
      onSubscribe.call(subscriber);
      return subscriber;
  }
  ```

  做了三件事：

  * 调用 `Subscriber.onStart()` 。这个方法在前面已经介绍过，是一个可选的准备方法。
  * 调用 `Observable` 中的 `OnSubscribe.call(Subscriber)` 。在这里，事件发送的逻辑开始运行。从这也可以看出，在 RxJava 中， `Observable` 并不是在创建的时候就立即开始发送事件，而是在它被订阅的时候，即当 `subscribe()` 方法执行的时候。
  * 将传入的 `Subscriber` 作为 `Subscription` 返回。这是为了方便 `unsubscribe()`.

### 4、取消订阅

1、Disposable 取消订阅

```java
Disposable disposable = Observable.just(1).subscribe(new Consumer<Integer>() {
    @Override
    public void accept(@NonNull Integer integer) throws Exception {

    }
});

//取消订阅
if(disposable != null && !disposable.isDisposed()){
     disposable.dispose();
}

//或者直接使用Observer中的onSubscribe()方法
private Disposable disposable;
Observable.just(1).subscribe(new Observer<Integer>() {
       @Override
       public void onSubscribe(Disposable d) {
            disposable = d;
       }

       @Override
       public void onNext(Integer integer) {}

       @Override
       public void onError(Throwable e) {}

       @Override
       public void onComplete() {}
});

//然后在需要取消订阅的地方调用即可
if(disposable != null && !disposable.isDisposed()){
     disposable.dispose();
}
```

2、DisposableObserver 取消订阅

```java
//比如这个是我们的Observer
DisposableObserver observer = new DisposableObserver() {
      @Override
      public void onNext(Object o) {}
      @Override
      public void onError(Throwable e) {}
      @Override
      public void onComplete() {}
};
//把我们的Observer对Observable进行订阅        
Observable.just(1).subscribe(observer);

//然后在需要取消订阅的地方对这个observer进行取消订阅即可。
observer.dispose();
```

3、DisposableObserver+subscribeWith 取消订阅

```java
DisposableObserver observer = Observable.just(1).subscribeWith(new DisposableObserver<Integer>() {
     @Override
     public void onNext(Integer integer) {}
     @Override
     public void onError(Throwable e) {}
     @Override
     public void onComplete() {}
});

//需要取消订阅的地方：
observer.disposable();
```

4、CompositeDisposable 同时取消多个订阅

```java
CompositeDisposable compositeDisposable = new CompositeDisposable();
//批量添加
compositeDisposable.add(observer1);
compositeDisposable.add(observer2);
compositeDisposable.add(observer2);
//最后一次性全部取消订阅
compositeDisposable.dispose();
```

5、RxLifeCycle 取消订阅

### 4. 线程控制

* 默认的线程

  **被观察者 `（Observable）` / 观察者`（Observer）`的工作线程 = 创建自身的线程**，即在哪里创建在哪里发送事件/接收事件。

  ```java
   Observable.create(new ObservableOnSubscribe<Integer>() {
              @Override
              public void subscribe(@NonNull ObservableEmitter<Integer> e) throws Exception {
                  Log.d(TAG, "create: "+ Thread.currentThread().getName());
                  e.onNext(1);
              }
          })
                  .subscribeOn(Schedulers.io())
                  .doOnSubscribe(new Consumer<Disposable>() {
                      @Override
                      public void accept(Disposable disposable) throws Exception {
                          Log.d(TAG, "accept: "+ Thread.currentThread().getName());
                      }
                  })
                  .subscribeOn(AndroidSchedulers.mainThread())
                  .flatMap(new Function<Integer, ObservableSource<Boolean>>() {
                      @Override
                      public ObservableSource<Boolean> apply(@NonNull Integer integer) throws Exception {
  
                          return Observable.create(new ObservableOnSubscribe<String>() {
  
                              @Override
                              public void subscribe(@NonNull ObservableEmitter<String> e) {
                                  try {
                                      Log.d(TAG, "subscribe: "+ Thread.currentThread().getName());
                                      testRxjava test = NetEngineFactory.CreateNetEngine(OkHttpNetEngine.class, RxjavaMainActivity.this).testFloatMap();
                                      e.onNext(test.getErrno());
                                  }catch (Exception error){
                                      e.onError(error);
                                  }
                              }
                          }).map(new Function<String, Boolean>() {
                              @Override
                              public Boolean apply(@NonNull String s) throws Exception {
  //                                Log.d(TAG, "testRxjava: map");
                                  Log.d(TAG, "map: "+ Thread.currentThread().getName());
                                  if(TextUtils.equals(s,"10000")){
                                      return  true;
                                  }
                                  return false;
                              }
                          });
                      }
                  })
                  .observeOn(AndroidSchedulers.mainThread())
                  .subscribe(rst -> {
              Log.d(TAG, "testRxjava: final result "+ rst);
          }, error -> {
              Log.d(TAG, "testRxjava: "+ error.toString());
          });
  //测试结果
  2022-04-01 17:22:51.340 13672-13672/com.example.androiddemo D/testRxjava: accept: main
  2022-04-01 17:22:51.341 13672-13832/com.example.androiddemo D/testRxjava: create: RxCachedThreadScheduler-1
  2022-04-01 17:22:51.342 13672-13832/com.example.androiddemo D/testRxjava: subscribe: RxCachedThreadScheduler-1
  2022-04-01 17:22:55.054 13672-13832/com.example.androiddemo D/testRxjava: map: RxCachedThreadScheduler-1
  2022-04-01 17:22:55.055 13672-13672/com.example.androiddemo D/testRxjava: testRxjava: final result false
  ```

  以上流程可以完成一个AsyncTask的步骤，异步线程如果只需要一个，可以只使用subscribeOn()。

* Secheduler API   调度器，通过它来控制应该在哪个线程运行

  * `Schedulers.immediate()`: 直接在当前线程运行，相当于不指定线程。这是默认的 `Scheduler`。（已被移除）

  * `Schedulers.newThread()`: 总是启用新线程，并在新线程执行操作。

  * `Schedulers.io()`: I/O 操作（读写文件、读写数据库、网络信息交互等）所使用的 `Scheduler`。行为模式和 `newThread()` 差不多，区别在于 `io()` 的内部实现是是用一个无数量上限的线程池，可以重用空闲的线程，因此多数情况下 `io()` 比 `newThread()` 更有效率。不要把计算工作放在 `io()` 中，可以避免创建不必要的线程。

  * `Schedulers.computation()`: 计算所使用的 `Scheduler`。也是一个线程池，区别于I/O调度的是线程数=CPU核数，这个计算指的是 CPU 密集型计算，即不会被 I/O 等操作限制性能的操作，例如图形的计算。这个 `Scheduler` 使用的固定的线程池，大小为 CPU 核数。不要把 I/O 操作放在 `computation()` 中，否则 I/O 操作的等待时间会浪费 CPU，适用于计算操作。

  * `Schedulers.Single()`:由一个线程支持，无论有多少个observables,都将只运行在这个线程上。也可将其认为是主线程的一个替代。

  * 如果你有代码运行在主线程上，它会将将要运行的代码块添加到主线程的队列。和Immediate非常相似，不同的是Immediate会阻塞此线程，而Trampoline会等待当前任务执行完成。适用用于不止一个observables，并且希望它们能够按照顺序执行的场景。

  * `Schedulers.trampoline()`:运行在当前线程，在其中一个参与线程中以顺序和 FIFO 方式运行工作，通常用于测试目的。

  * `Schedulers.from(Executor)`：自定义线程池，适用于Observable远多于IO调度的场景。

    ```java
    val executor = Executors.newFixedThreadPool(10) 
    val pooledScheduler = Schedulers.from(executor)
    ```

  * 另外， Android 还有一个专用的 `AndroidSchedulers.mainThread()`，它指定的操作将在 Android 主线程运行。

  

* subscribeOn()方法：指定subscribe()所发生的线程。

* observeOn():指定subscribe()所运行的线程。

  ```java
  Observable.just(1, 2, 3, 4)
      .subscribeOn(Schedulers.io()) // 指定 subscribe() 发生在 IO 线程
      .observeOn(AndroidSchedulers.mainThread()) // 指定 Subscriber 的回调发生在主线程
      .subscribe(new Action1<Integer>() {
          @Override
          public void call(Integer number) {
              Log.d(tag, "number:" + number);
          }
      });
  ```

  在IO线程发出数据，在andriod的主线程进行调用。

* 线程控制原理

  ```java
  Observable.just(1, 2, 3, 4) // IO 线程，由 subscribeOn() 指定
      .subscribeOn(Schedulers.io())
      .observeOn(Schedulers.newThread())
      .map(mapOperator) // 新线程，由 observeOn() 指定
      .observeOn(Schedulers.io())
      .map(mapOperator2) // IO 线程，由 observeOn() 指定
      .observeOn(AndroidSchedulers.mainThread) 
      .subscribe(subscriber);  // Android 主线程，由 observeOn() 指定
  ```

  以上为例，我们知道subscribeOn()指定事件产生的线程，observeOn()指定subscribe()运行的线程。

  * subscribeOn()的原理图

       ![subscribeOn() 原理](RxJAVA 基础知识.assets/e26b388ffc7b4411ab268c7b7ea15974.jpeg)

    subscribeOn()发生在OnSubscribe()中，通知上一级之前。此时事件还没有发送，在事件开端 就进行了切换，因此当有多个subscribeOn()时，会被第一个subscribeOn()拦截，后续的subscribeOn()将不会对事件流程发生改变。

  * observeOn()的原理图

    ![observeOn() 原理](RxJAVA 基础知识.assets/8f90ce42e3344f578c558a6f8f58a244.jpeg)

    observeOn()发生在自己内建的Subscriber中，发生在即将发送给下一级的Subscriber中，控制的是后面的线程，因此可以有多个observeOn()发生。

    * doOnSubscribe():因为Subscriber.onStart()发生在事件发送之前，而subscribe()所运行的线程可以被observe()指定，所以onStart()发生在哪个线程不能指定，一些需要数据准备在主线程的操作无法执行，于是使用了doOnSubscribe()方法。
      * doOnsubscibe()默认发生的线程是subscibe()发生的线程。
      * 可以在doOnsubscribe()之后由subscribeOn()指定所发生的线程

    ```java
    Observable.create(onSubscribe)
        .subscribeOn(Schedulers.io())
        .doOnSubscribe(new Action0() {
            @Override
            public void call() {
                progressBar.setVisibility(View.VISIBLE); // 需要在主线程执行
            }
        })
        .subscribeOn(AndroidSchedulers.mainThread()) // 指定主线程
        .observeOn(AndroidSchedulers.mainThread())
        .subscribe(subscriber);
    ```

### 5. 变换原理

将事件序列中的对象或整个序列进行加工处理，转换成不同的事件。

变换操作发生在订阅之前，默认和事件发射是同个线程

* 变换的原理：lift().  map()和flatmop()都是基于这个这个方法。

  * 核心源码：

    ```java
    // 注意：这不是 lift() 的源码，而是将源码中与性能、兼容性、扩展性有关的代码剔除后的核心代码。
    // 如果需要看源码，可以去 RxJava 的 GitHub 仓库下载。
    public <R> Observable<R> lift(Operator<? extends R, ? super T> operator) {
        return Observable.create(new OnSubscribe<R>() {
            @Override
            public void call(Subscriber subscriber) {
                Subscriber newSubscriber = operator.call(subscriber);
                newSubscriber.onStart();
                onSubscribe.call(newSubscriber);
            }
        });
    }
    ```

  * `subscribe()` 中这句话的 `onSubscribe` 指的是 `Observable` 中的 `onSubscribe` 对象，这个没有问题，但是 `lift()` 之后的情况就复杂了点。

  * 当含有 `lift()` 时：
    1.`lift()` 创建了一个 `Observable` 后，加上之前的原始 `Observable`，已经有两个 `Observable` 了；
    2.而同样地，新 `Observable` 里的新 `OnSubscribe` 加上之前的原始 `Observable` 中的原始 `OnSubscribe`，也就有了两个 `OnSubscribe`；
    3.当用户调用经过 `lift()` 后的 `Observable` 的 `subscribe()` 的时候，使用的是 `lift()` 所返回的新的 `Observable` ，于是它所触发的 `onSubscribe.call(subscriber)`，也是用的新 `Observable` 中的新 `OnSubscribe`，即在 `lift()` 中生成的那个 `OnSubscribe`；
    4.而这个新 `OnSubscribe` 的 `call()` 方法中的 `onSubscribe` ，就是指的原始 `Observable` 中的原始 `OnSubscribe` ，在这个 `call()` 方法里，新 `OnSubscribe` 利用 `operator.call(subscriber)` 生成了一个新的 `Subscriber`（`Operator` 就是在这里，通过自己的 `call()` 方法将新 `Subscriber` 和原始 `Subscriber` 进行关联，并插入自己的『变换』代码以实现变换），然后利用这个新 `Subscriber` 向原始 `Observable` 进行订阅。
    这样就实现了 `lift()` 过程，有点**像一种代理机制，通过事件拦截和处理实现事件序列的变换**

  * 简单说：当observable执行了lift()函数以后，返回的是新的observable，这个新的observable将原始的observable发出的事件进行处理和变换，最后返回给subcriber.

    ![lift() 原理图](RxJAVA 基础知识.assets/5c39e24d01ba40379089726ff8984e1e.jpeg)

### 6、冷/热Observable

https://juejin.cn/post/6844903476883881998#comment

* hot Observable 

  如果开始传输数据，你不主动喊停(`dispose()`/`cancel()`)，那么他就不会停，一直发射数据，即使他已经没有Subscriber了。特点：不同的Subscriber，共享事件

  可以使用`publish`将Cold  Observable转换为hot Observable。

* Cold  Observable

  只有当订阅(subscriber)了的时候才会发射事件。特点：当你有多个Subscriber的时候，他们的事件是独立的

​      常见的工厂方法都是Cold  Observable，例如`create`、`defer`、`fromXX`等

### 7、transformer  

ObservableTransformer,FlowableTransformer,SingleTransformer,MaybeTransformer,CompletableTransformer。

## 4、RxJava Observable 操作符

### 1、创建型

一般在最上游，静态方法

| 操作符                                       | 说明                                                         |
| -------------------------------------------- | ------------------------------------------------------------ |
| create(emitter -> {})                        | 通过emitter来发射事件，从而创建一个Observable<br />需要自己实现onNext()、onError()、onComplete()的逻辑 |
| just(c)                                      | 跟进给定的常量或表达式创建Observable<br />c如果是表达式，那在订阅前已经完成，成为常量，再次订阅也不会发生改变 |
| defer(callable)                              | 比just灵活，订阅的时候才会创建Observable，并且为每个订阅者创建自己的Obserable<br />callable一般为静态回调 |
| fromCallable(callable)                       | 通过Callable创建Observable<br />与defer略有不同，会更注重Observable的异步的情况 |
| fromFuture(future)                           | 拥有更多异步可选项，并且可以设置调度器和超时时间             |
| fromArray(array)<br />fromIterable(iterable) | 拆分数组、迭代器，依次发射事件                               |
| repeat(int)                                  | 重复创建n个int数据的Observable                               |
| range()                                      | 创建数字区间数据的Observable                                 |
| rangelong()                                  | range的唯一区别是数据类型是long                              |
| interval()                                   | 创建周期发射的Observable，默认调度器为computation            |
| intervaRangel()                              | 每隔指定时间就发送 事件，可指定事件序列起点，可指定发送数据的数量 |
| timer()                                      | 创建延时发射的Observable，默认调度器为computation            |

* just

  快速创建多个观察者对象，最多只能传入十个对象，如果有表达式，会在传入事件之前产生。

  ```java
  Observable.just(1,2,3,4)
                 .subscribe(rst->{
                     Log.d(TAG, "testRxjava: "+ rst);
                 });
  ```

* fromArray 

  会将数组进行拆分，依次发射事件，但是若是list集合，则会一次性发射，可以传入十个对象以上

  ```java
   //数组必须使用引用对象
  Integer[] str = {1, 2};
          Observable.fromArray(str)
                  .subscribe(rst->{
                      Log.d(TAG, "testRxjava: "+ rst);
                  });
  ```

* fromIterable

  发送事件的特点：直接发送 传入的集合`List`数据,会进行list集合的遍历，可以传入十个对象以上

  ```java
   //参数必须是list 或 iterator
  ArrayList<String> str1 = new ArrayList<String>();
          str1.add("zhangsan");
          str1.add("lisi");
          Observable.fromIterable(str1)
                  .subscribe(rst->{
                      Log.d(TAG, "testRxjava: "+ rst);
                  });
  ```

  

* defer()

  延迟创建，直到有观察者（`Observer` ）订阅时，才动态创建被观察者对象（`Observable`） & 发送事件

  适用于以下场景：动态创建被观察者对象（`Observable`） & 获取最新的`Observable`对象数据

  ```java
         <-- 1. 第1次对i赋值 ->>
          Integer i = 10;
  
          // 2. 通过defer 定义被观察者对象
          // 注：此时被观察者对象还没创建
          Observable<Integer> observable = Observable.defer(new Callable<ObservableSource<? extends Integer>>() {
              @Override
              public ObservableSource<? extends Integer> call() throws Exception {
                  return Observable.just(i);
              }
          });
  
          <-- 2. 第2次对i赋值 ->>
          i = 15;
  
          <-- 3. 观察者开始订阅 ->>
          // 注：此时，才会调用defer（）创建被观察者对象（Observable）
          observable.subscribe(new Observer<Integer>() {
  
              @Override
              public void onSubscribe(Disposable d) {
                  Log.d(TAG, "开始采用subscribe连接");
              }
  
              @Override
              public void onNext(Integer value) {
                  Log.d(TAG, "接收到的整数是"+ value  );
              }
  
              @Override
              public void onError(Throwable e) {
                  Log.d(TAG, "对Error事件作出响应");
              }
  
              @Override
              public void onComplete() {
                  Log.d(TAG, "对Complete事件作出响应");
              }
          });
  ```

* timer()

  延迟指定时间后，发送1个数值0（`Long`类型）,相当于延迟指定时间后，调用一次 `onNext(0)`

  ```java
  Observable.timer(2, TimeUnit.SECONDS) 
                    .subscribe(rst-> {});
  ```

* Interval()

  每隔指定时间就发送 事件，从0开始、无限递增1的整数序列

  ```java
    // 参数说明：
          // 参数1 = 第1次延迟时间；
          // 参数2 = 间隔时间数字；
          // 参数3 = 时间单位
  Observable.interval(3,1,TimeUnit.SECONDS)
                  // 延迟3s后发送事件，每隔1秒产生1个数字（从0开始递增1，无限个）
                  .subscribe(rst->{})
  ```

* IntervalRange()

  每隔指定时间就发送 事件，可指定事件序列起点，可指定发送数据的数量

  ```java
  // 参数1 = 事件序列起始点；
          // 参数2 = 事件数量；
          // 参数3 = 第1次事件延迟发送时间；
          // 参数4 = 间隔时间数字；
          // 参数5 = 时间单位
          Observable.intervalRange(3,10,2, 1, TimeUnit.SECONDS)
            // 延迟2s后发送10个事件，每隔1秒产生1个数字（从3开始递增1）
            .subcribe(rst->{});
  ```

* range()

  发送事件的特点：连续发送m个事件序列，可指定事件序列的范围,[n,n+m-1];

  ```java
  // 参数说明：
          // 参数1 = 事件序列起始点；
          // 参数2 = 事件数量；
          // 注：若设置为负数，则会抛出异常
          Observable.range(3,10)
            .subcribe(rst->{});
  ```

*  rangeLong（） 

  同range() 只是类型是long

  以下适用于测试时使用

```java
/ 下列方法一般用于测试使用

<-- empty()  -->
// 该方法创建的被观察者对象发送事件的特点：仅发送Complete事件，直接通知完成
Observable observable1=Observable.empty(); 
// 即观察者接收后会直接调用onCompleted（）

<-- error()  -->
// 该方法创建的被观察者对象发送事件的特点：仅发送Error事件，直接通知异常
// 可自定义异常
Observable observable2=Observable.error(new RuntimeException())
// 即观察者接收后会直接调用onError（）

<-- never()  -->
// 该方法创建的被观察者对象发送事件的特点：不发送任何事件
Observable observable3=Observable.never();
// 即观察者接收后什么都不调用
```

### 2、变换操作符

进行数据转换，有输入，有输出

| 操作符                         | 说明                                                         |
| ------------------------------ | ------------------------------------------------------------ |
| map(a -> b）                   | a转成b                                                       |
| flatMap(a -> observable）      | a转成observable                                              |
| concatMap(a -> observable）    | flatMap不能保证输出顺序和输入顺序一致，concatMap则是保证顺序的 |
| switchMap(a -> observable)     | 当源Observable发射一个新的数据项时，如果旧数据项订阅还未完成，就取消旧订阅数据和停止监视那个数据项产生的Observable，开始监视新的数据项。如果源Observable和新Observable都是在同一个线程里跑的话，那么该操作符与ContactMap无异；只有源Observable和新Observable在不同的线程里跑的时候，即新Observable线程方案为newThread的时候，才会出现这种情况。 |
| debounce                       | 对Observable每产生一个结果后，如果在规定的间隔时间内没有别的结果产生，则把这个结果提交给订阅者处理，否则忽略该结果。 |
| flatMapIterable(a -> iterable) | 作者：代码改变人生 链接：https://www.jianshu.com/p/9ddbb4144a4d 来源：简书 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。a转为iterable，依次发射 |
| scan((a, b) -> f(a, b)         | f(a, b)的结果传递给下游，同时还会赋值给a，一直累计           |
| compose                        | **对 Observable 进行整体的变换**，compose能够从数据流中得到原始的被观察者，当创建被观察者时，compose 操作符会立即执行，而不像其他的操作符需要在 onNext() 调用后才能执行 |

* map()

```
Observable.just("images/logo.png") // 输入类型 String
    .map(new Func1<String, Bitmap>() {
        @Override
        public Bitmap call(String filePath) { // 参数类型 String
            return getBitmapFromPath(filePath); // 返回类型 Bitmap
        }
    })
    .subscribe(new Action1<Bitmap>() {
        @Override
        public void call(Bitmap bitmap) { // 参数类型 Bitmap
            showBitmap(bitmap);
        }
    });
```

`map()` 方法将参数中的 `String` 对象转换成一个 `Bitmap` 对象后返回，而在经过 `map()` 方法后，事件的参数类型也由 `String` 转为了 `Bitmap`。这种直接变换对象并返回的，是最常见的也最容易理解的变换。

map()的变换示意图：

![944365-a9c0b5eb2cc573d6](file:///Users/zhangqian20/git%20profile/ZqAndroid/Androidbase/RxJAVA%20%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86.assets/944365-a9c0b5eb2cc573d6.webp?lastModify=1648806195)

* flatmap():

示例：一个学生有多门课程，打印出所有学生拥有的课程。

```
Student[] students = ...;
Subscriber<Course> subscriber = new Subscriber<Course>() {
    @Override
    public void onNext(Course course) {
        Log.d(tag, course.getName());
    }
    ...
};
Observable.from(students)
    .flatMap(new Func1<Student, Observable<Course>>() {
        @Override
        public Observable<Course> call(Student student) {
            return Observable.from(student.getCourses());
        }
    })
    .subscribe(subscriber);
```

 `flatMap()` 和 `map()` 有一个相同点：它也是把传入的参数转化之后返回另一个对象。但需要注意，和 `map()` 不同的是， `flatMap()` 中返回的是个 `Observable` 对象，并且这个 `Observable` 对象并不是被直接发送到了 `Subscriber` 的回调方法中。 `flatMap()` 的原理是这样的：

1. 使用传入的事件对象创建一个 `Observable` 对象；
2. 并不发送这个 `Observable`, 而是将它激活，于是它开始发送事件；
3. 每一个创建出来的 `Observable` 发送的事件，都被汇入同一个 `Observable` ，而这个 `Observable` 负责将这些事件统一交给 `Subscriber` 的回调方法。

这三个步骤，把事件拆成了两级，通过一组新创建的 `Observable` 将初始的对象『铺平』之后通过统一路径分发了下去。而这个『铺平』就是 `flatMap()` 所谓的 flat。

![flatMap() 示意图](RxJAVA 基础知识.assets/2d1d371d9d2e49dc8da75c15a105c809.jpeg)

* concatMap()

  类似FloatMap,区别在于**拆分 & 重新合并生成的事件序列 的顺序 = 被观察者旧序列生产的顺序**，新生成的Observable必须实现onComplete()方法，因为在onComplete()之后,才会进行新的事件发射。

  ```java
     Observable.range(1,2)
                  .concatMap(new Function<Integer, Observable<String>>() {
                      @Override
                      public Observable<String> apply(@NonNull Integer integer) throws Exception {
  
                          return Observable.create(new ObservableOnSubscribe<String>() {
  
                              @Override
                              public void subscribe(@NonNull ObservableEmitter<String> e) {
                                  try {
                                      testRxjava test = NetEngineFactory.CreateNetEngine(OkHttpNetEngine.class, RxjavaMainActivity.this).testFloatMap();
  //                                    Log.d(TAG, "subscribe: "+ test.getErrno());
                                      e.onNext(test.getErrno());
                                      e.onComplete();
                                  }catch (Exception error){
                                      e.onError(error);
                                  }
                              }
                          });
                      }
                  })
                  .subscribe(rst -> {
          }, error -> {
          });
  ```

* switchMap()

  New Observable 的Schedulers为newThread()时，以下的例子中只会订阅响应最后一个。

  ```java
   Observable.range(1,5)
                  .switchMap(new Function<Integer, Observable<Integer>>() {
                      @Override
                      public Observable<Integer> apply(@NonNull Integer integer) throws Exception {
  
                          return Observable.create(new ObservableOnSubscribe<Integer>() {
  
                              @Override
                              public void subscribe(@NonNull ObservableEmitter<Integer> e) {
                                  try {
  //                                    testRxjava test = NetEngineFactory.CreateNetEngine(OkHttpNetEngine.class, RxjavaMainActivity.this).testFloatMap();
  ////                                    Log.d(TAG, "subscribe: "+ test.getErrno());
                                      Log.d(TAG, "subscribe: "+ Thread.currentThread().getName());
                                      e.onNext(integer);
                                      e.onComplete();
                                  }catch (Exception error){
                                      e.onError(error);
                                  }
                              }
                          }).subscribeOn(Schedulers.newThread())
                      }
                  })
  
                  .subscribe(rst -> {
                      Log.d(TAG, "testRxjava: "+rst);
          }, error -> {
          });
  ```

* debounce 抖动 筛选

  ```java
  Observable.create(new ObservableOnSubscribe<Integer>() {
              @Override
              public void subscribe(ObservableEmitter<Integer> emitter) throws Exception {
                  // send events with simulated time wait
                  emitter.onNext(1); // skip
                  Thread.sleep(400);
                  emitter.onNext(2); // deliver
                  Thread.sleep(505);
                  emitter.onNext(3); // skip
                  Thread.sleep(100);
                  emitter.onNext(4); // deliver
                  Thread.sleep(605);
                  emitter.onNext(5); // deliver
                  Thread.sleep(510);
                  emitter.onComplete();
              }
          })
                // 设置时间为0.5秒
              .debounce(500, TimeUnit.MILLISECONDS)
               // Run on a background thread
               .subscribeOn(Schedulers.io())
               // Be notified on the main thread
               .observeOn(AndroidSchedulers.mainThread())
                .subscribe(getObserver());
  //运行结果 2 4 5
  ```

* compose

  * 进行线程切换

    compose 操作符可以和 Transformer 结合使用,提高代码的复用性，同时不影响Rxjava的链式调用的简洁

    ```java
    public class ComSchedulerTransformer<T> implements ObservableTransformer<T,T> {
    
        private LifecycleOwner owner;
    
        public ComSchedulerTransformer(LifecycleOwner owner) {
            this.owner = owner;
        }
    
    
        @NonNull
        @Override
        public ObservableSource<T> apply(@NonNull Observable<T> upstream) {
            return upstream.subscribeOn(Schedulers.io())
                    .observeOn(AndroidSchedulers.mainThread());
        }
    
    }
    //使用时
    .compose(new ComSchedulerTransformer<>())
    ```

    RxLifecycle 是 trello 开源的一个配置 RxJava 使用的开源库，我们知道 RxJava 有个缺点就是会导致内存泄露，此时，RxLifecycle 横空出世了，它可以配合 RxJava 一起使用，可以有效防止内存泄漏发生。

### 3、条件/布尔操作符

### 3、功能型

传入参数即可，工具类型操作符

| 操作符                 | 说明                                               |
| ---------------------- | -------------------------------------------------- |
| buffer(int)            | 每集齐int个元素，作为一个集合发射到下游            |
| buffer(int, timeUnit)  | 每收集一段时间，收集到的元素作为一个集合发射到下游 |
| window(int, timeUnit)  | 与buffer类似，但会创建一个新的Observable           |
| cast(clazz)            | 类型强转                                           |
| timeout(int, timeUnit) | 超时抛异常或发射默认Observable                     |
| startWith(c)           | 在事件源之前先发射一个事件，经常配合timer使用      |

### 4、过滤型

跟进条件过滤输出

| 操作符                  | 说明                                                         |
| ----------------------- | ------------------------------------------------------------ |
| filter(a -> boolean)    | 每集齐int个元素，作为一个集合发射到下游                      |
| take(int)               | 取int项向下游发射                                            |
| takeuntil               |                                                              |
| skip(int)               | 忽略int项后，再向下游发射                                    |
| elementAt(int)          | 发射第int项                                                  |
| debounce(int, timeUnit) | 防抖，一段时间内无新事件传入才会把该事件向下游发出，否则遗弃 |
| throttle(int, timeUnit) | 采样，一段时间内，只向下游发射第一个或最后一个接收到的事件   |
| distinct()              | 过滤重复元素                                                 |
| first() / last()        | 只发送指定项或符合条件的指定项                               |

### 5、组合型

组合多个Observable

| 操作符                           | 说明                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| merge(o1, o2)                    | 组合2个Observable，各自按照自己的顺序事件发射元素            |
| concat(o1, o2)                   | 组合2个Observable，先发送的Observable发射完全部元素complete后才到另一个发射 |
| amb((o1, o2))                    | 先发射事件的Observable作为真正的Observable，其他的直接抛弃   |
| zip((o1, o2) -> f(x, y))         | 组合2个Observable，两个Observable的元素一一对应的接收到才会组合 f(x, y)的结果向下游发射 |
| combineLast((o1, o2) -> f(x, y)) | 组合2个Observable，不要求两个Observable的元素一一对应，接到任何新元素都会组合新的元素向下游发射，组合方法f(x, y)的x，y分别是连个Observable最新发出来的元素 |

### 6、重复与重试

| 操作符                                   | 说明                                |
| ---------------------------------------- | ----------------------------------- |
| repeat()                                 | complete后自动重新订阅              |
| repeatWhen(predict)                      | 满足条件时，complete则自动重新订阅  |
| retry()                                  | 抛异常则重试，只对其上游的error有效 |
| retryWhen(errorObservable -> observable) | 抛异常时，满足条件重新订阅          |

### 7、异步转同步、阻塞

| 操作符                    | 说明                                                       |
| ------------------------- | ---------------------------------------------------------- |
| blockingFirst()           | 阻塞并返回第一个收到的元素                                 |
| blockingForEach(a - f(a)) | 阻塞每一个元素，直到complete。收到每个元素都调用回调去处理 |

## 5、背压策略

一种 **控制事件流速** 的策略

* 解决的问题

  在异步订阅中，被观察者发送事件速度 与 观察者接收事件速度 **不匹配**（一般是前者 快于 后者），从而导致观察者无法及时响应 / 处理所有 被观察者发送的事件，从而造成**导致缓存区溢出、事件丢失 & OOM**。

* 作用

  在 **异步订阅关系** 中，**控制事件发送 & 接收的速度**

* 应用场景

  被观察者发送事件速度 与 观察者接收事件速度 **不匹配**的场景

  具体场景就取决于 该事件的类型，如：网络请求，那么具体场景：有很多网络请求需要执行，但执行者的执行速度没那么快，此时就需要使用背压策略来进行控制。



## 6、实例应用

1、优先加载缓存，再加载网络请求
