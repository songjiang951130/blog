---
title: innodb 最佳实践
date: '2019-09-24'
categories:
  - mysql
tags:
  - mysql innodb
---

[官方英文链接](https://dev.mysql.com/doc/refman/5.7/en/innodb-best-practices.html)
- 设置表中常用的一个列或者多个列为主键。没有用自增id做为主键。
- 有相同id的表，联表查询使用 ```join``` 。使用数据结构相同的外键提高性能，保持数据完整性（外键能起到关联更新和删除的作用，防止在子表中插入父表中没有的数据）。
- 关闭自动提交。每秒上百次提交影响性能（这取决于存储设备的性能）。

> ps: 这不就是让你上ssd嘛  

- 批量数据库DML（Data manipulation language，增删改）操作使用事务。
- 不使用表锁 ```LOCK TABLES``` 。innodb 有行级锁，能够提高性能、保证数据完整。```SELECT ... FOR UPDATE```
- 启用```innodb_file_per_table```（默认已启用） 或者通用的表空间存放数据和索引到单独的文件中，替换系统的表空间
- 自行判断使用 innodb 表压缩功能
- 启动服务时配置  ```--sql_mode=NO_ENGINE_SUBSTITUTION``` ，防止创建其他储存引擎的表


注：
**InnoDB行锁是通过给索引上的索引项加锁来实现的，这一点MySQL与Oracle不同，后者是通过再数据块中，对相应数据行加锁来实现的。InnoDB这种行锁实现特点意味着：只有通过索引条件检索数据，innoDB才使用行级锁，否则InnoDB将使用表锁，在实际开发中应当注意**  
来源：https://www.cnblogs.com/fuwentao/p/7354145.html