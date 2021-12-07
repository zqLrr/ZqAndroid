## Java基础学习

## 1、asyncTask

1. 使用原因：在后台进行处理耗时操作。

AsyncTask类允许定义将在后台执行的操作，并提供了可以用来监控进度及在GUI线程上发布结果的处理程序。

2. 基本原理

   * 它可以用来创建一个异步任务，该任务由两部分组成：将在后台执行的处理，以及在处理完成后执行的UI更新。

   * 是一个抽象类，有三个泛型参数

     * Params. 启动任务执行的输入参数，比如HTTP请求的URL。(输入参数)

     * Progress 后台任务执行的百分比。(报告进度的值)

     * Result. 返回结果的值

       三者不是必需的，不想使用可以直接指定为void. 

   * 执行分为四个步骤，每一步对应一个回调方法

   * 需要子类化

   * 需要实现下面的一个或四个方法

     * onPreExecute():该方法将在执行实际的后台操作前被UI thread调用。可以在该方法中做一些准备工作，例如在界面上显示一个进度条。
     * doInBackground(Params):将在onPreExecute方法执行后马上执行，该方法运行在后台线程中。这里将主要负责执行那些很耗时的后台计算工作。可以调用publishProgress方法来更新实时的任务进度。该方法是抽象方法，子类必须实现。
     * onProgressUpdate(Progress):UI thread将调用这个方法从而在界面上展示任务的进展情况，例如通过一个进度条进行展示。
     * onPostExecute(Result):在doInBackground执行完成后，onPostExecute()方法将被UI thread调用，后台的计算结果将通过该方法传递到UI thread

3. 注意要点：

   * Task的实例必须在UI线程创建
   * execute()方法必须在UI线程被创建
   * 不要手动调用这四个方法
   * 该Task只能执行一次，多次调用会出错

## 2、Synchronized 锁机制的原理

* 是一种独占式的重量级锁，在运行到同步方法或者同步代码块的时候，让程序的运行级别由用户态切换到内核态，把所有的线程挂起，通过操作系统的指令，去调度线程。

* 实现原理：

  * 在JVM中实现，基于进入和退出Monitor对象来实现方法和代码块的同步

  * 同步代码块：

    monitorenter指令插入到同步代码块的开始位置，monitorexit指令插入到同步代码块的结束位置，JVM需要保证每一个monitorenter都有一个monitorexit与之相对应。任何对象都有一个monitor与之相关联，当且一个monitor被持有之后，他将处于锁定状态。线程执行到monitorenter指令时，将会尝试获取对象所对应的monitor所有权，即尝试获取对象的锁；

  * 同步方法：

* 作用：
  * 确保线程互斥的访问同步代码
  * 保证共享变量的修改能及时看见
  * 有效解决重排序问题

* 锁机制相关原理：

  * 自旋概念：阻塞时挂起线程和恢复线程在操作系统太浪费时间，使其线程稍微等一会，但也有等待时间限制，一般为自旋10次。
  * 锁消除：所使用的资源没有其他线程用，消除锁
  * 锁粗化：同时对一个对象反复加锁和解锁，扩大锁的范围
  * 锁的状态（只能由低到高）：
    * 无锁状态
    * 偏向锁状态
    * 轻量级锁状态
    * 重量级锁状态

  ![image-20210729120821220](Java基础学习.assets/image-20210729120821220.png)

## 3、单例模式

1. 推荐使用单例的方法(Lazy loading、线程安全)

   * 双重检查

     ```java
     public class Singleton(){
       private static volatile Singleton singleton;
       private Singleton(){}
       public static Singleton getInstance(){
         if(singleton == null){
             synchronized (Singleton.class){
               if(singleton ==null){
                 singleton = new Singleton();
               }
             }
         }
         return singleton;
       }
     }
     ```

   * 静态内部类实现单例

     ```java
     public class Singleton(){
       private Singleton(){}
       private static class SingletonInstance(){
          private final static Singleton INSTANCE = new Singleton();
       }
       public Singleton getInstance(){
         return SingletonInstance.INSTANCE;
       }
     }
     ```

