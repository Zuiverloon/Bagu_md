# MySQL
### 什么是主键？外键

Primary Key identifies the records in a table uniquely.

Foreign Key is used to link the tables.

### 索引
b+树，非叶子结点存放的是指向下一层的指针，叶子结点存放的是主键，通过主键去获得整条数据。

### 覆盖索引

查询的每一个属性都能在索引中获得，不需遍历数据表

### 聚集索引/非聚集索引

### 稀疏索引/稠密索引

### 复合索引
