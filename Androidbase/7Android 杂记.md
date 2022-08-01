# 7Android 杂记

## IMSL IMEL MEID ICCID相关知识

###  IMSI

国际移动用户识别码（IMSI） international mobile subscriber identity 国际上为唯一识别一个移动用户所分配的号码。

IMSI共有15位，其结构如下：MCC+MNC+MSIN ，（MNC+MSIN=NMSI）　　

Mcc：Mobile Country Code，移动国家码，MCC的资源由国际电联（ITU）统一分配和管理，唯一识别移动用户所属的国家，共3位，中国为460;　

 MNC:Mobile Network Code，移动网络码，共2位，中国移动TD系统使用00，中国联通GSM系统使用01，中国移动GSM系统使用02，中国电信CDMA系统使用03，一个典型的IMSI号码为460030912121001。

 MSIN:Mobile Subscriber Identification Number共有10位，其结构如下：09+M0M1M2M3+ABCD　其中的M0M1M2M3和MDN号码中的H0H1H2H3可存在对应关系，ABCD四位为自由分配。

### IMEI

IMEI(International Mobile Equipment Identity)是移动设备国际身份码的缩写，移动装备国际辨识码，是由15位数字组成的"电子[串号](http://baike.baidu.com/view/545571.htm)"，它与每台手机一一对应，而且该码是全世界唯一的。

* 每一部手机在组装完成后都将被赋予一个全球唯一的一组号码，这个号码从生产到交付使用都将被制造生产的厂商所记录。
* 手机用户可以在手机中查到自己手机的IMEI码。
* 但是有些手机是有两个IMEL,这是因为在移动设备开发规范中，IMEI和IMSI存在一一对应的关系，所以在双卡双待的情况下，两个IMEL号相对安全。
* 有些双卡双待也是一个IMEI,是因为手机系统和入网时都不查IMEI.

### MEID

MEID 移动设备识别码(Mobile Equipment Identifier)是CDMA手机的身份识别码，也是每台CDMA手机或通讯平板唯一的识别码。通过这个识别码，网络端可以对该手机进行跟踪和监管。用于CDMA制式的手机。MEID的数字范围是十六进制的，和IMEI的格式类似。

* 现在没有电信卡也会分配一个MEID,是因为不清楚用户会不会用电信卡。

### ICCID 

Integrate circuit card identity 集成电路卡识别码（固化在手机SIM卡中） ICCID为IC卡的唯一识别号码，共有20位数字组成，其编码格式为：XXXXXX 0MFSS YYGXX XXXXX。分别介绍如下： 前六位运营商代码：中国移动的为：898600；中国联通的为：898601。

当API<29时，并静态添加`Read_Phone_State`权限，同时运行时授权，可以读取到ISMI、IMEI、MEID。

现在API>=29时，都无法获取ISMI、IMEI、MEID，看API可知，它需要以下的权限：

```java
@RequiresPermission(android.Manifest.permission.READ_PRIVILEGED_PHONE_STATE)
//IMSI
telephoneManager.getSub
//ICCID
telephoneManager.getSimSerialNumber()
 //IMEI MEID
telephoneManager.getDeviceID()
```

而这个权限是只有系统才能获取的，普通应用无法获取，那么应该以怎样的方式获取这些值呢？

* 获取MCC+MNC

  ```java
  public String getNetworkOperator ()
  ```

  ```java
  //无需权限即可获取MCC+MNC
  String stringNetworkOperator = telephonyManager.getNetworkOperator();
  String[] stringArrayMccMnc = { "", "" };
  if(TextUtils.isEmpty(stringNetworkOperator) || !isNumeric(stringNetworkOperator)){
    return stringArrayMccMnc;
  }
  
  if(!TextUtils.isEmpty(stringNetworkOperator) && stringNetworkOperator.length() >= 5){
    stringArrayMccMnc[0] = stringNetworkOperator.substring(0, 3);
    stringArrayMccMnc[1] = stringNetworkOperator.substring(3, 5);
  }
  return stringArrayMccMnc;
  ```

* 获取手机号

  需要以下权限的任何一个

  ```
  android.Manifest.permission.READ_PHONE_STATE,
  android.Manifest.permission.READ_SMS,
  android.Manifest.permission.READ_PHONE_NUMBERS
  ```

  ```java
    //获取手机号
  telephoneManager.getLine1Number();
  ```

* 获取移动网络类型

  ```java
  //需要权限READ_PHONE_STATE 无法区分是否为无线
  telephoneManager.getNetworkType()
  telephoneManager.getDataNetworkType();
  ```

​		或者还可以这样获取,但下面的方法在API 29已被上面的方法代替

     //需要权限
         ConnectivityManager connectivity = (ConnectivityManager)context.getSystemService(Context.CONNECTIVITY_SERVICE);
        if (null == connManager) { // 为空则认为无网络
          return NETWORK_NONE;
        }
        // 获取网络类型，如果为空，返回无网络
        NetworkInfo activeNetInfo = connManager.getActiveNetworkInfo();
        if (activeNetInfo == null || !activeNetInfo.isAvailable()) {
          return NETWORK_NONE;
        }
        // 判断是否为WIFI
        NetworkInfo wifiInfo = connManager.getNetworkInfo(ConnectivityManager.TYPE_WIFI);
        if (null != wifiInfo) {
          NetworkInfo.State state = wifiInfo.getState();
          if (null != state) {
            if (state == NetworkInfo.State.CONNECTED || state == NetworkInfo.State.CONNECTING) {
              return NETWORK_WIFI;
            }
          }
        }
        //若是移动网络类型 getSubtype()
        NetworkInfo networkInfo = connectivity.getActiveNetworkInfo();
        if(null != networkInfo){
          int type = networkInfo.getSubtype();
          Log.d(TAG, "get phone mobile type: "+ type);
        }



