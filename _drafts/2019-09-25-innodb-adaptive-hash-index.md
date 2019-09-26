---
title: 自适应hash索引
#默认获取文件名上的日期。data可覆盖.
updated_at: '2019-09-25'
categories:
  - mysql
tags:
  - mysql
  - hash index
---

[官方英文链接](https://dev.mysql.com/doc/refman/5.7/en/innodb-adaptive-hash.html)
自适应hash索引使innodb更像一个内存数据库。```innodb_adaptive_hash_index``` 启用自适应hash索引，mysql 启动时添加参数```--skip-innodb-adaptive-hash-index```关闭自适应hash索引。

hash索引根据索引的前缀构建。

