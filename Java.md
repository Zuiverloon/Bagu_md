# Java

## 哪种 String 存在常量池，哪种存在堆

```java
String s1 = "helloworld";//常量池
String s2 = new String("helloworld");//堆
```

## 常量(final 关键字)

```java
final int con = 1;
```

## final 类&方法

final 类不能被继承如 String 类  
final 方法不能被重写，但可以重载  
final 变量一旦赋值不可改变

## 作为类变量时的初始化

```java
int i; // 0;
String s; // null
//如果是final变量，必须在构造函数中或直接初始化
```

## 开辟数组会初始化

```java
int a[] = new int[10];//初始化为0
```

## 访问修饰符

public: 任何  
protected: 包内相当于 public，包外只能被继承的类访问  
private: 类外不能访问，即使被继承也不能  
package: 在包内可访问，在包外不可访问，不写默认是 package

## upcasting(子类转父类)/downcasting(父类转子类)

```java
Parent p = new Child();//upcasting

Parent p = new Child();
Child c = (Child)p;//downcasting
```

## 多态 polymorphism

运行期间才确定对象的类型  
优点：对于子类对象都可以用父类变量去管理，多个不同子类的对象可以存在同一个父类数组中

## 模版

类型参数化，编译时确定类型（在字节码层面的本质是使用了 Object 类，如果加了限制类如 Number，那就是限制类）

## 抽象类

抽象类不能被实例化  
抽象类不一定包含抽象方法，也可以包含实现的方法，但若包含抽象方法的类了必须是抽象类  
抽象方法只有声明没有实现  
构造方法，类方法(static 修饰的)不能是抽象  
子类必须实现抽象方法，除非子类是抽象类

## 接口

接口的方法默认是 public
接口只能有 static 和 final 变量

## 接口和抽象类的区别

1. 抽象类可以有抽象方法也可以没有，继承抽象类的子类如果是抽象类，可以只实现部分，而接口全是抽象方法(1.8 开始可以 static 方法)
2. 接口可以继承多个接口，但是类不能多继承
3. 接口只有 static 和 final 变量而抽象类任何变量全都可以
4. 接口是被实现，抽象类不能被实现
5. 接口的方法默认全是 public

## RuntimeError 和其他 error

runtime:程序员的错误如下标越界，可以不写 trycatch 而是抛出给 jvm  
其他如 IO:外部错误，代码没错，必须要写 try catch(io 错误必须回去把文件关了)

## 为什么要 iterator？

有些集合是无序的如 set，需要迭代器才能访问所有元素  
可以不关心集合对象内部细节的同时，访问所有元素

## IO

BIO 同步阻塞 对每个连接都启动一个线程去服务  
NIO 同步非阻塞 所有连接打到多路复用器 有请求再启动一个线程去服务  
AIO 异步非阻塞 把数据放在缓冲区中 操作系统通知应用程序来取数据

## HashMap && HashTable && ConcurrentHashMap && TreeMap

HashMap:1.8 以前是数组(桶)+链表，1.8 以后是数组+红黑树，默认 16 个桶，元素数量超过 loadfactor 0.75\*桶 就扩容一倍  
HashTable:类似于 hashmap，不支持 null 为 key 且线程安全(synchronized)  
Con:volatile 关键词(操作后会被别的线程立即看见)，可以并行操作不同的 bucket，如果是同一个 bucket 就加锁控制  
TreeMap:也是存键值对，红黑树，排序遍历较快

## 死锁

死锁条件:资源独占(mutual exclusive)，请求保持(hold and wait)，环路等待(circular waiting)，不可强夺(no-preemption)

## 设计模式

**工厂模式**：传递类型信息获取不同类的对象

```java
public class ShapeFactory {

   //使用 getShape 方法获取形状类型的对象
   public Shape getShape(String shapeType){
      if(shapeType == null){
         return null;
      }
      if(shapeType.equalsIgnoreCase("CIRCLE")){
         return new Circle();
      } else if(shapeType.equalsIgnoreCase("RECTANGLE")){
         return new Rectangle();
      } else if(shapeType.equalsIgnoreCase("SQUARE")){
         return new Square();
      }
      return null;
   }
}
```

**单例模式**：一个类仅有一个实例对象，提供一个访问他的 getinstance 方法，类中有一个 static 实例
懒汉：第一次获取时创建实例
饿汉：成员变量创建实例

```java
public class SingleObject {

   //创建 SingleObject 的一个对象
   private static SingleObject instance = new SingleObject();

   //让构造函数为 private，这样该类就不会被实例化
   private SingleObject(){}

   //获取唯一可用的对象
   public static SingleObject getInstance(){
      return instance;
   }//如果是懒汉式，可以这里再初始化一个新实例，并且用synchronized保证并发

}
```

**代理模式**：创建现有对象的代理类，向外界提供接口（可解决远程代理问题，需要的对象在远程，在本地通过代理访问）

```java
public interface Image {
   void display();
}
public class RealImage implements Image {

   private String fileName;

   public RealImage(String fileName){
      this.fileName = fileName;
      loadFromDisk(fileName);
   }

   @Override
   public void display() {
      System.out.println("Displaying " + fileName);
   }

   private void loadFromDisk(String fileName){
      System.out.println("Loading " + fileName);
   }
}
public class ProxyImage implements Image{

   private RealImage realImage;
   private String fileName;

   public ProxyImage(String fileName){
      this.fileName = fileName;
   }

   @Override
   public void display() {
      if(realImage == null){
         realImage = new RealImage(fileName);
      }
      realImage.display();
   }
}
```