2. 优点：系统内存中该类只存在一个对象，节省了系统资源，对于一些需要频繁创建销毁的对象，使用单例模式可以提高系统性能。

3. 缺点：当想实例化一个单例类的时候，必须要记住使用相应的获取对象的方法，而不是使用new，可能会给其他开发人员造成困扰，特别是看不到源码的时候。

4. 使用场景：

   * 需要频繁的进行创建和销毁的对象
   * 创建对象时耗时过多或耗费资源过多，但又经常用到的对象
   * 工具类对象
   * 频繁访问数据库或文件的对象。

## 4、工厂设计模式

简单工厂模式   工厂方法模式 抽象工厂模式

1. ## 简单工厂模式

   ![image-20210901185056250](Java基础学习.assets/image-20210901185056250.png)

   简单工厂模式可以说是一种编程习惯，即将一些复杂的方法抽到函数或类中，减轻了本身的代码量。

2. ## 工厂方法模式

![image-20210901185405903](Java基础学习.assets/image-20210901185405903.png)

每一个产品都安排一个工厂去实现

3、抽象工程模式

![image-20210901204246565](Java基础学习.assets/image-20210901204246565.png)

工厂可以实现多个产品，产品也需要多个抽象类

## 5、函数式接口的用法 

* Function函数常用入口

  | name           | type             | description                     |
  | -------------- | ---------------- | ------------------------------- |
  | Consumer       | Consumer< T >    | 接收T对象，不返回值             |
  | Predicate      | Predicate< T >   | 接收T对象并返回boolean          |
  | Function       | Function< T, R > | 接收T对象，返回R对象            |
  | Supplier       | Supplier< T >    | 提供T对象（例如工厂），不接收值 |
  | UnaryOperator  | UnaryOperator    | 接收T对象，返回T对象            |
  | BinaryOperator | BinaryOperator   | 接收两个T对象，返回T对象        |

* Function常用方法使用：apply andThen compose

  * apply  输入T，执行操作后，返回R 

    ```java
    import java.util.function.Function;
    
    public class FunctionTest<In, Out> {
    
        private Function<In, Out> processor = new Function<In, Out>() {
            @Override
            public Out apply(In in) {
                return (Out) new String("apply:" + in);
    
            }
        };
    
        public static void main(String[] args) {
            FunctionTest<String, String> functionTest = new FunctionTest();
            System.out.println(functionTest.processor.apply("hello~!"));
        }
    }
    
    //输出
    apply:hello~!
    ```

  * apply lambda

    ```java
    import java.util.function.Function;
    
    public class FunctionTest<In, Out> {
    
        private Function<In, Out> processor = in -> {
            return (Out) new String("apply:" + in);
        };
    
        public static void main(String[] args) {
            FunctionTest<String, String> functionTest = new FunctionTest();
            System.out.println(functionTest.processor.apply("hello~!"));
        }
    }
    //输出和上面一样
    ```

  * andThen   先执行apply,再执行andThen

    ```java
    Function<Integer, Integer> name = e -> e * 2;
    Function<Integer, Integer> square = e -> e * e;
    int value = name.andThen(square).apply(3);
    System.out.println("andThen value=" + value);
    //输出 value=36
    ```

  * compose  先执行compose,再执行apply
    ```java
    int value2 = name.compose(square).apply(3);
    System.out.println("compose value2=" + value2);
    //输出value2=18
    ```

  * 返回一个执行了apply()方法之后只会返回输入参数的函数对象

    ```java
    Object identity = Function.identity().apply("huohuo");
    System.out.println(identity);
    ```


## 6、控制反转(IOC)和依赖注入DI 

* IOC：是一种编程思想，是将设计好的对象交给容器控制，而不是传统的在你的对象内部控制。

  传统的编程思路(将其称为正转)：

  ![image-20211027183226279](Java基础学习.assets/image-20211027183226279.png)

