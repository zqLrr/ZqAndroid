# 12 Otto库、IPCOtto库的使用

## Otto库

> Otto 是一种事件总线，旨在解耦应用程序的不同部分，同时仍允许它们有效通信。
>
> Otto 从 Guava 衍生而来，为已经完善的事件总线添加了独特的功能，并将其专门用于 Android 平台。

### 1. 什么是事件总线

> 为了实现发布者和订阅者之间的匿名，需要一个中间人进行沟通，事件总线就是这样的一个中间人。

Android 中常用的两个事件总线库是Otto和EventBus,现在推荐的是Rxjava-Rxbus。

### 2.什么时候使用Otto库

现在官方推荐使用Rxjava-RxBus，因为otto库已经不再使用，但是建议如果只是使用事件总线可以使用Otto，因为其注解比较清晰好用，若是App中已存在Rxjava，不想添加新的库可以使用Rxjava-Rxbus。

## Otto的使用

消息类

> 用来存储发布者和订阅者通信的内容

Bus类

> Otto库的主要功能通过Bus类来使用，需要注册和解注册

@Subscibe

> Otto库订阅者功能的注解

post

> Bus发布事件的函数

@Produce

> Otto库生产者的注解，当Bus在注册的时候，调用该注解来发布事件

* 实例

```java
//消息类
public class BusData {
    public BusData(String message) {
        this.message = message;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

    public String message;

}
//Bus单例
public class OttoBus extends Bus {

    public OttoBus() {
    }

    private static class BusIntance{
       private final  static  OttoBus  mInstance = new OttoBus();
    }

    public static OttoBus getInstance(){
        return BusIntance.mInstance;
    }
}
//发布订阅
public class OttoActivity extends AppCompatActivity {

    private OttoBus bus;
    private TextView mText;
    private EditText mSendText;
    private Button bt_send;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_otto);

        mText = findViewById(R.id.txt_receive);
        mSendText = findViewById(R.id.et_send);
        bt_send = findViewById(R.id.bt_send);
        bt_send.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                BusData data = new BusData(mSendText.getText().toString());
              //发布事件
                bus.post(data);
            }
        });
        //注册Otto bus
        bus = OttoBus.getInstance();
        bus.register(this);
    }

  //@Produe 发布事件
    @Produce
    public BusData postMessage(){
        return new BusData("使用Produce注解");
    }

  //@Produce 订阅事件
    @Subscribe
    public void setMessage(BusData data){
        mText.setText(data.getMessage());
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        //解注册bus
        bus.unregister(this);
    }
}
```

3、ipcotto库的使用

> 结合humers实现了跨进程的事件总线，可以实现不同进程的发布订阅



注意事项：

1.Otto在父类中注册是没问题的，但是订阅写在父类是无法收到的