## java 反射机制

在运行状态中，对任意一个类，都可以知道类的所有属性和方法，动态获取对象/调用方法

```java
Class clazz = Class.forName("...");//获取类
clazz.getDeclaredConstructor();//获取构造函数
clazz.getField();//获取变量
clazz.getMethod;//获取方法
clazz.set(Object,value);//设置变量的属性
clazz.invoke(Object,args);//调用方法
```

**动态代理**
每一个动态代理类的调用处理程序都必须实现 InvocationHandler 接口,通过 Proxy 类创建代理对象

```java
public class Teacher implements People{
    @Override
    public String work() {
        System.out.println("老师教书育人...");
        return "教书";
    }
}
public class WorkHandler implements InvocationHandler{

    //代理类中的真实对象
    private Object obj;

    public WorkHandler() {
        // TODO Auto-generated constructor stub
    }
    //构造函数，给我们的真实对象赋值
    public WorkHandler(Object obj) {
        this.obj = obj;
    }
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        //在真实的对象执行之前我们可以添加自己的操作
        System.out.println("before invoke。。。");
        Object invoke = method.invoke(obj, args);
        //在真实的对象执行之后我们可以添加自己的操作
        System.out.println("after invoke。。。");
        return invoke;
    }
}
public class Test {

    public static void main(String[] args) {
        //要代理的真实对象
        People people = new Teacher();
        //代理对象的调用处理程序，我们将要代理的真实对象传入代理对象的调用处理的构造函数中，最终代理对象的调用处理程序会调用真实对象的方法
        InvocationHandler handler = new WorkHandler(people);
        /**
         * 通过Proxy类的newProxyInstance方法创建代理对象，我们来看下方法中的参数
         * 第一个参数：people.getClass().getClassLoader()，使用handler对象的classloader对象来加载我们的代理对象
         * 第二个参数：people.getClass().getInterfaces()，这里为代理类提供的接口是真实对象实现的接口，这样代理对象就能像真实对象一样调用接口中的所有方法
         * 第三个参数：handler，我们将代理对象关联到上面的InvocationHandler对象上
         */
        People proxy = (People)Proxy.newProxyInstance(handler.getClass().getClassLoader(), people.getClass().getInterfaces(), handler);
        //System.out.println(proxy.toString());
        System.out.println(proxy.work());
    }
}
```

## 线程

### 创建线程的三种方法

1.  继承 Thread，重写 run

```java
class A extends Thread{
    @Override
    public void run(){
        System.out.println("A");
    }
}

    //也可以用lambda表达式
    Thread t = new Thread(()->{
        System.out.println("haha");});
    t.start();//调用start方法才是启动了线程
```

1.  实现 Runnable，重写 run

```java
class B implements Runnable{
    @Override
    public void run(){
        System.out.println("B");
    }
}
    Thread t2 = new Thread(new B());//作为参数传入Thread对象
    t2.start();
```

1.  实现 callable 接口,重写 call

```java
class C implements Callable<String> {
    @Override
    public String call(){
        System.out.println("C");
        return "C";
    }
}

    Thread t3 = new Thread(new FutureTask<String>(new C()));
    t3.start();
```

### callable 和 runnable 的区别

Runnable 不返回结果(返回空 future)或抛出异常  
Callable 返回 future，且若有异常在 Future.get()时会抛出异常，通过 e.getCause()获得异常  
两个都可以用 executorService 来执行（就是线程池）

## 线程池

### 线程池参数：

1. 核心线程数(core):任务来时，如果线程数小于核心线程数，则直接创建线程执行
1. 最大线程数(max):如果队列满了，有新任务，创建新线程
1. 存活时间(keepalivetime)及单位:超过存活时间并且线程数大于核心线程数就 kill
1. 等待队列(waitingqueue):若线程数等于核心线程数，丢进队列，等到有空闲线程了就取第一个任务执行
1. 拒绝策略(abortstrategy):一般的，丢弃新任务/最老任务/ 由提交者线程执行/抛出异常
1. 线程工厂:接受 runnable 对象，封装到 Thread 类中

### 创建线程池的两种方法

1.  调用 executor 创建四种中的一种  
    a. 单核心线程池

    ```java
    ExecutorService singleThreadPool = Executors.newSingleThreadExecutor();//只有一个线程执行，可按照(FIFO，LIFO，优先级)来执行，等待队列长度无界
    ```

    b. 可缓存线程池

    ```java
    ExecutorService newCachedPool = Executors.newCachedThreadPool(); //最大线程无穷大，会复用之前开的线程，可能导致爆内存
    ```

    c. 定长的线程池

    ```java
    ExecutorService fixedThreadPool = Executors.newFixedThreadPool(2);//核心线程和最大线程都是n，也是无界队列
    ```

    d. 周期性线程池

    ```java
    ExecutorService scheduledThreadPool = Executors.newScheduledThreadPool(2);//可延时或定期的执行任务(调不同方法决定是延时还是定时)
    ```

1.  调用 threadpoolexecutor 创建自定义所有参数的线程池

### 线程池提交任务的两种方法 execute&submit

1. execute 方法提交不需要返回值的任务，无法判断是否执行完成
2. submit 方法提交需要返回值的任务，返回一个 Future 对象，通过 Future.get 来判断是否执行完成(get 会阻塞直到执行完成)
