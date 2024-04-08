# 15 Android 常用业务功能

## 1.AlarManager

> AlarmManager 可以按照”预先设定的时间“或”时间间隔"激活Intent的一种方式，他与timer不同的是，他是程序之外的，生命周期不受程序控制，即使程序关闭也会触发。它比Service和定时器更加节省资源。如果在应用程序内触发定时操作，推荐Handler，Timer以及Thread等方式。因为这样Android能更好的控制系统资源。

* Alerm的四个可用类型（set()方法的第一个参数）

  （1）RTC_WAKEUP 在指定的时间唤醒设备，并激活PendingIntent

  （2）RTC 在指定的时间激活PendingIntent，但是不会唤醒设备

  （3） ELAPSED_REALTIME 根据设备启动之后经过的时间激活PendingIntent，但是不会唤醒设备，经过的时间包含设备休眠的所有时间

  （4）ELAPSED_REALTIME_WAKEUP 在设备启动并经过指定的时间之后激活PendingIntent和唤醒设备。

* 设置重复的Alarm

  * setRepeating  可以精确控制时间，比较耗电

  * setIndexactRepeating 可以减少电量的消耗，但是会同步多个没有精确指定时间间隔的Alerm.

    INTERVAL_FIFTEEN_MINUTES（每十五分钟）

    INTERVAL_HALF_HOUR（每隔半个小时）

    INTERVAL_HOUR（每隔一个小时）

    INTEVAL_HALF_DAY（每个半天）


  ​        INTEVAL_DAY（每隔一天）

*  set (int type, 
                  long triggerAtMillis, 
                  PendingIntent operation)

  Android 19之后，`triggerAtMillis`是不精确的，不会提前但可能会延迟，为了降低系统的消耗

  

代码示例：1分钟后发送广播ACTION_GET_PUSH_VALUE

```java
AlarmManager alarmMng = (AlarmManager) context
					.getSystemService(Context.ALARM_SERVICE);
			int flags = SDKAdaptationUtils.getPendingIntent31Flags(1);//Flags适配Android版本
			PendingIntent rtcIntent = PendingIntent.getBroadcast(context, 0,
					new Intent(ACTION_GET_PUSH_VALUE), flags);
			Date t = new Date();
			t.setTime(java.lang.System.currentTimeMillis() + 60 * 1000);
			alarmMng.set(AlarmManager.RTC, t.getTime(), rtcIntent);
//取消该任务
alarmMng.cancle();
```

## 2.常用的Action 

1. `Intent.ACTION_BOOT_COMPLETED`,开机启动广播

   ```xml
   需要权限
   <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
   //自定义的广播添加
   <intent-filter>
     <action android:name="android.intent.action.BOOT_COMPLETED" />
   </intent-filter>
   ```

  注意：

> 1.需要系统开启自启动开关，当然这个开关不是说app可以自启动了，只是可以接收开机启动广播了.
>
> 2.Android 8 之后说静态注册的广播不能接收系统广播，因为Android 8广播只能发送显式广播,经测试，仍然可以接收系统广播
>
> 3.开机广播需要开机一段时间后才能收到。	

## 3.Android 定位的原理

> 移动端大致通过三种方式来进行设备定位：GPS、基站、wifi。

Android中我们一般使用`LocationManager`来获取配置信息，`Provider`有：

```java
public static final String NETWORK_PROVIDER = "network";
public static final String GPS_PROVIDER = "gps";
public static final String PASSIVE_PROVIDER = "passive";
public static final String FUSED_PROVIDER = "fused";
```

| 类型             | 作用                                                   | 优缺点                                                       |
| ---------------- | ------------------------------------------------------ | ------------------------------------------------------------ |
| GPS_PROVIDER     | 通过 GPS 来获取地理位置的经纬度信息                    | 优点：获取地理位置信息精确度高； 缺点：只能在户外使用，获取经纬度信息耗时，耗电； |
| NETWORK_PROVIDER | 通过移动网络的基站或者 Wi-Fi 来获取地理位置            | 优点：只要有网络，就可以快速定位，室内室外都可； 缺点：精确度不高； |
| PASSIVE_PROVIDER | 被动接收更新地理位置信息，而不用自己请求地理位置信息。 | PASSIVE_PROVIDER 返回的位置是通过其他 providers 产生的       |
| FUSED_PROVIDER   | 依赖GMS                                                | 暂时在国内无法使用,目前在Android12（即android api 31）上又重新使用了起来 |

打开系统定位，并允许app使用定位权限，则会获取以上Provider，关闭定位，则一个都不会获取到，低版本的能获取到PASSIVE_PROVIDER。

## 位置信息使用权的类型