​        由上图可以看出，我们在正常编程的时候，需要自己去手动创建我们要使用的类，如果有依赖，就要将依赖的类也创建出来。

​                IOC的编程思路：      

<img src="Java基础学习.assets/image-20211027184121528.png" alt="image-20211027184121528" style="zoom:50%;" />

​         由上图可以看出，我们编程时由IOC容器去帮我门查找和提供用户类及其依赖的类，我们是被动的接受对象。

​         **IoC对编程带来的最大改变不是从代码上，而是从思想上，发生了“主从换位”的变化。应用程序原本是老大，要获取什么资源都是主动出击，但是在IoC/DI思想中，应用程序就变成被动的了，被动的等待IoC容器来创建并注入它所需要的资源了。**

　　**IoC很好的体现了面向对象设计法则之一—— 好莱坞法则：“别找我们，我们找你”；即由IoC容器帮对象找相应的依赖对象并注入，而不是由对象主动去找。**

* DI:其实和IOC 是一回事，只是换了一个角度说明，重点说明：“**被注入对象依赖IoC容器配置依赖对象**”

　   **IoC的一个重点是在系统运行中，动态的向某个对象提供它所需要的其他对象。这一点是通过DI（Dependency Injection，依赖注入）来实现的**

## 7、Java序列化

* 概念

  java序列化:保存内存对象的状态，包括对象的属性值,但不包括方法和static变量(因为static修饰的变量是属于类而不隶属于对象),以及用transient关键字修饰的变量(transient是禁止序列化的标识,效果等同于static修饰的变量).

  java反序列化:是与java序列化相对的,表示从磁盘或者其他介质中读取序列化对象的内容.

* 应用场景

  - 内存中的对象保存到磁盘文件中
  - 网络传输对象,比如Socket套接字传输
  - 通过RMI（Remote Method Invoke 远程方法调用）传输对象,如android中的AIDL

* 实现方式

  * 实现Serializable序列化的空接口

    ```java
    public class TestSerializable implements Serializable {//Serializable是一个空接口
        private static final  long serializableUID = 1L;//序列化标识，防止反序列化失败
        private transient  int  banSerializable; //禁止序列化
        private static  int banSeriallizable2; //禁止序列化
        //属性中的类也必须实现序列化，否则会报异常
    
        public String name;
        public int id;
    
        public TestSerializable() {
        }
    
        public TestSerializable(String name, int id) {
            this.name = name;
            this.id = id;
        }
    }
    ```

  * Parcelable接口，android特有的序列化存储,传输效率高,需要实现里面的抽象方式,实现起来比较麻烦.

    * 两个方法
    * 一个接口

    ```java
    public class TestParcelable  implements Parcelable {
        public  String  name;
        public  int     id;
        public TestParcelable(String name,int id){//构造函数
            this.name = name;
            this.id   = id;
        }
        
        protected TestParcelable(Parcel in) {//对象反序列化读取数据
            this.name = in.readString();
            this.id   = in.readInt();
        }
        
        @Override
        public int describeContents() { //内容接口描述，默认返回0
            return 0;
        }
    
        @Override
        public void writeToParcel(Parcel dest, int flags) {//将对象序列化一个Parcel对象，将对象存入Parcel中
            dest.writeString(name);
            dest.writeInt(id);
        }
        
        public static final Creator<TestParcelable> CREATOR = new Creator<TestParcelable>() {//实现的接口
            @Override
            public TestParcelable createFromParcel(Parcel in) {
                return new TestParcelable(in);
            }
    
            @Override
            public TestParcelable[] newArray(int size) {
                return new TestParcelable[size];
            }
        };
    
    }
    ```

* 区别

  Serializable : 虽然该接口实现比较简单，但是Serializable频繁的进行IO操作,在内存序列化上开销比较大。

  Parcelable： Parcelable性能比较好，在内存开销方面较小，在内存间数据传输推荐使用，但使用起来比较方便。
