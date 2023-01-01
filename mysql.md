# MySQL

## 什么是主键？外键

Primary Key identifies the records in a table uniquely.  
Foreign Key is used to link the tables.

# 什么是 join？

join:从两张或多张表中读数据  
inner join:selects records that have matching values in both tables.(需指明那一列)  
left outer join:保留左表的所有数据，匹配右表的数据，如果没有就 null  
right outer join:同上  
natural join:自动匹配名字相同的列，会导致意外情况  
join 时用 using 和 on 的区别:using 会去掉重复的一列  
cross join: give the cartisian product result 笛卡尔积(对每一列，匹配所有的列)

## 存储过程&函数

存储、组织、优化 sql，加强安全性

## 事务 ACID 特性

Atomic 原子性：事务不可分割，要么全执行，要么全不执行  
Consistency 一致性：事务开始前、结束后数据库状态一致（银行转帐，有进有出）  
Isolation 隔离性：事务可并发执行，但对于一个事务感知不到另外的事务  
Durability 持久性：对数据库的操作是永久的

## 索引

用来快速查找符合某些条件的数据，提高查询性能  
b+树，非叶子结点存放的是指向下一层的指针，叶子结点存放的是主键，通过主键去获得整条数据。
只有主键是一级索引(primary 且 cluster)，我们自己创建的都是二级索引

## 覆盖索引

查询的每一个属性都能在索引中获得，不需遍历数据表

## 聚集(cluster)索引/非聚集索引

如果记录按照索引码的顺序排列，则该索引是聚集索引  
每张表仅一个聚集索引  
只有聚集索引能是稀疏索引

## 稀疏(sparse)索引/稠密(dense)索引

稠密：为索引码的每个值都创建索引

## 复合(union)索引

索引包含多个属性

## 最左匹配原则

检索数据时从联合索引的最左边开始匹配  
eg: 索引(a,b,c)  
where a = 1 and b = 1(yes)  
where a = 1 and c = 1(yes 只用到了索引 a)  
where b = 1 and c = 1(未命中)

## 遇到慢查询怎么办

```sql
    explain select ...
```

用 explain 看下 sql 是怎么执行的，是否扫全表，是否命中了索引

## 有哪些存储引擎

INNODB(用于安全性要求高，频繁 update 的场景)：

1. 支持事务
2. 唯一支持外键约束
3. 自动灾难恢复
4. 支持 auto_increment

MYISAM(用于较少的 update，较多的 select 的场景):

1. 每次查询有原子性，速度快，无事务
2. 允许没有索引和主键，索引保存行地址

## mysql 执行过程

查询语句：

1. 客户端将 SQL 语句通过驱动发送到连接池
1. (8.0 开始没有这一步)进入查询缓存查询，有的话直接通过连接池返回(为什么没有了？查缓存命中率不高，还需要新建缓存，修改后批量失效，开销较大)
1. 通过 SQL 解析器解析 SQL 语句(词法分析，语法分析)
1. 解析完后通过预处理器，提交语句模版(可复用，如一个模版不同参数)和参数(会验证参数，如表、字段是否存在)
1. SQL 优化(基于成本，选择成本最小的路径，通过索引，表的大小等计算出 IO 和 CPU 消耗)
1. 经过上述步骤，sql 语句变成了执行计划，由执行器来执行(调用存储引擎的 handler API)
1. 获取结果后返回

**磁盘与内存交互的最小单位**：页(16KB)，页中地址是连续的。一个区(1MB)存 64 个连续的页。一个组(256MB)存 256 个区

**每行数据长度限制**：64KB，页中一行的大小为 8KB，超过 8KB 的数据存在溢出页(只针对 varchar，varbinary)

**每个表最多 4096 个字段**(受到每行长度 8KB 的限制，如果全是 8B 的字段，那只能有 1000 个字段)
