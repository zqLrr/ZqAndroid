# Android Demo  200

## 1、抽屉式公告

* 设置SlidingDrawer控件

* 设置抽屉引导按钮

* 设置抽屉显示内容

* 实现全屏抽屉式广告

  ```xml
  android:content="@id/show_content"  //SlidingDraw 显示内容
  android:handle="@id/ly_1" //打开抽屉式公告的按钮
  ```

  

## 2、滑动菜单

* 绘制隐藏菜单界面
* 新建一个HorizontalScrollView的子类View，用来设置滚动
* 在View 中重写onMeasure()、onLayout()、onTouchEvent()方法
* 绘制主界面，将View添加进去
* 遇到的问题
  * 因为总体主题布局，Button的颜色不变
    * MainActivity 继承Activity
    * 修改主题

## 3、退出对话框

* 实现主界面布局

* 实现Dialog布局

* 新建动画自定义样式

* 新建一个Dialog继承Dialog，绘制布局

* 在主布局中实现onKeyDown()函数

  ```java
    @Override
      public boolean onKeyDown(int keyCode, KeyEvent event) {
          //如果单击了返回按钮
          if(keyCode == KeyEvent.KEYCODE_BACK){
              dlg = new Dialog(this);
              dlg.setCancelable(false);//点击空白处不消失
              Window window = dlg.getWindow();
              window.setContentView(R.layout.dialog_layout);
              window.setWindowAnimations(R.style.myStyle);
              Bt_Ensure = (Button)window.findViewById(R.id.Ensure);
              Bt_Cancle =(Button)window.findViewById(R.id.Cancle);
              initEvent();
              dlg.show(); //显示对话框
  }
  ```

  

## 4、消息通知

* 创建主布局和消息返回的布局

* 发送消息（在Android8.0之后，必须要添加通知渠道）

  * 获取通知服务管理

  * 创建通知渠道

  * 创建一个启动其他Activity的Intent (PendingIntent)

  * 创建消息通知类

  * 发送通知

    ```java
          //获取通知服务管理
            NotificationManager notificationManager = (NotificationManager) getSystemService(NOTIFICATION_SERVICE);
    
            //创建一个启动其他消息的Intent
            Intent intent = new Intent(MainActivity.this, Message.class);
            PendingIntent Pi = PendingIntent.getActivity(this, 0, intent, 0);
    
    
            //创建消息通知类
            Notification notification = new Notification.Builder(MainActivity.this)
                    .setChannelId(CHANNEL_1)  //设置消息通道渠道
                    .setAutoCancel(true)    //设置打开消息通知后该通知自动消失
                    .setTicker("第一条通知发送了")
                    .setLargeIcon(BitmapFactory.decodeResource(getResources(), R.mipmap.ic_launcher)) //设置下拉列表中的大图
                    .setContentTitle("我是标题")  //设置标题
                    .setContentText("点击查看详情")
                    .setWhen(System.currentTimeMillis()) //设置当前时间为发送时间
                    //设置标题蓝消息通知小图标
                    .setSmallIcon(R.mipmap.ic_launcher)
                    .setContentIntent(Pi)
                    .build();
    
            //创建通知渠道
            NotificationChannel channel1 = new NotificationChannel(CHANNEL_1, "热点信息", NotificationManager.IMPORTANCE_HIGH);
            channel1.setDescription("通知渠道描述");
            notificationManager.createNotificationChannel(channel1);
            //发送通知
            notificationManager.notify(NOTIFYID, notification);
    ```

## 5、苹果退出对话框

* 建立退出对话框的Activity

* 创建对话框布局和样式

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
      android:layout_width="match_parent"
      android:background="@drawable/mdialog_bg"
      android:orientation="vertical"
      android:layout_height="match_parent">
  <!--    对话框标题区域-->
  <RelativeLayout
      android:layout_width="@dimen/layout_width"
      android:layout_height="@dimen/layout_height"
      android:id="@+id/layout_title"
      android:gravity="center">
      <TextView
          android:id="@+id/title"
          android:layout_width="wrap_content"
          android:layout_height="wrap_content"
          android:text="@string/text_title"
          android:textColor="@color/black"
          android:gravity="center"/>
  </RelativeLayout>
  <!--    对话框横线-->
      <View
          android:layout_width="@dimen/layout_width"
          android:layout_height="1dp"
          android:background="@color/grey"
          android:layout_below="@+id/layout_title"
          android:layout_marginBottom="10dp"/>
  <!--    对话框按钮-->
      <RelativeLayout
          android:id="@+id/bottomBtn"
          android:layout_width="@dimen/layout_width"
          android:layout_height="wrap_content"
          android:layout_below="@+id/layout_title">
          <LinearLayout
              android:layout_width="@dimen/layout_width"
              android:layout_height="wrap_content"
              android:orientation="horizontal">
  <!--            取消按钮-->
              <Button
                  android:id="@+id/btn_cancle"
                  android:layout_width="0dp"
                  android:layout_weight="1"
                  android:layout_height="wrap_content"
                  android:text="@string/text_cancel"
                  android:textColor="@color/blue"
                  android:paddingTop="10dp"
                  android:paddingBottom="10dp"/>
              <View
                  android:layout_width="1dp"
                  android:layout_height="match_parent"
                  android:background="@color/grey"/>
              <Button
                  android:id="@+id/btn_exit"
                  android:layout_width="0dp"
                  android:layout_weight="1"
                  android:layout_height="wrap_content"
                  android:text="@string/text_exit"
                  android:textColor="@color/blue"
                  android:paddingTop="10dp"
                  android:paddingBottom="10dp"/>
          </LinearLayout>
      </RelativeLayout>
  
  </RelativeLayout>
  ```

* 新建Mdialog 继承于Dialog，添加样式和布局

  ```java
   super(context,R.style.mdialog);
   //绘制背景
   View view = LayoutInflater.from(getContext()).inflate(R.layout.activity_mdialog,null);
  ```

* 在Activity中显示对话框，并对取消和确定退出按钮添加点击事件

