# Boradcast Receiver 

> 即广播接收器，用于监听/接收Android应用分发的各类广播并做出相应的响应。

## 应用场景

1. 监听系统事件：如开机广播，网络连接与断开，屏幕开启与关闭等
2. 不同组件间通信（多个Activity/service，包括不同应用间）
3. 多线程通信

3、广播类型：无序广播、有序广播和粘性广播（超出10s）

4、生命周期：从调用开始到onReceiver()方法执行完毕结束。（一般不超过10s,会出现ANR）

5、实现原理：观察者模式（基于**消息发布/订阅模式的事件驱动模型**）

​	广播模型中包含三个角色：

1. 消息发布者（广播发布者）
2. 消息中心（AMS，即Activity Manager Service）
3. 消息订阅者（广播接收者）

原理流程：

1.广播接受者通过Binder机制在AMS中注册监听

2.广播发布者通过Binder机制向AMS发送广播

3.AMS根据发送者的需求，在已注册表中获取到合适的广播接受者（根据Intent-filter，Permission）

4.AMS将广播发送给合适的广播接受者的消息循环队列中

5.广播接受者通过消息循环获取到广播并回调onReceive()



## 广播注册

注意：

* 不要在onReceiver中进行耗时操作
* 不要在onReceiver中开启异步任务
* receiver的注册有数量限制，系统限制是1000个，动态注册的广播在不需要时，必须销毁。

### 静态注册

* 新建一个Broadcast Receiver子类，修改其函数
* 在AndroidManifest.xml中通过<receive>标签进行申明

### 动态注册

* 在代码中通过registerReceiver(BroadcastReceiver receiver, IntentFilter filter)来动态注册广播

## 广播接收者动态注册和静态注册的区别

| 动态注册                                                 | 静态注册                                                   |
| -------------------------------------------------------- | ---------------------------------------------------------- |
| 在程序中进行                                             | 在AndroidManifest.xml中定义                                |
| 活动销毁时，广播接收器也要unRegisterReceiver()           | 为常驻广播，即使应用处于非运行状态，也可接收               |
| 优先级较高，且可以控制注册与注销，开销更小，生命周期可控 | 生命周期不易控制                                           |
| 支持隐式intent和显式intent                               | Android 8 之后隐式intent不支持sendboardCast,支持显式intent |

### 接收特定权限的广播

* 首先，在AndroidManifest.xml中定义新的权限SEND_XXX，例如：

```xml
<permission android:name="com.android.SEND_XXX"
            android:protectionLevel="normal"/> 
//定义权限规则：权限的名称必须遵循 包名+.+字符串 的规则，详细属性见下图
```

![定义权限](/Users/zhangqian20/git profile/ZqAndroid/docs/png/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBASGVpa28tQW5kcm9pZA==,size_20,color_FFFFFF,t_70,g_se,x_16.png)

然后，在接受者app的Androidmanifest.xml中的<receiver> tag里添加权限SEND_XXX的声明

```xml
<!-- 声明自定义权限 -->
<uses-permission android:name="com.android.permission.SEND_XXX" />

<receiver android:name=".MyBroadcastReceiver"   
         android:permission="com.android.permission.SEND_XXX">   
   <intent-filter>  
        <action android:name="com.android.XXX_ACTION" />   
   </intent-filter>  
</receiver> 
```

同时，发送者也需要在Androidmanifest.xml中声明权限。

## 广播发送

### 普通广播

sendBroadcast(),sendOrderedBroadcast()和sendStickyBroadcast()

```java
//发送广播
Intent intent = new Intent();  
intent.setAction("com.itlao5.broadcast");  
intent.putExtra("data", "data");  
sendBroadcast(intent);
```

#### 显式广播和隐式广播

> 显式和隐式是Intent的概念

静态注册Android8 之后不支持隐式，若要支持隐式需要+Flag,或者加上包名

```java
public static final int FLAG_RECEIVER_INCLUDE_BACKGROUND = 0x01000000;
intent.addFlags(0x01000000);
intent.setPackage(getPackageName());
```

```java
//静态注册
//显式广播
try {
  Intent intent = new Intent(MainActivity.this, Class.forName("com.example.androiddemo.BroadcastReceiver.testReceiver"));
  //intent.setAction("staticFilter");
  intent.putExtra("data", "test BoardCast");
  sendBroadcast(intent);
} catch (ClassNotFoundException e){
  e.printStackTrace();
}
//隐式广播
Intent intent = new Intent();
intent.setAction("staticFilter");
intent.putExtra("data", "test BoardCast");
intent.addFlags(0x01000000);
sendBroadcast(intent)；
```

### 有序广播

> 有序广播是存在接收优先级的广播，可以使用setResult系列、getResult系列及abort（中止）系列API来处理不同优先级的广播。

```java
// 创建一个 Intent 对象，指定广播的动作
Intent intent = new Intent("com.example.ORDERED_ACTION");
// 添加额外的数据到 Intent 中（可选）
intent.putExtra("message", "Hello, world!");

// 发送有序广播
sendOrderedBroadcast(intent, null);
```

#### 有序广播的优先级

* 添加方式

  ```java
  //静态注册
   <intent-filter android:priority="1000"></intent-filter>
  //动态注册
   intentFilter.setPriority(10);
  ```

  Android 对于广播的优先级定义为优先级数值越大，则优先级越高，实际上有以下效果，使用优先级一般是有序广播，有序广播是同步进行的。

