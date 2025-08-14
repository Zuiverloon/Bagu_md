# Java

封装 encapsulation：把变量和方法绑在一起  
继承 inherit：inherit from base class  
多态 polymorphism：对于父类和自类，uses the same methods to perform different tasks

jdk(development kit):core package, JRE(JVM)  
JVM:runtime engine to run java program. heap, stack, PC. bytecode in JVM will be interpreted  
JRE(runtime env):enables a java program to run in different os.

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

## immutable class

class should be declared final  
members should be private & final  
constructor should initialize all field

## 作为类变量时的初始化

```java
int i; // 0;
String s; // null
//如果是final变量，必须在构造函数中或直接初始化
//如果是局部变量，会提示为初始化
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

## static

whether or not the class object is generated, we can have access to the static var or method.

## equals hashcode

if overwrite equals, must write hashcode. because if two obj equals, their hashcode must be equal.

## autobox & unbox

auto convert a primitive type var into a class object (int -> Integer)

## 包装类和基本类的区别

1. 包装类是引用类型，基本类是 primitive
2. 包装类有许多方法和属性，基本变量只有 value

## Arrays.sort

对 primitive 变量是 quicksort，对引用类型是 merge，因为 quicksort 是不稳定的，merge 是稳定的

## upcasting(子类转父类)/downcasting(父类转子类)

```java
Parent p = new Child();//upcasting

Parent p = new Child();
Child c = (Child)p;//downcasting
```

## 多态 polymorphism

对于父类和自类，uses the same methods to perform different tasks
运行期间才确定对象的类型  
优点：

1. 对于子类对象都可以用父类变量去管理，多个不同子类的对象可以存在同一个父类数组中
2. 可扩展性
3. 降低耦合(左边是父类，右边是子类)

需要显示重写父类方法

## 泛型

类型参数化，编译时确定类型（在字节码层面的本质是使用了 Object 类，如果加了限制类如 Number，那就是限制类）  
PECS 原则：往外读的用上界 extends，往里插入的用下界 super

```java

//上界<? extends T>不能往里存，只能往外取，取出来的对象只能放在基类中
List<? extends Fruit> p = new ArrayList<Apple>(new Apple());
p.add(new Fruit());// NO!
p.add(new Apple());// NO!

Fruit a = p.get(0);//只能放在基类中

//下界<? super T>能往里存，但是取的时候必须放在Object对象中
List<? super Fruit> p = new ArrayList<Fruit>(new Fruit());
p.add(new Apple());// OK
Apple a = p.get(0);// NO!
Object o = p.get(0);//OK
```

## 抽象类

不能被实例化(instantiated)  
不一定包含抽象方法，也可以包含实现的方法，但若包含抽象方法的类了必须是抽象类  
抽象方法只有声明没有实现  
构造方法，类方法(static 修饰的)不能是抽象  
子类必须实现抽象方法，除非子类是抽象类

## 接口(抽象方法的集合)

接口的方法默认是 public abstract
接口只能有 static 和 final 变量
(1.8 后接口里可以有 static 方法和方法体)

## 接口和抽象类的区别

1. 抽象类可以有抽象方法也可以没有，继承抽象类的子类如果是抽象类，可以只实现部分，而接口全是抽象方法(1.8 开始可以 static 方法)
2. 接口可以继承多个接口，但是类不能多继承
3. 接口只有 static 和 final 变量而抽象类任何变量全都可以
4. 接口是被实现(可以实现多个接口)，抽象类是被继承
5. 接口的方法默认全是 public

## RuntimeError 和其他 error

runtime:程序员的错误如下标越界，可以不写 trycatch 而是抛出给 jvm  
其他如 IO:外部错误，代码没错，必须要写 try catch(io 错误必须回去把文件关了)

## 为什么要 iterator？

有些集合是无序的如 set，需要迭代器才能访问所有元素  
可以不关心集合对象内部细节的同时，访问所有元素

## IO

BIO(per connection per thread) 同步阻塞 对每个连接都启动一个线程去服务且 acceptor 是阻塞的，accept 返回时新建线程去处理  
NIO(per request per thread) 同步非阻塞 所有连接打到多路复用器(selector) 有请求再启动一个线程去服务，selector 是阻塞的，可以用较少的线程来处理 selector 返回的请求，NIO 中所有读写都是在 buffer 中完成  
AIO(per request per thread) 异步非阻塞 把数据放在缓冲区中 操作系统通知应用程序来取数据

## HashMap && HashTable && ConcurrentHashMap && TreeMap

HashMap:1.8 以前是数组(桶)+链表(并发扩容会导致死循环，插入是头插，扩容会让链表反转)，1.8 以后是数组+红黑树，默认 16 个桶，元素数量超过 loadfactor 0.75\*桶 就扩容一倍  
HashTable:类似于 hashmap，不支持 null 为 key 且线程安全(synchronized)  
Con:volatile 关键词(操作后会被别的线程立即看见)，可以并行操作不同的 bucket，如果是同一个 bucket 就加锁控制  
TreeMap:也是存键值对，红黑树，排序遍历较快

## 锁

control the concurrent access to an object.

synchronized：关键字(与锁的功能类似但是不显式创建锁，加锁)，不可中断，非公平锁  
如果写 synchronized(this)或者把方法标记为 synchronized，会防止并发修改这个类上的所有变量，不太好，建议创建独立的 monitor
reentrant：类，调用 lock，unlock 实现加锁与释放可中断(lockinterrupt()方法可响应中断)，可公平(公平的话会看队列中是否有其他线程)

## 死锁

threads/processes are blocked because each thread is holding resources and waiting another resources held by others.

死锁条件:资源独占(mutual exclusive)，请求保持(hold and wait)，环路等待(circular waiting)，不可强夺(no-preemption)

## 设计模式

**工厂模式**：传递类型信息获取不同子类的对象
用于解耦(decoupling)

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
懒汉(eager)：第一次获取时创建实例
饿汉(lazy)：当类被加载到 jvm 时创建实例
可用于数据库连接 session，log 对象等
目的是避免不一致

```java
public class SingleObject {

