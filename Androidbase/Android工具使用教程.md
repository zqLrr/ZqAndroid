#  Charless安装配置教程

1、下载安装Charless [4.6.2](https://www.charlesproxy.com/download/)

2、将charless设置相应操作系统的代理

3、配置Android

* 查看本机IP地址：Help -> Local IP Addresses
* 打开Android 的无线连接，和PC的无线连接保持一致
* 配置代理，手动->填写PC相应的IP地址和端口号
* 会向PC发送配置请求，点击允许，在Proxy -> Access Control Settings里看到可以访问此代理服务器列表

# Adb 命令使用

1、安装

brew install --cask android-platform-tools

2、查询当前运行的Activity

adb shell dumpsys activity | grep -i run

adb shell dumpsys activity | grep mResumedActivity

3、查询正在运行的Services

adb shell dumpsys activity services | grep ServiceName

```adb
dumpsys activity services | grep schemebyService
#运行的会有相关日志打印
```

4、调起Activity

* 通过包名和

adb shell am start [options] <INTENT>

<INTENT> = 包名/Activity名称   

Activity还必须包含属性`android:exported="true"`

Eg： adb shell am start -n com.sina.weibo/com.sina.weibo.VisitorMoreActivity

微博中，包名都是这个：com.sina.weibo

* 通过Scheme 调起Activity

  ```adb
  adb shell am start -a [action] -d [uri] [packagename]
  ```

  Eg：

  ```adb
  adb shell am start -a android.intent.action.VIEW -d "sinaweibo://searchall?"
  ```

  如果Scheme 含有参数，在adb shell 命令下，打开Scheme.

```adb
adb shell #第一步
am start -a android.intent.action.VIEW  -d "sinaweibo://searchall?containerid=100103&q=%23%E8%B0%81%E6%98%AF%E5%87%B6%E6%89%8B%E5%90%91%E5%AE%8B%E8%8C%9C%E9%81%93%E6%AD%89%23&isnewpage=1&extparam=seat%3D1%26source%3Dranklist%26filter_type%3Drealtimehot%26pos%3D0%26pre_seqid%3D583717771%26dgr%3D0%26c_type%3D30%26mi_cid%3D100103%26flag%3D257%26cate%3D0%26display_time%3D1638955922%26pre_seqid%3D583717771"
```

5、查询设备

```adb
adb devices
```

6、adb查询logcat

* 查询当前包名所在的PID 

  ```adb
  adb shell dumpsys meminfo 包名
  adb logcat | grep PID
  ```

* 条件查询

  * 根据Tag和级别查询

    ```adb
    adb logcat -s TAG
    adb logcat TAG:级别 *:s //*:s 意味不查看其他日志
    ```

  * 以某种格式查询

    ```adb
    db logcat -b 缓冲区类型
    注：缓冲区类型（radio-无线缓冲区，events-事件缓冲区，main-主缓冲区，默认）
    ```

* 写到文件中

  ```adb
  adb logvat -f filename 查询条件
  ```

* 清理已经存在的日志

  ```adb
  adb logcat -c
  ```

* 将日志显示在控制台后退出

  ```adb
  adb logcat -d
  ```

# SSH

* 修改密码

  ![image-20210817105630380](Android工具使用教程.assets/image-20210817105630380.png)

# HomeBrew 安装

* 安装命令：（在bash的命令窗口中）

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

* 遇到的问题

  * 安装成功但是有一个Warning和next step的提示

    ![image-20210820193951593](Android工具使用教程.assets/image-20210820193951593.png)

    ![image-20210820194017326](Android工具使用教程.assets/image-20210820194017326.png)

    * 首先我按照提示在.bash_profile添加`$(/opt/homebrew/bin/brew shellenv)`，但是我发现所有的命令not commend,

    * 所以只能执行

      ```bash
      export PATH="/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin:/usr/X11/bin"
      ```

      这样就可以重新打开.bash_profile文件，将添加的删除掉

    * 但是仍然会报错： brew not commend

    * 执行：

      ```bash
      export PATH=$PATH:/opt/homebrew/bin
      ```

      这个方法是将brew命令放在终端中，至此可以使用。

  * 环境变量配置,放在.bash_profile

    ```bash
    export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles/bottles
    ```

  * 一些源的链接

    ```xml
    //清华源
    https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles/bottles
    ```

    

# adb  bugreport

* 读取设备所有信息

  ```bash
  adb bugreport > bugreport.txt
  ```

  会生成该设备的所有信息在压缩包中。

因为adb bugreport没有什么选项，是通过dumpsys等命令配合完成的。

* 重置电池统计信息

  ```bash
  adb shell dumpsys batterystats --reset
  ```

* .Wakelock analysis全部wakelock信息

  ```bash
  adb shell dumpsys batterystats --enable full-wake-history
  ```

*  Kernel trace analysis分析内核，主要分析wakeup source和wakelock activities，首先使kernel分析

  ```bash
  #暂时还不知道这个有什么用
  $ adb root
  $ adb shell
  
  # Set the events to trace.
  $ echo "power:wakeup_source_activate" >> /d/tracing/set_event
  $ echo "power:wakeup_source_deactivate" >> /d/tracing/set_event
  
  # The default trace size for most devices is 1MB, which is relatively low and might cause the logs to overflow.
  # 8MB to 10MB should be a decent size for 5-6 hours of logging.
  
  $ echo 8192 > /d/tracing/buffer_size_kb
  
  $ echo 1 > /d/tracing/tracing_on
  
  #写入日志
  $ echo 0 > /d/tracing/tracing_on
  $ adb pull /d/tracing/trace <some path>
  
  # Take a bug report at this time.
  $ adb bugreport > bugreport.txt
  ```

获取bugreport压缩包后，使用[battery historian](https://github.com/google/battery-historian)分析。

