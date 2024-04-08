# ContentProvider

## 监听ContentProvider

发送变化通知

```java
this.getContext().getContentResolver().notifyChange(URI,null);
```

注册监听

```java
context.getContentResolver().registerContentObserver(uri,true,new ContentObserver(new Handler()));
```

创建内部类继承ContentObserver并重写下面两个方法：
(1)带有Handler的构造函数；
(2)public void onChange(boolean selfChange); 此函数在内容提供者发出改变信号时就会被调用
参数selfChange为如果是自己改变的原因，则为true；如果不是自己改变的，则为false；