```xml
<manifest ... >
  <-- 前台位置信息-->
  <--精确定位-->
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
  <--模糊定位-->
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
 <-- 后台位置信息-->
  <!-- Required only when requesting background location access on
       Android 10 (API level 29) and higher. -->
  <uses-permission android:name="android.permission.ACCESS_BACKGROUND_LOCATION" />
</manifest>
```

1.在API Level 29以下请求位置信息，请求前台位置信息同时会获取后台位置信息，在29以上，需要请求后台位置信息。

2.精确度

* 模糊定位，在3平米以内

* 精确定位，在50米以内 

  精确定位的请求同时会返回模糊定位的权限，但Android 建议递增式请求位置信息。

3.权限组

```java
public static final String LOCATION = android.permission-group.LOCATION
```

权限组的概念是为了更好的使用权限，而不需要每次去请求。

1.ACCESS_BACKGROUND_LOCATION——后台定位权限使用说明

* targetSdkVersion<29时,即Android 10 以下，不区分前台和后台定位权限,申请前台位置权限，默认给予后台位置权限。
* targetSdkVersion>=29时,即Android 10及以上，至少需要有一个前台权限同意，才能申请后台定位，若两个没有，则不会展示权限申请框，默认拒绝。
* targetSdkVersion>=29时，当前台和后台权限同时申请时，选择“使用时允许”，后台定位申请会被拒绝。targetSdkVersion>=30时,即Android 11及以上，取消了“始终允许”，则后台一定会被拒绝。因此先选前台，再选后台。

2.ACCESS_COARSE_LOCATION&& ACCESS_FINE_LOCATION

Android 12，申请ACCESS_COARSE_LOCATION不会默认有ACCESS_FINE_LOCATION权限，但是申请ACCESS_FINE_LOCATION回默认有ACCESS_COARSE_LOCATION权限。



## GPS 定位

精确定位打开的情况，不会再请求打开模糊定位的权限，模糊定位的权限打开，需要精确定位的时候仍然需要申请权限打开。

需要打开App Gps 定位，使用LocationManager 定位,会在Listener中收到Location,

```java
LocationManager.requestLocationUpdates()
```

## 网络定位

