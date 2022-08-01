# 3 Android 大纲2-View控件

## View控件

### AutoCompleteText

* Android:imeOptions  软件盘响应事件
  * actionDone 使用软件盘完成下一步

* 实现下拉列表

### Toolbar

* 导航按钮

  ```xml
  app:navigationIcon="@drawable/icon_back_32px"
  ```

  ```java
  mToolbar.setNavigationOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
      getActivity().finish();
    }
  });
  ```

* title

  ```xml
  app:title="这是游客界面"
  ```

* PopMenu

 ```xml
//使用主题的方式实现
 app:popupTheme="@style/OverflowMenuStyle"

<style name="OverflowMenuStyle" parent="@style/Widget.AppCompat.PopupMenu.Overflow">
  <!-- 是否覆盖住ToolBar-->
  <item name="overlapAnchor">false</item>
  <!--        下拉列表的宽度-->
  <item name= "android:dropDownWidth">10dp</item>
  <item name= "android:paddingLeft">0dp</item>
  <!--        弹出背景色-->
  <!--        <item name="android:popupBackground">@color/black</item>-->
  <!--        <item name="android:background">@drawable/menupopbackground</item>-->
  <!--                 弹出框和toolbar的垂直方向的偏移量，为负则覆盖toolbar-->
  <item name="android:dropDownVerticalOffset">20dp</item>
  <!--        &lt;!&ndash;        弹出框和toolbar的水平方向的偏移量，为负则覆盖toolbar&ndash;&gt;-->
  <!--        <item name="android:dropDownHorizontalOffset">0dp</item>-->
  <!--         弹出框文字颜色-->
  <item name="android:textColor">@color/MenuPopTextColor</item>
</style>
 ```

但是好多属性无法实现。

### ListView

* 优化性能

  * 在ListView的Adapter中复用getView方法中的convertView
  * 使用静态内部类ViewHolder,用于对控件的实例存储进行缓存，减少findViewById的调用次数

* 常用属性

  ```xml
  android:divider="#00000000" //或者在javaCode中如下定义：listView.setDividerHeight(0);
  android:divider="@drawable/list_driver" //设置分割线的图片资源
  android:divider="@drawable/@null" //不想显示分割线
  
  android:scrollbars="none"//隐藏listView的滚动条  
  在javaCode中如下定义
  setVerticalScrollBarEnabled(true);
  android:fadeScrollbars="true" //设置为true就可以实现滚动条的自动隐藏和显示
  
  android:fadingEdge="none" //去掉上边和下边黑色的阴影
  
  android:fastScrollEnabled="true" 
  //在javaCode中如下定义：
  mListView.setFastScrollEnabled(true); //来控制启用，参数false为隐藏。 
  
  android:stackFromBottom="true"  //该属性默认为false，设置为true后，你的列表显示最后一项
  ```

* ListView实现选中每一行高亮

### FloatingActionButton

* 需要CoordinatorLayout的配合
  * 所有的子View会默认显示在左上角
  * 使用方式
    1. 作为顶层布局
    2. 作为与一个或多个子 View 交互的容器

* 属性

  ```xml
  android:src             //设置图标(24dp)
  app:backgroundTint      //设置图标背景颜色。  
  app:rippleColor         //设置点击时水波纹颜色  
  app:elevation           //设置阴影大小 
  app:fabSize             //设置大小  mini auto normal
  app:pressedTranslationZ //按下时距离Z轴的距离  一般大于elevation
  app:layout_anchor       //设置锚点  
  app:layout_anchorGravity//设置相对锚点的位置
  app:layout_gravity       //设置相对位置
  app:borderWidth="0dp"    //边框宽度
  ```

* Java代码实现 同属性

* 实现

  ```xml
      <androidx.coordinatorlayout.widget.CoordinatorLayout
              android:layout_width="match_parent"
              android:layout_height="match_parent">
  
              <ListView
                  android:id="@+id/schemelist"
                  android:layout_width="match_parent"
                  android:layout_height="match_parent"
                  android:cacheColorHint="#00000000"
                  android:fadeScrollbars="true"
                  android:paddingLeft="10dp"
                  android:paddingRight="10dp"
                  android:stackFromBottom="false" />
  
              <com.google.android.material.floatingactionbutton.FloatingActionButton
                  android:id="@+id/add_scheme"
                  android:layout_width="wrap_content"
                  android:layout_height="wrap_content"
                  android:layout_gravity="bottom|end"
                  android:layout_margin="20dp"
                  android:src="@drawable/icon_back_32px"
                  app:backgroundTint="@color/teal_200"
                  app:borderWidth="0dp"
                  app:elevation="8dp"
                  app:fabSize="normal"
                  app:pressedTranslationZ="16dp"
                  app:rippleColor="#e7d16b" />
  
          </androidx.coordinatorlayout.widget.CoordinatorLayout>
  ```