   //创建 SingleObject 的一个对象
   private static SingleObject instance = new SingleObject();

   //让构造函数为 private，这样该类就不会被实例化
   private SingleObject(){}

   //获取唯一可用的对象
   public static SingleObject getInstance(){
    if (instance == null){
        instance = new SingleObject();
        return instance;
    }
      return instance;
   }//如果是懒汉式，可以这里再初始化一个新实例，并且用synchronized保证并发

}
```

**建造者模式**：使用多个简单的对象构建出一个复杂对象（McDonald Vegmeal() normalMeal()）

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

## java 反射机制 reflection

在运行状态中，对任意一个类，都可以知道类的所有属性和方法，动态获取对象/调用方法

```java
Class clazz = Class.forName("...");//获取类
clazz.getDeclaredConstructor();//获取构造函数
clazz.getField();//获取变量
clazz.getMethod;//获取方法
clazz.set(Object,value);//设置变量的属性
clazz.invoke(Object,args);//调用方法
```

**三种方法获得类**

```java
F f = new F();
Class c1 = F.class;//任何类有一个隐含的静态成员变量class
Class c2 = f.getClass();
try{
    Class c3 = Class.forName("全类名")
} catch(ClassNotFoundException e){

}
```

**动态代理**
每一个动态代理类的调用处理程序都必须实现 InvocationHandler 接口,通过 Proxy 类创建代理对象
流程：将真实对象传入一个实现了 invocationhandler 接口的 handler 类中，从 handler 类中获得一个代理对象

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

### 内存模型

本地方法栈：执行非 java 方法
PC：程序计数器，记录下一条指令位置
虚拟机栈：各种线程的栈空间，用于函数调用，本地变量，返回地址
堆：所有 object 和数组，GC 管理，分为新生代+老年代
方法区（元空间/永久代）：存放 class metadata，方法和静态变量，但是元空间位于本地内存，永久代也不在堆上

堆外内存：不由 GC 管理的，需手动申请释放的本地内存

```java
ByteBuffer buffer = ByteBuffer.allocateDirect(1024); // Allocates 1KB off-heap
buffer.putInt(42);
buffer.flip();
System.out.println(buffer.getInt()); // Outputs: 42

