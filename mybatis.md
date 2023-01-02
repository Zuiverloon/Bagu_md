# mybatis

database access framework in java

## SqlSessionFactory

以 sqlsessionfactory 为核心，通过读取 xml 或代码来建立

```java
String resource = "org/mybatis/example/mybatis-config.xml";
InputStream inputStream = Resources.getResourceAsStream(resource);
SqlSessionFactory sqlSessionFactory =
  new SqlSessionFactoryBuilder().build(inputStream);
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "https://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <property name="driver" value="${driver}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${username}"/>
        <property name="password" value="${password}"/>
      </dataSource>
    </environment>
  </environments>
  <mappers>
    <mapper resource="org/mybatis/example/BlogMapper.xml"/>
  </mappers>
</configuration>
```

**sqlsessionfactory 的作用域**  
最好是应用运行时一直存在（可使用单例模式）

## sqlsession

通过 sqlsessionfactory 获得 sqlsession
可以直接通过 sqlsession 执行 sql（但更好的是用 mapper）

```java
try (SqlSession session = sqlSessionFactory.openSession()) {
  Blog blog = session.selectOne(
    "org.mybatis.example.BlogMapper.selectBlog", 101);
}
```

**sqlsession 作用域**  
最好是一个线程或一个请求维护一个 session

## mapper

通过 sqlsession 获得 mapper，mapper 就是一个接口，通过 mapper 执行 sql

```java
try (SqlSession session = sqlSessionFactory.openSession()) {
  BlogMapper mapper = session.getMapper(BlogMapper.class);
  Blog blog = mapper.selectBlog(101);
}
```

mapper 可以写在 xml 中，也可以写在 java 接口中

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.mybatis.example.BlogMapper">
  <select id="selectBlog" resultType="Blog">
    select * from Blog where id = #{id}
  </select>
</mapper>
```

```java
package org.mybatis.example;
public interface BlogMapper {
  @Select("SELECT * FROM blog WHERE id = #{id}")
  Blog selectBlog(int id);
}
```

**mapper 作用域**
最好和 sqlsession 一致

## 读取配置优先级

如果某些属性在不止一个地方被配置，顺序是：首先读 mybatis-config.xml 中 properties 标签体内的属性，在读取 properties 元素的 resource 属性路径下的属性文件中的属性(config.properties 文件)，最后读取方法参数传递的属性（通过调用 SqlSessionFactoryBuilder.build()）。优先级是最后读的最高  
事务管理器 transactionManager 标签 如果选择 jdbc 就是使用 jdbc 的提交与回滚，如果使用 managed，由容器来管理事务，spring 用自带的管理器来管理  
数据源 data-source 标签 选择 pooled 使用连接池，避免创建新实例所花的初始化与认证时间。

## \#{}和${}的区别

\#{}会预编译 sql 语句，就像 jdbc 中的 preparedStatement
而\${}是字符串拼接，会出现 sql 注入的问题

## resultMap 自动映射

sql 查询完的结果集中的列名和 java 类中的属性名自动映射

## resultMap 手动映射

在 resultMap 标签中，通过 column 和 property 标签进行配对

## 多表查询（一对一）

可以写多表关联的 sql，也可以分步查询
多表关联：直接写 join 的 sql 语句，resultMap 手动映射，也可以在 resultMap 下用 association 标签(也是指定某个类的字段和查询出来的字段怎么映射，前者写法是 user.id,后者是 id，因为已经知道类名了)
分步：