### checkbox

* 修改边框颜色

  ```xml
   android:buttonTint="@color/colorAccent"
  ```

* 修改边框大小

  ```xml
  android:scaleX="0.8"
  android:scaleY="0.8"
  ```

* 添加文字 可以使用text添加文字

### ImageView

#### Glide:加载图片的框架

* 基本使用

  导入库

  ```bash
  implementation 'com.github.bumptech.glide:glide:4.12.0'
  annotationProcessor 'com.github.bumptech.glide:compiler:4.12.0'
  ```

  添加权限

  ```xml
  #读取外部存储权限
  <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
  #网络访问权限
  <uses-permission android:name="android.permission.INTERNET" />
  ```

  Glide的into()使用只能在主线程

  * 加载网络图片
  
  ```java
  Glide.with(context).load(internetUrl).into(targetImageView);
  ```

  * 从文件加载图片
  
  ```java
  //看一下文件的共有目录和私有目录的区别
  File file = new File(Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_PICTURES),"Test.jpg");
  Glide.with(context).load(file).into(imageViewFile);
  ```

  * 从资源id加载图片
  
  ```xml
  int resourceId = R.mipmap.ic_launcher;
  Glide.with(context).load(resourceId).into(imageViewResource);
  ```

  * 播放本地mp4
  
  ```xml
  String filePath = "/storage/emulated/0/Pictures/example_video.mp4";
  Glide.with( context ).load( Uri.fromFile( **new **File( filePath ) ) ).into( imageViewGifAsBitmap );
  ```

  * 加载Gif图片
  
  ```java
  String gifUrl = "xxxxx";
  Glide.with( context ).load( gifUrl ).into( imageViewGif );
  ```

  * 使用bitmap播放Gif
  
  ```java
  Glide.with( context ).load( gifUrl ).asBitmap().into( imageViewGifAsBitmap );
  ```

  * 取消加载
  
  ```java
  Glide.with(fragment).clear(imageView);
  ```

* Target

  ​        一般情况下，我们都是通过into(),将Glide加载的图片放入ImageView，但是有的时候我们不需要Glide处理的图片，而是图片的原图时，我们将会用到Target,当然还有其他功能，以后慢慢补充,target有很多种。

  ​       .into(ImageView view)根据源码会`buildTarget`，Glide会跟踪这个Target,在获得图片资源之后，通知Target来更新内部持有的引用，即相当于一个网络请求后的UI回调。

  ​       .into(ImageView view) 默认使用的Target为`ViewTarget`,默认为以下两个:`BitmapImageViewTarget`和`DrawableImageViewTarget`

  ```java
  if (Bitmap.class.equals(clazz)) {
    return (ViewTarget<ImageView, Z>) new BitmapImageViewTarget(view);
  } else if (Drawable.class.isAssignableFrom(clazz)) {
    return (ViewTarget<ImageView, Z>) new DrawableImageViewTarget(view);
  } else {
    throw new IllegalArgumentException(
      "Unhandled class: " + clazz + ", try .as*(Class).transcode(ResourceTranscoder)");
  }
  ```

  * SimpleTarget:`AppWidgetTarget`、`PreloadTatget`、`NotificationTarget`

    ```java
    private SimpleTarget target = new SimpleTarget<Bitmap>(width,height) {  
        @Override
        public void onResourceReady(Bitmap bitmap, GlideAnimation glideAnimation) {
            // do something with the bitmap
            // for demonstration purposes, let's just set it to an ImageView
            imageView1.setImageBitmap( bitmap );
        }
    };
    
    private void loadImageSimpleTarget() {  
        Glide
            .with( context ) // could be an issue!
            .load(uri )
            .asBitmap()
            .into( target );
    }
    ```

  ​        SimpleTarget可以获取原始图片的大小，Android 推荐使用ViewTarget及其子类，若使用SimpleTarget,尽量提供宽高,使用 Target.SIZE_ORIGINAL 是不安全的，因为它可能导致 Glide 将非常大的图像加载到内存中，从而造成OOM。

  需要注意的点：

  * Target的对象的字段说明，从技术上来说，Java/Android 会允许你在 **.into()** 方法中去声明 target 的匿名内部类。然而，这大大增加了这样一个可能性：即在 Glide 做完图片请求之前， Android 垃圾回收移除了这个匿名内部类对象。最终这可能会导致一个情况，当图像加载完成了，但是回调再也不会被调用。所请确保你所声明的回调对象是作为一个字段对象的，这样你就可以保护它避免被邪恶的 Android 垃圾回收机制回收 ，因此最好在.into()之外声明Target。
  * .with(context),为了安全请使用applicationContext。

