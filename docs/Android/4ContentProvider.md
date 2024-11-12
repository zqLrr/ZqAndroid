# ContentProvider

>  数据共享标准，Android四大组件之一,可以实现进程间通信

## ContentProvider注册

* 新建ContentProvider子类
* 在Android中注册ContentProvider
  * authorities  相当于ContentProvider的域名
  * name 实现类的类名
  * exported:是否被允许被其他应用调用
  * readPermission:指定读取该ContentProvider的权限。
  * writePermission:指定写入该ContentProvider的权限。
  * permission：配置了读取和写入的权限

## ContentProvider主要方法

* ```java
  <-- 4个核心方法 -->
    public Uri insert(Uri uri, ContentValues values) 
    // 外部进程向 ContentProvider 中添加数据
  
    public int delete(Uri uri, String selection, String[] selectionArgs) 
    // 外部进程 删除 ContentProvider 中的数据
  
    public int update(Uri uri, ContentValues values, String selection, String[] selectionArgs)
    // 外部进程更新 ContentProvider 中的数据
  
    public Cursor query(Uri uri, String[] projection, String selection, String[] selectionArgs,  String sortOrder)　 
    // 外部应用 获取 ContentProvider 中的数据
  
  // 注：
    // 1. 上述4个方法由外部进程回调，并运行在ContentProvider进程的Binder线程池中（不是主线程）
   // 2. 存在多线程并发访问，需要实现线程同步
     // a. 若ContentProvider的数据存储方式是使用SQLite & 一个，则不需要，因为SQLite内部实现好了线程同步，若是多个SQLite则需要，因为SQL对象之间无法进行线程同步
    // b. 若ContentProvider的数据存储方式是内存，则需要自己实现线程同步
  
  <-- 2个其他方法 -->
  public boolean onCreate() 
  // ContentProvider创建后 或 打开系统后其它进程第一次访问该ContentProvider时 由系统进行调用
  // 注：运行在ContentProvider进程的主线程，故不能做耗时操作
  
  public String getType(Uri uri)
  // 得到数据类型，即返回当前 Url 所代表数据的MIME类型
  //模糊查询时，将'%'放到参数里，而不是放在Where中
  ```

## Uri

```java
自定义URI = Content://com.carson.provider/User/1
```

* 主题名： content:// (Android规定)

* 授权信息：//com.carson.provider

* 资源部分：/User/1

  由于数据可以来源于表格、xml、网格等，所以资源部分写法不统一

## ContentResolve

   ContentResolve是操纵数据的，而ContentProvider是负责接收处理数据的，因而两者方法相同。

* getContextResolve() 获取ContextResolve的对象
* insert(Uri,ContentValues)
* delete(Uri,String where,String[] selectionArgs)
* update(Uri,ContentValues values,String Where,String[] selectionArgs)
* query()

## ContentProvider、ContentResolve和Uri的关系和使用规则

![image-20210708213559737](/Users/zhangqian20/git profile/ZqAndroid/docs/Android/Android 基础.assets/image-20210708213559737.png)

因此，ContentResolve根据Uri间接调用ContenProvider.

## UriMatcher工具类

* void addURI(String authority,string path,int code) 给UriMatcher注册标识码
* int match(Uri uri)  返回标识码

## ContentUris工具类

* withAppendedId(uri,id):用于为路径加上id部分
* parseId(uri):用于从指定Uri中解析出所包含的ID值

## 监听ContentProvider变化

当`ContentProvider` 中的数据发生变化（增、删 & 改）时，就会触发该 `ContentObserver`类

### ContentProvider发送变化通知

```java
this.getContext().getContentResolver().notifyChange(URI,null);
```

### 注册监听

```java
context.getContentResolver().registerContentObserver(uri,true,new ContentObserver(new Handler()));
```

创建内部类继承ContentObserver并重写下面两个方法：

(1)带有Handler的构造函数；

(2)public void onChange(boolean selfChange); 此函数在内容提供者发出改变信号时就会被调用

参数selfChange为如果是自己改变的原因，则为true；如果不是自己改变的，则为false；

## MIME数据类型

作用：指定某个扩展名的文件用某种应用程序来打开

MIME有两种形式

```cpp
// 形式1：单条记录  
vnd.android.cursor.item/自定义
// 形式2：多条记录（集合）
vnd.android.cursor.dir/自定义 
```

## ContentProvider的启动流程

![d125lt](/Users/zhangqian20/git profile/ZqAndroid/docs/png/d125lt.png)

## ContentProvider 请求流程

主要分三级：本地-》AMS-》含有该ContentProvider的进程

![ContentProvider 请求流程](/Users/zhangqian20/git profile/ZqAndroid/docs/png/d14kb6.png)