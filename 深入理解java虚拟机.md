# java 内存模型与线程

TPS transaction per second（每秒事务处理数）

## volatile

当一个变量是 volatile 时，将具备以下两条特性：

1. 保证该变量对所有线程的可见性，当一条线程修改了这个变量的值，新值对于其他线程是可以立即得知的。（普通变量做不到这一点，因为普通变量的值在线程之间传递需要通过主内存）。但对 volatile 变量的运算并不是并发安全，因为运算符不是原子的。
2. 禁止指令重排序

## java 线程的实现

在 hotspot 虚拟机中，每个 java 线程直接映射到一个操作系统原生线程来实现，没有中间额外操作，所以 hotspot 不干涉线程调度。

## java 线程状态

new：创建后尚未启动
running：有可能在执行，有可能在等待 os 为他分配时间
waiting：无限期等待，等待被其他线程唤醒。若执行 Object.wait() / Thread.join() LockSupport.park() 会进入此状态。
timed waiting：期限等待，一段时间后由系统自动唤醒。Thread.sleep(),设置了 timeout 时间的 Object.wait() / Thread.join()，LockSupport.parkNanos(),LockSupport.parkUntil()
blocked：被阻塞，等待获取一个排他锁
ternimated：已终止

# 线程安全与锁优化

## 阻塞同步 synchronized vs reentrantlock

synchronized 是一种块结构的同步语法。经过编译后会在同步块前后生成 monitorenter 和 monitorexit 两个字节码指令。两个指令都需要一个 reference 类型的参数来指明要锁定和解释的对象。如果指定了对象参数，那就以这个对象的引用作为 ref，若没有，则根据 synchronized 修饰的方法类型（实例方法或类方法），决定是取对象实例还是取 class。
进入 monitorenter 前会尝试获得对象的锁。如果没被锁定或被当前线程锁定，则计数器+1，执行 exit 时就-1。获取对象锁失败就线程阻塞。
sunchronized 可重入。
reentrantlock 锁与 synchronized 类似，但是 等待可中断， 公平， 可以锁定多个条件。

## 非阻塞同步 CAS

CAS 有三个操作数。V 表示内存地址，A 表示旧值，B 表示新值。如果 V 符合 A，就更新为 B。否则就不执行更新。

## 锁优化

### 自旋与自适应自旋

自旋：多核处理器中，如果一个线程没有拿到锁，并不挂起线程，而是让线程执行一个忙循环（自旋），看看锁是否会被很快释放。
自适应：如果一直获得了锁，那就延长自旋时间；如果很少获得过锁，就不自旋了。

### 锁消除

编译器发现不存在共享数据竞争，就把锁消除。

### 锁粗化

编译器检测到一串连续的操作对同一对象加锁，就把加锁同步的范围扩展到操作序列的外部。比如说扩展到第一个 append 之前和最后一个 append 之后

```java
public String concatString(String s1,String s2,String s3){
    StringBuffer sb = new StringBuffer();
    sb.append(s1);
    sb.append(s2);
    sb.append(s3);
    return sb.toString();
}
```

## 轻量级锁

利用 cas 避免互斥量。如果在轻量级锁状态时 cas 失败且锁被其他线程占用，轻量级锁会膨胀为重量级锁。在有竞争时，轻量级锁比重量级锁更慢，因为需要 cas+互斥量。

## 偏向锁

锁会偏向第一个获得它的线程。如果一直没有被其他线程获取，则第一个线程就不需要同步。