需要权限

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
```

同Gps定位，需要获取NETWORK_PROVIDER

> 原理：Android 提供的位置服务，利用位置服务器进行定位

## 基站定位

通过TelephonyManager我们可以拿到基站信息，再通过相关的api接口就能得到经纬度，但是基站定位精度很差。

基站信息包含如下：

> MCC，Mobile Country Code，移动国家代码（中国的为460）；
> MNC，Mobile Network Code，移动网络号码（中国移动为00，中国联通为01）；
> LAC，Location Area Code，位置区域码；
> CID，Cell Identity，基站编号，是个16位的数据（范围是0到65535）。
>
> 以下是CDMA独有的信息
>
> SID, 无线系统ID
>
> NID,Network id
>
> BID, Basestation ID

需要区分GSM和CDMA，IMSL：中国联通GSM系统使用01，中国移动GSM系统使用02，中国电信CDMA系统使用03。

获取到以上信息，通过一些公开的Api信息获取到经纬度。

### 基站定位信息获取

> 基站信息的获取需要开启定位服务和精确位置权限，否则无法获取基站信息。
>
> 低版本不需要开启服务。

需要权限

```java
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/> dangours
```

获取卡1基站信息 

* TelephonyManager#getCellLocation();

  获取当前手机的基站位置，需要精确位置的权限。

  ```java
  TelephonyManager mTelephonyManager = context.getSystemService(Context.TELEPHONY_SERVICE);
  CellLocation cellLocation = mTelephonyManager.getCellLocation();
  ```

* TelephonyManager#getAllCellInfo();

  获取当前设备的所有基站信息，需要精确位置的权限。

* TelephonyManagerEx#getCellLocation()

  通过反射获取当前设备基站的信息，可以选择卡1/卡2，需要精确位置的权限。

  ```java
    Class<?> cls;
              try {
                 cls = Class.forName("com.mediatek.telephony.TelephonyManagerEx");
                 Method method1 = cls.getDeclaredMethod("getCellLocation", int.class);
                 Method method2 = cls.getDeclaredMethod("getDefault");
                 Object obj = (Object)method2.invoke(null);
                 cellLocation = (CellLocation)method1.invoke(obj, 1);  //0表示卡1，1表示卡2
              } catch (ClassNotFoundException e) {
                  e.printStackTrace();
              } catch (IllegalArgumentException e) {
                  e.printStackTrace();
              } catch (NoSuchMethodException e) {
                  e.printStackTrace();
              } catch (IllegalAccessException e) {
                  e.printStackTrace();
              } catch (InvocationTargetException e) {
                  e.printStackTrace();
              }  catch (Exception e) {
  				e.printStackTrace();
  			}
  ```

* ITelephony#getCellLocation()

  获取当前设备的基站信息，主要针对于双卡双待手机

  ```
  try {
  bundle = getITelephonyBundle(mTelephonyManager);
  } catch (Exception e) {
  e.printStackTrace();
  }
  if (bundle != null) {
  backupCell = new GsmCellLocation(bundle);
  }
  private Bundle getITelephonyBundle(TelephonyManager telMgr)throws Exception {
  @SuppressLint("SoonBlockedPrivateApi")
  Method getITelephony = telMgr.getClass().getDeclaredMethod("getITelephony");
  getITelephony.setAccessible(true);
  Object iTelephony = getITelephony.invoke(telMgr);
  Method getCellLocation = iTelephony.getClass().getDeclaredMethod("getCellLocation");
  Object result = getCellLocation.invoke(iTelephony);
  return (Bundle) result;
  }
  ```

  

## Wifi定位

wifi定位是通过WifiManager拿到wifi的信息，主要是wifi的BSSID（即mac地址）。然后通过一些api查询经纬度

比如：[接口说明文档 | LBS数据仓库](http://www.cellocation.com/interfac/)

### Wifi的相关概念

* SAT

> Station(站点)，是指Android访问无线网络的节点

* AP

> AccessPoint(接入点)，分为硬AP和软AP。
>
> 硬AP是指拥有独立的CPU，能独立进行数据的相关处理，是一个独立完全的模块，常见的为无线路由器。
>
> 软AP是指使用驱动配置称为AP模式，实现接入点的功能，包括通过 Wi-Fi 热点和仅限本地使用的 Wi-Fi 热点共享网络。

* wifi感知对设备

> 通过 Android 8.0 中新增的 [Wi-Fi 感知](https://developer.android.google.cn/guide/topics/connectivity/wifi-aware.html)功能，支持设备可以直接使用 Wi-Fi 感知协议发现其他设备、与其他设备互连，以及将覆盖范围扩展到其他设备。

* WIFI直联

> [WLAN 直连](https://developer.android.google.cn/guide/topics/connectivity/wifip2p.html)功能又称“Wi-Fi 点对点”，它允许支持设备直接使用 WLAN 直连协议发现其他设备并与之互连，无需连接到互联网或移动网络，即在不使用移动网络的情况下，使用wifi在设备之间进行数据通信。

现在已经出现了SAT/SAT并发、SAT/AP并发和AP/AP并发，允许设备同时连接两个Wifi，实现了wifi切换时的平滑过渡，因此Android 12及以上推出了新的API 来获取wifi信息。

因此不使用 `WifiManager.getConnectionInfo()`，而改用 [`NetworkCallback.onCapabilitiesChanged()`](https://developer.android.google.cn/reference/android/net/ConnectivityManager.NetworkCallback#onCapabilitiesChanged(android.net.Network, android.net.NetworkCapabilities))，以获取与用于注册 `NetworkCallback` 的 `NetworkRequest` 匹配的所有 `WifiInfo` 对象。

### wifi信息的获取和使用

需要权限

```java
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/> normal
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/> dangours
```

> 需要打开wifi和打开定位服务，才能获取到连接的wifi信息，需要精确定位权限才能获取接入点列表。低版本Android 不需要定位服务
>
> 需要打开wifi,连不连上无所谓，在google的服务器或者其他服务器中存有你附近的wifi信息，则可以定位

* 获取连接的wifi信息

  * WifiManager#getConnectionInfo()

  ```java
  //Android 12 之前
  WifiManager wifiManager = (WifiManager) context.getSystemService(Context.WIFI_SERVICE);
  android.net.wifi.WifiInfo wifiinfo =  wifiManager.getConnectionInfo();
  ```

  * NetworkCallback.onCapabilitiesChanged()

  ```java
  ```

* 获取最近一次扫描的接入点列表

  WifiManager#getScanResults() 需要精确定位的权限

  ```java
  WifiManager wifiManager = (WifiManager) context.getSystemService(Context.WIFI_SERVICE);
  List<ScanResult> oriScanResults  = wifiManager.getScanResults();
  ```

  

## 混合定位

基于临近基站和WIFI热点的高精度混合基站定位，比单一的基站定位和wifi定位更加精确一点。

方案：

1.通过WifiManager的getScanResults函数获取扫描到的wifi列表，其中level就是信号强度，可能需要去重/

2.通过TelephonyManager的getAllCe.llInfo函数获得。这个函数要求minsdkverison必须在17及以上,其中mRssi就是信号强度。

## AGPS定位

这个与其说是一个定位方式，不如说是一种实现方案，即通过网络位置和位置服务器判断出最佳的卫星，减少获取卫星信号的时间，因为获取网络位置的速度比较快。这个是Android自带的，但是好像默认就是这种模式。

## Android Moke位置

只针对于Provider,即网络定位和Gps定位

* 权限

  ```
  <uses-permission android:name="android.permission.ACCESS_MOCK_LOCATION"
  tools:ignore="MockLocation"/>
  ```

```java
//code

