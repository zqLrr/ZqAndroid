# 3 Android 大纲2-多媒体

## 手势Gestures

* Android 提供手势检测，提供相应的监视器
* Android 提供Api添加相应的手势，并且提供了相应的Api来识别相应的手势

### 手势检测

* 关键类
  * **MotionEvent**：封装手势动作
  * **GestureDetector**:识别各种手势
  *  **OnGestureListener**:手势的监听，根据`GestureDetector`区分的行为，提供手势交互

* 手势检测执行顺序

  * 手指触碰屏幕时，触发MotionEvent事件！

  * 该事件被OnTouchListener监听，可在它的onTouch()方法中获得该MotionEvent对象
  * 通过GestureDetector转发MotionEvent对象给OnGestureListener
  * 通过OnGestureListener获取相应的行为，可以进行相应的处理

* GestureListener接口中的函数

  按下（onDown）： 刚刚手指接触到触摸屏的那一刹那，就是触的那一下。

  抛掷（onFling）： 手指在触摸屏上迅速移动，并松开的动作。

  长按（onLongPress）： 手指按在持续一段时间，并且没有松开。

  滚动（onScroll）： 手指在触摸屏上滑动。

  按住（onShowPress）： 手指按在触摸屏上，它的时间范围在按下起效，在长按之前。

  抬起（onSingleTapUp）：手指离开触摸屏的那一刹那。

  ```java
  //原始的监听接口
  GestureDetector.OnGestureListener
  //实现了接口方便我们操作
  GestureDetector.SimpleOnGestureListener
  ```

* 简单实现

  ```java
  public class GestureActivity extends AppCompatActivity {
  
      private static final String TAG = "MyGesture";
      private static  final int MIN_MOVE = 200;
  
      private MyGestureListener myGestureListener;
      private MySimpleGestureListener mySimpleGestureListener;
      private GestureDetector mDetector;
  
      @Override
      protected void onCreate(Bundle savedInstanceState) {
          super.onCreate(savedInstanceState);
          setContentView(R.layout.activity_gesture);
          myGestureListener = new MyGestureListener();
          mySimpleGestureListener = new MySimpleGestureListener();
          mDetector = new GestureDetector(this,mySimpleGestureListener);
      }
  
      @Override
      public boolean onTouchEvent(MotionEvent event) {
          return mDetector.onTouchEvent(event);
      }
  
      private class  MyGestureListener implements GestureDetector.OnGestureListener{
  
          @Override
          public boolean onDown(MotionEvent e) {
              //Toast.makeText(getApplicationContext(),"on down,手指触摸到触摸屏",Toast.LENGTH_SHORT).show();
              Log.d(TAG, "onDown:,手指触摸到触摸屏 ");
              return false;
          }
  
          @Override
          public void onShowPress(MotionEvent e) {
              Log.d(TAG, "onShowPress: 在长按之前");
          }
  
          @Override
          public boolean onSingleTapUp(MotionEvent e) {
              Log.d(TAG, "onSingleTapUp: 手指离开触摸屏");
              return false;
          }
  
          @Override
          public boolean onScroll(MotionEvent e1, MotionEvent e2, float distanceX, float distanceY) {
              Log.d(TAG, "onScroll: 在触摸屏屏上滑动");
              return false;
          }
  
          @Override
          public void onLongPress(MotionEvent e) {
              Log.d(TAG, "onLongPress:  长案且没有松开");
          }
  
          @Override
          public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX, float velocityY) {
              Log.d(TAG, "onFling: 迅速滑动，并松开");
              return false;
          }
      }
  
      //SimpleOnGestureListener的使用可以方便我们 只想使用的操作
      private class MySimpleGestureListener extends GestureDetector.SimpleOnGestureListener{
  
          @Override
          public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX, float velocityY) {
              if(e1.getY() - e2.getY() > MIN_MOVE){
                  Toast.makeText(getApplicationContext(),"向上滑动，打开登录的Activity",Toast.LENGTH_SHORT).show();
                  Intent intent = new Intent();
                  intent.setData(Uri.parse("fangweibo://login"));
                  startActivity(intent);
              } else if(e2.getY() - e1.getY() > MIN_MOVE){
                  Toast.makeText(getApplicationContext(),"向下滑动，关闭Activity",Toast.LENGTH_SHORT).show();
                  finish();
              }
              return true;
          }
      }
  }
  ```

### 手势添加和识别

Andorid 提供了相应的Api,提供了手势库和对手势的识别Api.

* 相关类

  * **GestureLibrary**：手势库
  * **GestureLibraries**:手势加载的工具类