((DirectBuffer) buffer).cleaner().clean();
```

### 内存管理

| s                | jdk8-                                                        | jdk8+                                               |
| ---------------- | ------------------------------------------------------------ | --------------------------------------------------- |
| class metadata   | 存在永久代 PermGen                                           | 存在元空间 metaspace                                |
| 永久代特征       | 大小固定，会 OOM                                             | 大小不固定                                          |
| 堆结构           | 新生代+老年代+永久代（不在堆而在方法区，只有 hotspot VM 有） | 新生代+老年代+元空间(在本地内存中，不在 jvm 内存中) |
| 新生代 Young Gen | EDEN + survivor space(S0 + S1)                               | same                                                |
| 老年代 Old Gen   | 存活久的对象，清理他们需要 mark and swap                     | same                                                |
| GC               | minor(新生代)+major(老年代)+full                             | same，但是引入了新的 collector                      |
| GC 算法          | serial， parallel，CMS(Concurrent Mark Sweep)                | G1,ZGC,...                                          |
| 字符串池         | 位于永久代                                                   | 位于堆上                                            |
| 参数             | -XX:PermSize, -XX:MaxPermSize                                | -XX:MetaspaceSize, -XX:MaxMetaspaceSize             |

JDK8 关键改变：
引入元空间取消永久代  
元空间自动扩容  
GC 算法升级，使用 G1

## 垃圾回收

**如何 dereference**：把引用记为 null，把引用指向另一个对象，匿名变量(new String();)  
**垃圾回收 3 阶段**：标记对象(引用计数，可达性分析)、清除垃圾对象，压缩内存  
**引用计数**：统计指向对象的引用个数，弊端：需要额外空间记录，无法解决循环引用  
**可达性分析**：从 gc root(堆外指向堆内的引用，如局部变量，已加载类的静态变量) 开始，能访问的都标记为可达，问题：多线程环境下，已访问过的对象可能被其他线程修改  
在新生代触发的是 minor GC，在老年代触发的是 major GC  
新生代包括 eden space 和 s0，s1。eden 中都是新的对象，经过一次 gc（eden 空间满了会触发 minor gc） 后把 eden 和(s1/s0)存活的放入(s0/s1),保证 s1 和 s2 至少有一个是空的。当经过了一定次数后还存活的，就放入老年代。  
永久代：存放类方法。类不再被使用了。（为解决内存不够的问题，jkd8 开始永久代被替换成 metaspace，当空间满可以自动清理垃圾）

## Future vs Completeable Future

Future object represent the future result of an operation.
A Future is used for asynchronous Programming.(isDone() and get()).
CompletableFuture 是 future 的子类，can perform further actions after the async function returns.

```java
//异步方法有返回值
Supplier<Integer> task = ()->1;
CompletableFuture<Integer> future = CompletableFuture.supplyAsync(task);
future.thenRun(()->{System.out.println("haha")});
future.thenRunAsync(()->{System.out.println("haha")});//也可以异步跑
future.thenAccept(result->{
    System.out.println(result);
});//可以把返回值接着用，但是无返回结果，如果要返回结果，用thenapply。如果要接着执行异步方法并返回completableFuture就调用thenCompose
var first = CompletableFuture.supplyAsync(()->20);
var second = CompletableFuture.supplyAsync(()->0.9);
first.thenCombine(second,(a,b)->{
    return a*b;
});//用combine处理两个future结果

CompletableFuture.allOf(first,second,third).thenRun();//等三个都做完了，然后做别的
CompletableFuture.anyOf(first,second,third).thenRun();//等任何一个做完了，然后做别的

future.completeOnTimeout(1,1,TimeUnits.SECOND)//如果需要有一个超时时间,并返回默认值
future.onTimeout(1,TimeUnits.SECOND)//如果需要有一个超时时间,并不返回默认值，这两个都会返回一个新的future
//如果异步方法跑出异常，可以trycatch捕获然后调用future.exceptionally返回一个新的值/future对象

```

## volatile 关键字

如果一个线程修改了数据，别的线程可以立即看见
不依赖 cache 中的数据，而是每次都去内存查这个元素

## wait notify

每个 java 变量都有这两个方法(继承自 object)，当一个线程调用 wait，就会被阻塞，等到另外的线程调用了 notify
这两个方法的使用需要包在 synchronized(){}中

## atomic 类

atomicinteger 的自增用到了 CAS(compare and swap,CAS(V,O,N)核心思想为：若当前变量实际值 V 与期望的旧值 O 相同，则表明该变量没被其他线程进行修改，因此可以安全的将新值 N 赋值给变量,且 CAS 是原子的。

## LongAdder atomic 类的平替(如果线程很多，atomic 类的效率急剧下降)

在内存中维护了多个 counter，不同的线程更新不同的 counter，当获取具体值的时候，把所有 counter 加起来

## 多线程打印 1-100

用 synchronized，需要 wait/notify

```java
private static int counter = 0;
    private static int limit = 100;

    private static Object mutex = new Object();


    public static void main(String[] args) {
        for (int i = 0; i < limit; i++) {
            final int idx = i;
            new Thread(() -> {
                synchronized (mutex) {
                    while (counter <= idx) {
                        if (counter == idx) {
                            counter++;
                            System.out.println(idx);
                            mutex.notifyAll();
                            return;
                        } else {
                            try {
                                mutex.wait();
                            } catch (Exception e) {
                                e.printStackTrace();
                            }
                        }
                    }
                }
            }).start();
        }
    }
```

用 synchronized，不需要 wait/notify

```java
private static int counter = 0;
    private static int limit = 100;

    private static Object mutex = new Object();


    public static void main(String[] args) {
        for (int i = 0; i < limit; i++) {
            final int idx = i;
            new Thread(() -> {
                while (counter <= idx) {
                    synchronized (mutex) {
                        if (counter == idx) {
                            counter++;
                            System.out.println(idx);
                        }
                    }
                }
            }).start();
        }
    }
```

用 rentrant lock

```java
private static int counter = 0;
    private static int limit = 100;

    private static ReentrantLock lock = new ReentrantLock();


    public static void main(String[] args) {
        for (int i = 0; i < limit; i++) {
            final int idx = i;
            new Thread(() -> {
                while (counter <= idx) {
                    lock.lock();
                    if (counter == idx) {
                        counter++;
                        System.out.println(idx);
                    }
                    lock.unlock();

                }

            }).start();
        }
    }

```
