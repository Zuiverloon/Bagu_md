# spring

spring 框架帮助管理类的实例

## IOC / DI

一个对象只需要定义它的依赖，当 IOC 容器初始化 bean 时会自动注入装配依赖

### application context

spring 的 ioc 容器对象就是一个 applicationcontext 对象，通过读取 xml 或注解可知道如何实例化对象，装配依赖

```java
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml"); //使用classpath中的xml来初始化applicaitoncontext
```

### bean

ioc 容器创建的实例，属性包括(name,class,scope,constructor args,properties...)

### 获取 bean 的方法

要获取 XML 中配置的 Bean，最关键的是获取.springframework.context.ApplicationContext，获取后调用 getbean 即可

1. 在初始化时保存 ApplicationContext 对象
1. 通过 Spring 提供的工具类获取 ApplicationContext 对象
1. 继承自抽象类 ApplicationObjectSupport
1. 继承自抽象类 WebApplicationObjectSupport
1. 实现接口 ApplicationContextAware
1. 通过 Spring 提供的 ContextLoader

## bean 生命周期(5.x)

生产:

1. 加载 bean 定义，放在 beanDefinitionMap 中
2. 根据 map 调用 createBean

   1. 构造对象(createBeanInstance)，通过 bean class 属性找构造函数(如果有多个，优先拿 autowired 的，无法判断就报错) / 到单例池中找 bean 作为构造参数
   2. 填充属性(populateBean)，三级缓存
   3. 初始化实例
   4. 注册销毁，注册上述的 bean 就可以在销毁时执行 destroy

3. 放入单例池

使用  
销毁

1. 先执行前处理器执行所有@predestroy 方法
2. 调用 destroy 销毁 bean

### 依赖注入

实现方法分为构造器注入和 setter 注入

### 循环依赖

如果相互依赖的两个类都是构造器注入，则无法解决抛出异常，要改为其中一个 setter 注入，其中一个先初始化，再实例化

### lazy initialize bean

可以在 xml 中申明一个 bean 是 lazy initialize 的，这样就不会在一开始就实例化他，而是什么时候请求什么时候实例（如果是 singleton 的依赖，则还是一开始就实例化）

### bean scope

1. singleton：每个 ioc 容器仅一个
2. prototype：每次调用这个 bean 都会创建
3. request 一个 http 请求一个
4. session 一个 http session 请求一个  
   如何指定？xml 或注解@Scope 指定

## AOP

业务无关的代码与业务接耦，如打日志操作，校验参数(Before+传参)可以不写在方法中而是通过 AOP 实现

@Before @After 注解，统称为 advice

## 事务

Spring 不直接管理事务，通过事务管理器来管理。最底层接口为 transactionManager，包括 commit 和 rollback 方法。 可以通过注释@Transactional 或通过 xml 实现声明式的事物。(不要忘了配置 xml 里面的 datasource 如 jdbc 和事务管理器)

```java
// the service class that we want to make transactional
@Transactional
public class DefaultFooService implements FooService {

    @Override
    public Foo getFoo(String fooName) {
        // ...
    }

    @Override
    public Foo getFoo(String fooName, String barName) {
        // ...
    }

    @Override
    public void insertFoo(Foo foo) {
        // ...
    }

    @Override
    public void updateFoo(Foo foo) {
        // ...
    }
}


```

也可以是通过代码写事务

```java
DefaultTransactionDefinition def = new DefaultTransactionDefinition();
// explicitly setting the transaction name is something that can be done only programmatically
def.setName("SomeTxName");
def.setPropagationBehavior(TransactionDefinition.PROPAGATION_REQUIRED);

TransactionStatus status = txManager.getTransaction(def);
try {
    // put your business logic here
} catch (MyException ex) {
    txManager.rollback(status);
    throw ex;
}
txManager.commit(status);


```

**事务传播行为**有 7 种，默认是 REQUIRED 传播机制。

1. REQUIRED：如果当前存在事务，则加入该事务；如果当前不存在事务，则创建一个新的事务；
1. SUPPORTS：如果当前存在事务，则加入该事务；如果当前不存在事务，则以非事务的方式继续运行；
1. MANDATORY：如果当前存在事务，则加入该事务；如果当前不存在事务，则抛出异常；
1. REQUIRES_NEW：总是使用一个新的事物，而不加入现有的。如果当前不存在事务，重新创建一个新的事务；如果当前存在事务，则暂停当前的事务；
1. NOT_SUPPORTED：以非事务的方式运行 如果当前存在事务，则暂停当前的事务
1. NEVER：以非事务的方式运行，如果当前存在事务，则抛出异常
1. NESTED：如果当前存在事务，则在该事务内嵌套事务运行；如果当前不存在事务，则创建一个新的事务；

**REQUIRED 和 NESTED 区别**  
方法 A 中调用方法 B，如果是 required，A 故障，AB 全退，B 故障，AB 全退
如果是 nested，A 故障，AB 全退，B 故障，退 B

### 事务不生效的场景

1. 方法必须是 public 不然事务就失效
1. 方法不能是 final(aop 本质是生成代理类，如果方法是 final 或不是 public 就无法生成)
1. 调用本类的方法(调用了 this 对象的方法，也不会生成代理)
1. 忘了加 service 注释，没有被 spring 管理
1. 忘了开事务，去 applicationcontext.xml 配置一下

### 事务不回滚的场景

1. 在代码中把异常捕获了，导致事务管理器没有捕获到异常
2. 抛错了异常