* 相关方法

  * 加载手势库的方法GestureLibraries

    * GestureLibraries.fromFile(File path)
    * GestureLibraries.fromFile(String filepath)
    * GestureLibraries.fromPrivateFile(Context context, String name)
    * fromFileDescriptor(ParcelFileDescriptor pfd)
    * fromRawResource(Context context, int resourceId)

  * `public void addGesture(String entryName, Gesture gesture)`：添加一个名为entryName的手势

    `public Set<String> getGestureEntries ()`：获得手势库中所有手势的名称

    `public ArrayList<Gesture> getGestures (String entryName)`：获得entryName名称对应的全部手势

    `public ArrayList<Prediction> recognize (Gesture gesture)`: 从当前手势库中识别与gesture匹配的全部手势
     `public void removeEntry (String entryName)`：删除手势库中entryName名称对应的手势

    `public void removeGesture (String entryName, Gesture gesture)`：删除手势库中entryName和gesture都匹配的手势

    `public abstract boolean save ()`：想手势库中添加手势或从中删除手势后调用该方法保存手势库

    `public abstract boolean load()` Android10 存储改变之后，无法在用

* GestureOverlayView：手势画图的View控件

  ```xml
  android:gestureColor="@color/red" //手势颜色
  android:gestureStrokeWidth="5" //宽度
  android:gestureStrokeType="multiple" //是否一笔完成
  ```

  ```java
  //监听
  OnGesturePerformedListener
    OnGestureListener
    OnGesturingListener
  ```

实例：

```xml
//activity_gesture.xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center_horizontal"
    tools:context=".GestureActivity">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="上滑启动Activity,下滑关闭Activity"
        android:textSize="20sp" />
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="在下方画出你要保存的手势"
        android:textSize="20sp" />
    <android.gesture.GestureOverlayView
        android:background="@drawable/gesture_back"
        android:id="@+id/gesture_view"
        android:layout_width="300dp"
        android:layout_height="300dp"
        android:gestureColor="@color/red"
        android:gestureStrokeWidth="5"
        android:gestureStrokeType="multiple"/>
    <android.gesture.GestureOverlayView
        android:layout_marginTop="10dp"
        android:background="@drawable/gesture_back"
        android:id="@+id/gesturecomp_view"
        android:layout_width="300dp"
        android:layout_height="300dp"
        android:gestureColor="@color/red"
        android:gestureStrokeWidth="5"
        android:gestureStrokeType="multiple"/>
</LinearLayout>
//dialog
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="50dp"
        android:orientation="horizontal">
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="match_parent"
            android:layout_marginLeft="10dp"
            android:gravity="center"
            android:text="手势名称："
            android:textSize="20dp" />
        <EditText
            android:id="@+id/gesture_name"
            android:layout_width="200dp"
            android:layout_height="match_parent"
            android:textSize="20dp" />
    </LinearLayout>
    <ImageView
        android:id="@+id/img_gesture"
        android:layout_width="300dp"
        android:layout_height="300dp" />
</LinearLayout>
```