* 发送有序广播

  | 广播注册类型                     | 实际效果                                                     |
  | -------------------------------- | ------------------------------------------------------------ |
  | 全部是静态注册广播               | 数值越大，优先级越低                                         |
  | 全部是动态注册广播               | 数值越大，优先级越高，优先级相同的已先注册的优先             |
  | 既有动态注册广播又有静态注册广播 | 当只有一个动态注册的广播时，动态注册的广播优先级永远高于静态注册的广播，且静态注册的广播是数值越大，优先级低<br />当有多个动态注册的广播时，按照注册的顺序来接收广播 |

* 发送无序广播

  广播是无序的，是不考虑优先级的，因为执行顺序不确定，也无法使用setResult系列、getResult系列及abort（中止）系列API。

### 本地广播

> 本地广播是一种特定于应用程序的广播机制，在应用程序内部进行广播传递，而不会对其他应用程序产生影响

```java
// 创建一个 Intent 对象，指定广播的动作
Intent intent = new Intent("com.example.MY_LOCAL_ACTION");
// 添加额外的数据到 Intent 中（可选）
intent.putExtra("message", "Hello, world!");
// 发送本地广播
LocalBroadcastManager.getInstance(context).sendBroadcast(intent);
```

本地广播提高了传送数据的安全性，并节省了系统资源的开销。

发送的本地广播，静态注册的广播收不到，因此如果有静态注册的广播不适用本地广播。

### 粘性广播

> 粘性广播（Sticky Broadcast）是一种特殊类型的广播，在发送后会一直保留广播的信息，直到下一次发送者发送同样动作的普通广播或应用程序结束。

```java
// 发送粘性广播
Intent stickyIntent = new Intent("com.example.STICKY_ACTION");
stickyIntent.putExtra("message", "Hello, world!");
sendStickyBroadcast(stickyIntent);
```

适用场景：

1. **应用程序状态更新**：当应用程序状态发生变化时，例如网络连接状态、电池电量、应用程序运行状态等，可以发送粘性广播，以便后续打开应用程序的用户能够获取到最新的状态信息。
2. **应用程序内部通信**：在应用程序内部的不同组件之间进行通信时，可以使用粘性广播传递数据，以便后续注册的接收者能够获取到之前发送的数据。
3. **事件历史记录**：当需要记录应用程序中发生的事件历史记录时，可以使用粘性广播保存事件信息，以便后续分析或查看。
4. **数据同步**：在数据同步过程中，可以使用粘性广播传递同步结果或进度信息，以便后续处理或显示。
5. **应用程序初始化**：在应用程序初始化过程中，可能需要发送一些初始化信息或状态更新广播，以便后续注册的接收者能够获取到初始化完成后的状态信息。

### 发送给特定的广播

在AndroidManifest.xml中，我们可以配置

```xml
<permission android:name = "com.android.permission.XXX"/> 
```

然后在发送广播时

```java
sendBroadcast("com.android.XXX_ACTION", "com.android.permission.XXX");  
```

此时，就只有在广播接收的应用中在AndroidManifest.xml中添加对应的XXX权限，才能正常接收到该广播。

```xml
<uses-permission android:name="com.android.permission.XXX">></uses-permission>  
```

## onReceive 参数  context

如果是静态注册的Broadcast，context.toString()输出为：android.app.ReceiverRestrictedContext@e5d8f65

如果是动态注册的Broadcast，context.toString()输出为：com.example.test.MainActivity@84dfef5

静态与动态注册的的Broadcast，context.getPackageName()输出都为：com.example.test

无论静态还是动态注册的Broadcast，都跑在广播所在app的进程

## 未启动的App如何收到广播

> Android 3.1以后新增Stopped机制，如果App未启动在系统中记录为以下形式，是Stopped状态
>
> <pkg name="com.example.test" stopped="true" />
>
> 系统广播中添加了FLAG_EXCLUDE_STOPPED_PACKAGES，因此在stopped状态下无法收到广播。

看一下这两个Flag:

```java
/**
 * If set, this intent will not match any components in packages that
 * are currently stopped.  If this is not set, then the default behavior
 * is to include such applications in the result.
 */
public static final int FLAG_EXCLUDE_STOPPED_PACKAGES = 0x00000010;
/**
 * If set, this intent will always match any components in packages that
 * are currently stopped.  This is the default behavior when
 * {@link #FLAG_EXCLUDE_STOPPED_PACKAGES} is not set.  If both of these
 * flags are set, this one wins (it allows overriding of exclude for
 * places where the framework may automatically set the exclude flag).
 */
public static final int FLAG_INCLUDE_STOPPED_PACKAGES = 0x00000020;
```

简单地说，带有`FLAG_EXCLUDE_STOPPED_PACKAGES`的广播不会被发送给stopped状态的app，而两者都有的情况下以`FLAG_INCLUDE_STOPPED_PACKAGES`为准。这就是stopped机制，能在一定程度上防范流氓软件恶意监听系统广播，而`FLAG_INCLUDE_STOPPED_PACKAGES`则相当于留给开发者用于自定义广播的后门了。

因此，发送广播时，应加上FLAG_INCLUDE_STOPPED_PACKAGES

```java
// By default broadcasts do not go to stopped apps.
intent.addFlags(Intent.FLAG_EXCLUDE_STOPPED_PACKAGES);
```