* ViewTarget

  `SimpleTarget`只能获取最终的`bitmap`,而无法获取持有`View`的全局对象，当使用自定义View的时候，无法将相应的图片放在View中，并且`Glide`无法根据`View`的大小来缓存图片的大小。

  `ViewTarget`解决了以上的问题，在构造函数传入持有`View`的全局对象，并通过内部的`SizeDeterminer`计算`View`的宽高来提供给`Glide`作为参考，不会造成OOM。

  ```java
  //CustomView为自定义View
  public void loadViewTarget(View view) {
    CustomView customView = (CustomView) findViewById(R.id.cv_result);
    MyViewTarget myViewTarget = new MyViewTarget(customView);
    Glide.with(this)
      .load(R.drawable.book_url)
      .into(myViewTarget);
  }
  
  private class MyViewTarget extends ViewTarget<CustomView, GlideDrawable> {
  
    public MyViewTarget(CustomView customView) {
      super(customView);
    }
  
    @Override
    public void onResourceReady(GlideDrawable resource, GlideAnimation<? super GlideDrawable> glideAnimation) {
      view.setResult(resource.getCurrent());
    }
  }
  ```

* GlideDrawableImageViewTarget - 默认的Target，用于正常的加载和asGif()。(重写onResourceReady 方法后 .asGif() 可省略 )

* BitmapImageViewTarget - 当使用asBitmap()加载时，使用的默认Target。

Target相关回调方法

* void onLoadCleared(Drawable placeholder) 

  加载时调用生命周期回调,取消了和它的资源释放。一般情况不需要我们操作。

* void onLoadFailed(Exception e, Drawable errorDrawable) 

  加载失败回调，根据需求，可在当前方法中进行图片加载失败的后续操作。

* void onLoadStarted(Drawable placeholder) 

  开始加载图片，可在当前方法中进行加载图片的初始操作。比如，显示加载进度条。

* void onResourceReady(Z resource, GlideAnimation<? super Z> glideAnimation) 

  当前方法表示图片资源加载完成。

* Drawable getCurrentDrawable()

​        获取当前显示的 Drawable 

* void setDrawable(Drawable drawable) 

  设置 drawable 显示在当前 ImageView 

#### ScaleType属性

| 属性值        | 作用                                                         |
| ------------- | ------------------------------------------------------------ |
| MATRIX        | 使用setImageMatrix(Matrix),允许用户自定义缩放、平移、透视来变形 |
| FIT_XY        | 缩小或放大图片，充满控件，不裁剪，可能会改变图片纵横比       |
| FIT_START     | 缩小或放大图片，未充满控件，保证纵横比，从原点开始，保证长宽有一个是完整的 |
| FIT_CENTER    | 缩小或放大图片，未充满控件，保证纵横比，保证长宽有一个是完整的，显示在中间 |
| FIT_END       | 缩小或放大图片，未充满控件，保证纵横比，从底部右边开始，保证长宽有一个是完整 |
| CENTER        | 不缩放，充满控件，会裁剪，保证纵横比，显示在中央             |
| CENTER_CROP   | 缩放图片，保证纵横比，使长宽等于或大于视图的尺寸，显示在中央，会剪裁 |
| CENTER_INSIDE | 缩放图片，保证纵横比，，使长宽等于或小于视图的尺寸，显示在中央 |

* MATRIX 

  `MATRIX`是一个矩阵类，是用来对图片进行缩放、平移、旋转等操作，主要有：

  * translate 平移

    ```java
    mMaritx.setTranslate（float dx,float dy）
    ```

  * Rotate 旋转

    ```java
    mMaritx.setRotate(float degrees, float px, float py)//旋转角度，旋转中心
    ```

  * scale 缩放

    ```java
    mMartix.setScale(float sx, float sy,float px,float py) //
    ```

  * skew 倾斜

    ```java
    mMartix.setSkew(float sx, float sy,float px,float py) //
    ```