```java
package com.example.androiddemo;

import androidx.appcompat.app.AppCompatActivity;

import android.app.AlertDialog;
import android.app.Dialog;
import android.content.DialogInterface;
import android.content.Intent;
import android.gesture.Gesture;
import android.gesture.GestureLibraries;
import android.gesture.GestureLibrary;
import android.gesture.GestureOverlayView;
import android.gesture.Prediction;
import android.graphics.Bitmap;
import android.graphics.Color;
import android.net.Uri;
import android.os.Bundle;
import android.util.Log;
import android.view.GestureDetector;
import android.view.MotionEvent;
import android.view.View;
import android.view.ViewDebug;
import android.view.ViewGroup;
import android.widget.ArrayAdapter;
import android.widget.EditText;
import android.widget.ImageView;
import android.widget.Toast;

import com.alibaba.android.arouter.facade.annotation.Route;
import com.example.androiddemo.helper.Constants;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.InputStream;
import java.util.ArrayList;
import java.util.zip.Inflater;

@Route(path = Constants.GESTURE)
public class GestureActivity extends AppCompatActivity {

    private static final String TAG = "MyGesture";
    private static final int MIN_MOVE = 200;

    private MyGestureListener myGestureListener;
    private MySimpleGestureListener mySimpleGestureListener;
    private GestureDetector mDetector;
    private GestureOverlayView gestureView,gestureCompView;
    private GestureLibrary gestureLibrary;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_gesture);
        initView();
    }

    private void initView() {
        myGestureListener = new MyGestureListener();
        mySimpleGestureListener = new MySimpleGestureListener();
        mDetector = new GestureDetector(this, mySimpleGestureListener);
        gestureLibrary = GestureLibraries.fromFile(getPrivateFilepath());
//        if(gestureLibrary.load()){
//            Toast.makeText(GestureActivity.this,"手势库加载成功",Toast.LENGTH_SHORT).show();
//        } else {
//            Toast.makeText(GestureActivity.this,"手势库加载失败",Toast.LENGTH_SHORT).show();
 //       }
        gestureView = findViewById(R.id.gesture_view);
        gestureView.addOnGesturePerformedListener(new GestureOverlayView.OnGesturePerformedListener() {
            @Override
            public void onGesturePerformed(GestureOverlayView overlay, Gesture gesture) {
                View view = getLayoutInflater().inflate(R.layout.dialog_save_gesture,null, false);
                ImageView gesture_img = view.findViewById(R.id.img_gesture);
                EditText  gesture_name = view.findViewById(R.id.gesture_name);
                Bitmap bitmap = gesture.toBitmap(128,128,10, Color.RED);
                gesture_img.setImageBitmap(bitmap);
                AlertDialog dialog = new AlertDialog.Builder(GestureActivity.this)
                        .setView(view)
                        .setPositiveButton("保存", new DialogInterface.OnClickListener() {
                            @Override
                            public void onClick(DialogInterface dialog, int which) {
                                gestureLibrary.addGesture(gesture_name.getText().toString(),gesture);
                                gestureLibrary.save();
                            }
                        })
                        .setCancelable(true)
                        .setNegativeButton("取消",null)
                        .show();
            }
        });
        gestureCompView = findViewById(R.id.gesturecomp_view);
        gestureCompView.addOnGesturePerformedListener(new GestureOverlayView.OnGesturePerformedListener() {
            @Override
            public void onGesturePerformed(GestureOverlayView overlay, Gesture gesture) {
                ArrayList<Prediction> predictions = gestureLibrary.recognize(gesture);
                ArrayList<String> result = new ArrayList<String>();
                for(Prediction prd : predictions){
                    if(prd.score > 2){
                        result.add("与手势【"+prd.name+"】匹配度为"+prd.score+"%");
                    }
                }
                ArrayAdapter<Object> adapter = new ArrayAdapter<Object>(GestureActivity.this, android.R.layout.simple_dropdown_item_1line,result.toArray());
                new AlertDialog.Builder(GestureActivity.this).setAdapter(adapter,null)
                        .setPositiveButton("Ok",null).show();
            }
        });
    }

    private String getPrivateFilepath(){
        String privatefile = getExternalFilesDir("Caches").toString() +"/mygestures";
        File file = new File(privatefile);
        if(!file.exists()){
            file.mkdir();
        }
        return privatefile;
    }


    @Override
    public boolean onTouchEvent(MotionEvent event) {
        return mDetector.onTouchEvent(event);
    }

    private class MyGestureListener implements GestureDetector.OnGestureListener {

        @Override
        public boolean onDown(MotionEvent e) {
            //Toast.makeText(getApplicationContext(),"on down,手指触摸到触摸屏",Toast.LENGTH_SHORT).show();
            Log.d(TAG, "onDown:,手指触摸到触摸屏 ");
            return false;
        }

        @Override
        public void onShowPress(MotionEvent e) {
            Log.d(TAG, "onShowPress: 在长按之前");
        }

        @Override
        public boolean onSingleTapUp(MotionEvent e) {
            Log.d(TAG, "onSingleTapUp: 手指离开触摸屏");
            return false;
        }

        @Override
        public boolean onScroll(MotionEvent e1, MotionEvent e2, float distanceX, float distanceY) {
            Log.d(TAG, "onScroll: 在触摸屏屏上滑动");
            return false;
        }

        @Override
        public void onLongPress(MotionEvent e) {
            Log.d(TAG, "onLongPress:  长案且没有松开");
        }

        @Override
        public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX, float velocityY) {
            Log.d(TAG, "onFling: 迅速滑动，并松开");
            return false;
        }
    }

    //SimpleOnGestureListener的使用可以方便我们 只想使用的操作
    private class MySimpleGestureListener extends GestureDetector.SimpleOnGestureListener {

        @Override
        public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX, float velocityY) {
            if (e1.getY() - e2.getY() > MIN_MOVE) {
                Toast.makeText(getApplicationContext(), "向上滑动，打开登录的Activity", Toast.LENGTH_SHORT).show();
                Intent intent = new Intent();
                intent.setData(Uri.parse("fangweibo://login"));
                startActivity(intent);
            } else if (e2.getY() - e1.getY() > MIN_MOVE) {
                Toast.makeText(getApplicationContext(), "向下滑动，关闭Activity", Toast.LENGTH_SHORT).show();
                finish();
            }
            return true;
        }
    }
}
```