```

## Android 沉浸式(全屏功能实现)

## Android AsyncInflater

> 在异步线程中加载xml成View,然后回到主线程直接加载View。

使用用法：

```java
  // 异步加载 xml, 在 Activity.onCreate(xxx) 里面
        new AsyncLayoutInflater(this).inflate(R.layout.activity_main, null, new AsyncLayoutInflater.OnInflateFinishedListener() {
            @Override
            public void onInflateFinished(@NonNull View view, int resid, @Nullable ViewGroup parent) {
                setContentView(view);
                Log.d(TAG, "onInflateFinished: ");
            }
        });

```

原理：

1.new AsyncLayoutInflater(Context)的操作

```java
 public AsyncLayoutInflater(@NonNull Context context) {
        mInflater = new BasicInflater(context);
        mHandler = new Handler(mHandlerCallback);
        mInflateThread = InflateThread.getInstance();
    }
```

mInflater:用来渲染xml文件为view

mHandler:新建了一个Looper为主线程的Handler

mInflateThread： 新建了一个线程，InflateThread改线程为一个单例。

2.AsyncLayoutInflater.inflate()

```java
  @UiThread
    public void inflate(@LayoutRes int resid, @Nullable ViewGroup parent,
            @NonNull OnInflateFinishedListener callback) {
        if (callback == null) {
            throw new NullPointerException("callback argument may not be null!");
        }
        InflateRequest request = mInflateThread.obtainRequest();
        request.inflater = this;
        request.resid = resid;
        request.parent = parent;
        request.callback = callback;
        mInflateThread.enqueue(request);
    }
```

新建了InflateRequest，封装了一个InflateRequest请求，包括持有AsyncLayoutInflater的引用inflater，线程绘制view的resid，回到主线程加载view的callback，然后进入到了InflateThread的请求队列中。

3.InflateThread绘制完后，通过handler返回到主线程

```java
  private static class InflateThread extends Thread {
    // 单例 
        private static final InflateThread sInstance;
        static {
            sInstance = new InflateThread();
            sInstance.start();
        }

        public static InflateThread getInstance() {
            return sInstance;
        }

       //阻塞队列
        private ArrayBlockingQueue<InflateRequest> mQueue = new ArrayBlockingQueue<>(10);
       //同步的请求缓存池
        private SynchronizedPool<InflateRequest> mRequestPool = new SynchronizedPool<>(10);

        // Extracted to its own method to ensure locals have a constrained liveness
        // scope by the GC. This is needed to avoid keeping previous request references
        // alive for an indeterminate amount of time, see b/33158143 for details
       //这是重点，从阻塞队列中取到请求，
        public void runInner() {
            InflateRequest request;
            try {
              //1.从阻塞队列中取到请求
                request = mQueue.take();
            } catch (InterruptedException ex) {
                // Odd, just continue
                Log.w(TAG, ex);
                return;
            }

            try {
              //2.绘制成View
                request.view = request.inflater.mInflater.inflate(
                        request.resid, request.parent, false);
            } catch (RuntimeException ex) {
                // Probably a Looper failure, retry on the UI thread
                Log.w(TAG, "Failed to inflate resource in the background! Retrying on the UI"
                        + " thread", ex);
            }
          //3.将view返回给主线程
            Message.obtain(request.inflater.mHandler, 0, request)
                    .sendToTarget();
        }

        @Override
        public void run() {
            while (true) {
                runInner();
            }
        }

    //从request中获取请求
        public InflateRequest obtainRequest() {
            InflateRequest obj = mRequestPool.acquire();
            if (obj == null) {
                obj = new InflateRequest();
            }
            return obj;
        }

        public void releaseRequest(InflateRequest obj) {
            obj.callback = null;
            obj.inflater = null;
            obj.parent = null;
            obj.resid = 0;
            obj.view = null;
            mRequestPool.release(obj);
        }

        public void enqueue(InflateRequest request) {
            try {
                mQueue.put(request);
            } catch (InterruptedException e) {
                throw new RuntimeException(
                        "Failed to enqueue async inflate request", e);
            }
        }
    }

//主线程中
  private Callback mHandlerCallback = new Callback() {
        @Override
        public boolean handleMessage(Message msg) {
            InflateRequest request = (InflateRequest) msg.obj;
            if (request.view == null) {
                request.view = mInflater.inflate(
                        request.resid, request.parent, false);
            }
          //拿到View后在onInflateFinished中调用setContentView(View view)；
            request.callback.onInflateFinished(
                    request.view, request.resid, request.parent);
            mInflateThread.releaseRequest(request);
            return true;
        }
    };
```

4.AsyncInflater的原理比较简单，注意一点是有可能会内存泄漏，因为InflateThread是一个单例，它会持有Activity的引用。
