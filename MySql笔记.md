# MySql

## 1.提升批处理操作性能

> rewriteBatchedStatements=true

MySQL的JDBC连接的url中要加rewriteBatchedStatements参数，并保证5.1.13以上版本的驱动，才能实现高性能的批量插入。
MySQL JDBC驱动在默认情况下会无视executeBatch()语句，把我们期望批量执行的一组sql语句拆散，一条一条地发给MySQL数据库，批量插入实际上是单条插入，直接造成较低的性能。
只有把rewriteBatchedStatements参数置为true, 驱动才会帮你批量执行SQL
另外这个选项对INSERT/UPDATE/DELETE都有效添加**rewriteBatchedStatements=true**这个参数后的执行速度比较：
同个表插入一万条数据时间近似值：
JDBC BATCH 1.1秒左右 > Mybatis BATCH 2.2秒左右 > 拼接SQL 4.5秒左右

```xml
master.jdbc.url=jdbc:mysql://112.126.84.3:3306/outreach_platforme&rewriteBatchedStatements=true
```

## 2.防止SQL注入

 \#{}是经过预编译的，是安全的；${}是未经过预编译的，仅仅是取变量的值，是非安全的，存在SQL注入 

- 防止like注入

  ```sql
  select * from t_user where name like concat('%', #{name}, '%')
  ```


## 3.解决MySql启动报错问题

![image-20210716090528861](G:\markdown\typora-user-images\image-20210716090528861.png)

## 4.Mysql常用语句

### 4.1 自动生成uuid

```sql
SELECT REPLACE(UUID(), '-', '');
```

### 4.2 批量替换指定字段的指定内容

```sql
update 表名 SET 字段名 = REPLACE( 字段名, '要修改的内容', '修改内容' ) where 字段名 like '%康浩OA组织架构图%'
```

## 5.索引基本知识

索引的类型有：

- 普通索引INDEX：加速查找
- 唯一索引：
      -主键索引PRIMARY KEY：加速查找+约束（不为空、不能重复）
      -唯一索引UNIQUE:加速查找+约束（不能重复）
- 联合索引：
      -PRIMARY KEY(id,name):联合主键索引
      -UNIQUE(id,name):联合唯一索引
      -INDEX(id,name):联合普通索引

### 5.1索引的增删查

```sql
-- 新增索引 create (类型，默认为normal) 索引名称 on 表名(字段名，可多个字段)
create index index_name on tableName(column)
alter table tableName add index index_name(column);
-- 删除索引 drop 索引名称 on 表名
drop index index_name on tableName;
alter table tableName drop primary key;
-- 查看索引
show index from tableName
```

### 5.2 常见索引失效的条件及原因

**1、 包含or查询**

除非or的所有字段建立一个索引，否则只要有一个没有索引，就不走索引

**2.like查询**

当查询的select字段是组合索引时，则忽略条件查询语句，直接索引查询

**3.where中存在函数，这种情况也不会使用索引查询**

**4.如何列类型是字符串，where时一定用引号括起来，否则索引失效**

### 5.3 索引优化及案例

type中从好到坏的类型：system > const> eq_ref>ref> range> index> all

1. 两表以上之间join的优化

   永远考虑小表驱动大表

   ```mysql
   -- 如果说左连接（left join）则给右边的表加索引，反之（right join）给左边的表加索引，inner则给右边的表加
   -- 原因：因为左连接的左表的字段右表一定有，因此右表会全表扫描，左表加了索引也不会生效
   select * from a left join b on a.no = b.no;
   ```

## 6.事务的操作

```mysql
-- 开启mysql自动提交事务 默认说开启的
SET AUTOCOMMIT=1
-- 关闭mysql自动提交事务 默认说开启的
SET AUTOCOMMIT=0
-- 手动开启事务
BEGIN 或 START TRANSACTION
-- 手动提交事务
COMMIT
-- 手动回滚事务
ROLLBACK
```