​    矩阵的乘法是只有左乘的概念，需要`(m*n)*(n*m) `,但是由于mMartix是一个3*3的矩阵，因此可以左乘和右乘，这种概念毫无用处，只是因为它提供的Api中有`post`和`pre`，`post`是指当前矩阵左乘参数矩阵，`pre`是指参数矩阵乘以当前矩阵，因此如果你的变化是连续的，需要分清先后顺序。

```java
//源码解析
if (ScaleType.MATRIX == mScaleType) {
  // Use the specified matrix as-is.
  if (mMatrix.isIdentity()) {
    mDrawMatrix = null;
  } else {
    mDrawMatrix = mMatrix;
  }
```

由此可以见到，其是将参数矩阵`mMatrix`给了`mDrawMatrix`,然后在`Draw()`时调用`mDrawMatrix`.

使用时，需要先将矩阵填入，再使用类型

```java
setImageMatrix(mMatrix);
setScaleType(ScaleType.MATRIX);
```

setScaleType时会重新刷新。

* FIT_XY     

* FIT_START

* FIT_CENTER

* FIT_END

  以上方法时，使用了JNI，没有查到源码

* CENTER 

  ```java
  //vwidth 控件宽  vheight 控件高 //dwidth 图片宽 //dheight 图片高
  if (ScaleType.CENTER == mScaleType) {
                  // Center bitmap in view, no scaling.
                  mDrawMatrix = mMatrix;
                  mDrawMatrix.setTranslate(Math.round((vwidth - dwidth) * 0.5f),
                                           Math.round((vheight - dheight) * 0.5f));
  ```

  由源码可知，只是将其进行了平移，移动到控件的中间。

* CENTER_CROP

  这个我理解了很久，使用代数法进行了一波实验，保证有一边是可以完全展示出来的，另一边是超过控件大小的，最后再平移到控件中间，但是它对于裁边的处理，是通过图片宽高比和控件的宽高比进行比较，得到应该哪边完全展示出来。

  ```java
  //vwidth 控件宽  vheight 控件高 //dwidth 图片宽 //dheight 图片高
  if (ScaleType.CENTER_CROP == mScaleType) {
    mDrawMatrix = mMatrix;
    float scale;
    float dx = 0, dy = 0;
    if (dwidth * vheight > vwidth * dheight) {
      scale = (float) vheight / (float) dheight;
      dx = (vwidth - dwidth * scale) * 0.5f;
    } else {
      scale = (float) vwidth / (float) dwidth;
      dy = (vheight - dheight * scale) * 0.5f;
    }
    mDrawMatrix.setScale(scale, scale);
    mDrawMatrix.postTranslate(Math.round(dx), Math.round(dy));
  ```

  可以这么理解，图片宽高比>控件宽高比，图片高度是可以保证通过缩放完全显示，宽边会超过控件宽度，反之则是图片宽度是可以保证通过缩放完全显示，高度会超过控件高度。

* CENTER_INSIDE

  * 当图片宽高都小于控件的宽高时，则不会缩放
  * 当图片宽高有一边大于控件的宽高时，则会选择长边的缩放比例，保证长边充满控件，而短边完全显示却不充满控件
  * 最后平移到中间

  ```java
  if (ScaleType.CENTER_INSIDE == mScaleType) {
    mDrawMatrix = mMatrix;
    float scale;
    float dx;
    float dy;
  
    if (dwidth <= vwidth && dheight <= vheight) {
      scale = 1.0f;
    } else {
      scale = Math.min((float) vwidth / (float) dwidth,
                       (float) vheight / (float) dheight);
    }
  
    dx = Math.round((vwidth - dwidth * scale) * 0.5f);
    dy = Math.round((vheight - dheight * scale) * 0.5f);
  
    mDrawMatrix.setScale(scale, scale);
    mDrawMatrix.postTranslate(dx, dy);
  ```



## View UI 布局

### ScrollView

用于处理组成界面不规则的、竖直方向长度不够的界面，区别与ListView只处理规则组成的界面。

1、ScrollView的直接子View只能有一个。

2、使用`layout_width`和`layout_height`给`ScrollView`指定大小。

3、ScrollView只用来处理需要滚动的不规则视图的组合。大批量的列表数据展示可以使用ListView、GridView或者RecyclerView。

4、ScrollView和ListView之类的嵌套使用时会有滑动冲突。不到不得已不要使用。

5、水平滑动使用`HorizontalScrollView`。

*  android:fillViewport     是否可以拉伸其内容填满Viewport