## Android 的权限

 应用权限有助于保护对以下数据的访问和对以下操作的执行，从而为保护用户隐私提供支持：

* 受限数据：例如系统状态和用户的联系信息。
* 受限应用：例如连接到已配对的设备并录制音频。

### 权限的工作流程

![workflow-Android-permission](7Android 杂记.assets/workflow-Android-permission.svg)

工作流工作流程如下：

(1) 评估该功能是否不需要声明权限，若是，执行(2a),若不是执行(2b)

(2a) 执行该功能

(2b) 在App的AndroidManifest.xml文件中声明权限,接着执行(3)

(3) 判断该权限是否是运行时权限，若是执行(4),若不是执行(5)

(4) 在运行时申请用户提供权限，若申请成功，执行(5)

(5)执行该功能

### 权限的类型

* 安装时权限

  安装时权限授予应用对受限数据的受限访问权限，并允许应用执行对系统或其他应用只有最低影响的受限操作。如果您在应用中声明了安装时权限，系统会在用户安装您的应用时自动授予应用相应权限。应用商店会在用户查看应用详情页面时向其显示安装时权限通知。

  * 普通权限

    影响较低，类型级别为“normal”

  * 签名权限

    类型级别为“signature”

    当应用声明了其他应用已定义的签名权限时，如果两个应用使用同一证书进行签名，系统会在安装时向前者授予该权限。否则，系统无法向前者授予该权限。

  操作流程：

  一般只需要声明权限

* 运行时权限

  危险权限，类别为“dangerous”。

  操作流程：

  ```java
  //1、声明权限
      <uses-permission android:name="android.permission.READ_PHONE_STATE" />
  //2、判断权限是否授予  
  //3、判断是否需要显示原因
  //4、请求权限
            TelephonyManager manager = (TelephonyManager) getApplicationContext().getSystemService(Context.TELEPHONY_SERVICE);
  try {
    //判断权限是否授予  
    if (ActivityCompat.checkSelfPermission(this,
                                           Manifest.permission.READ_SMS) != PackageManager.PERMISSION_GRANTED &&
        ActivityCompat.checkSelfPermission(this,
                                           Manifest.permission.READ_PHONE_NUMBERS) != PackageManager.PERMISSION_GRANTED &&
        ActivityCompat.checkSelfPermission(this,
                                           Manifest.permission.READ_PHONE_STATE) != PackageManager.PERMISSION_GRANTED) {
      //判断是否需要显示原因
      if(shouldShowRequestPermissionRationale("需要手机号")){
        //请求权限
        requestPermissions(new String[] { Manifest.permission.READ_PHONE_STATE },PERMISSION_REQUEST);
      } else {
        requestPermissions(new String[] { Manifest.permission.READ_PHONE_STATE },PERMISSION_REQUEST);
      }
      return;
    }
    //执行权限
    String phone = manager.getLine1Number();
    Log.d(TAG, "get phone Number"+ phone);
    int type = manager.getDataNetworkType();
    Log.d(TAG, "get phone mobile type: "+ type);
    String imsl = manager.getSubscriberId();
    Log.d(TAG, "get phone imsl"+ imsl);
  } catch (Exception e){
    Log.d(TAG, "onStartTestIMSL: get phone fail"+ e.toString());
  }
  //5、执行权限，用户同意后，回调进行处理Activity#onRequestPermissionsResult
      @Override
      public void onRequestPermissionsResult(int requestCode, String[] permissions,
                                             int[] grantResults) {
          switch (requestCode){
              case PERMISSION_REQUEST:
                  if(grantResults.length > 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED){
                      TelephonyManager manager = (TelephonyManager) getApplicationContext().getSystemService(Context.TELEPHONY_SERVICE);
                      try {
                          Log.d(TAG, "onRequestPermissionsResult: ");
                          String phone = manager.getLine1Number();
                          Log.d(TAG, "get phone Number"+ phone);
                          int type = manager.getDataNetworkType();
                          Log.d(TAG, "get phone mobile type: "+ type);
                          String imsl = manager.getSubscriberId();
                          Log.d(TAG, "get phone imsl "+ imsl);
                      } catch (Exception e){
                          Log.d(TAG, "get read_phone_state faild;"+ e.toString());
                      }
  
                  } else {
                      Log.d(TAG, "get read_phone_state faild");
                  }
                  break;
              default:
                  break;
          }
      }
  ```

* 特殊权限

  系统自定义的特殊权限，类别为“appop”

* 自定义权限

  为自己应用的数据或操作提供相应的自定义权限

  

## Android 无障碍适配

* 无障碍扫描器 Accessibility Scanner

  *  方便校对适配效果
  * 不用打开Talkback

* 使用 ViewCompat#setAccessibilityDelegate(View, AccessibilityDelegateCompat) 进行统一处理

  具体方式：

  ```java
  AccessibilityDelegateCompat
  ```
  


开始精确定位

WeiboLocationManager#startRealtimeFuzzyLocation

WeiboLocationManager#startNotGmsFuzzyLocation

# Android 打包签名

将Apk解压或使用Android Studio 分析后，会有三个文件

* MANIFEST.MF
* CERT.SF
* CERT.RSA

# Android Gradlew 依赖问题

1. `Program type already present: XXX`

一般是依赖重复导致的，解决方案是排除重复的依赖。

排查依赖的命令：`./gradlew -g 模块名:dependencies`,会出现多个依赖树，
