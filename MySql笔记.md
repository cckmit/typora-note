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