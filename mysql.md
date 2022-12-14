# MySQL
## 什么是主键？外键

Primary Key identifies the records in a table uniquely.  
Foreign Key is used to link the tables.

## 索引

b+树，非叶子结点存放的是指向下一层的指针，叶子结点存放的是主键，通过主键去获得整条数据。

## 覆盖索引

查询的每一个属性都能在索引中获得，不需遍历数据表  

## 聚集索引/非聚集索引

如果记录按照索引码的顺序排列，则该索引是聚集索引  
每张表仅一个聚集索引  
只有聚集索引能是稀疏索引   


## 稀疏索引/稠密索引

稠密：为索引码的每个值都创建索引

## 复合索引
索引包含多个属性

## 最左匹配原则
检索数据时从联合索引的最左边开始匹配  
eg: 索引(a,b,c)   
where a = 1 and b = 1(yes)  
where a = 1 and c = 1(yes 只用到了索引a)  
where b = 1 and c = 1(未命中)
