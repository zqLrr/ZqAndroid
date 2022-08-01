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

​		

