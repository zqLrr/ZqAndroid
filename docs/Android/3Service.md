# Service

> Android的四大组件组件，分为本地服务和远端服务，是一种机制，运行在main线程上。

## StartService

* Service.startService(Intent intent) 通过intent指定Service

  会调用Service的onCreate()和onStartCommend()

* Service.stopService(Intent intent) 通过intent指定Service

  会调用Service的onDestory()

## BindService

* Service.bindService(Intent,ServiceConnection,flags)

  * intent  指定Service
  * 用于监听访问者与Service之间的连接情况，正常解绑不会通知
    * onServiceConnected     通知连接
    * onServiceDisconnected  通知断开
  * flags
    * 0   不自动创建
    * BIND_AUTO_CREATE  自动创建

  会调用onCreate()  onBind()

* Service.unbindService(ServiceConnection)

  会调用unBind()、onDestory()

* Service类提供IBinder onBinder()提供Binder对象，在ServiceConnection中获取Binder，访问者通过Binder和Service进行通信。

## 生命周期

![img](/Users/zhangqian20/git profile/ZqAndroid/docs/png/webp.png)

## IntentService

定义：由于Service不会专门启动一个单独的线程，依赖于main线程，因此不应该在Service中直接处理耗时的任务。

特征：

* IntentService会创建单独的worker线程来处理所有的Intent请求。
* IntentService会创建单独的worder线程来处理onHandleIntent()方法实现的代码。
* IntentService会将Intent请求添加到队列中，worker线程依次处理。
* 当所有请求处理完成后，IntentService会自动停止，无需开发者调用方法来停止Service。
* 为Service的onBind()、onStartComment()提供了默认的实现，无需重写。

## AIDL+Service

* AIDL中可以定义接口，在IAppServiceRemoteBinder.Stub()类中进行实现。

* 在onServiceConnected()中进行对Service中数据和操作进行绑定

  ```java
   public void onServiceConnected(ComponentName name, IBinder service) {
              System.out.println("Connected Success");
              System.out.println(service);
              //获取远程Servise的onBind()所返回对象的代理
              IBinder = IAppServiceRemoteBinder.Stub.asInterface(service);//虽然类名相同，但不是同一个类，因此需要这样转换
          }
  ```

* 通过AIDL定义的接口

  ```java
  private IAppServiceRemoteBinder IBinder = null;
  ```

  即可实现在服务与访问者之间进行通信。

本质上是通过Service连接不同的进程，通过AIDL进行进程间通信

## Messenger + Service

> 使用同AIDL Service

## 启动另一个进程中的Service

必须保证另一个App是启动的。

* 打开另一个App源码

```java
 Intent it =  getBaseContext().getPackageManager().getLaunchIntentForPackage("另一个App包名");
        if(it !=null) {
            it.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
            getBaseContext().startActivity(it);
        }
```

* 打开另一App中服务的源码（显式启动）

  * 通过包名和全路径名

  ```java
  Intent i  = new Intent();
  i.setClassName("com.example.firstservice","com.example.firstservice.MyService");
  startService(i);
  ```

  设置service的Android:exported="true";

  * 通过ComponentName类

* 启动、停止、绑定和解绑的方式一样，通过Intent显式调用另一个app的Service

### 