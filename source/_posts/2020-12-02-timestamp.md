---
title: timestamp 问题
categories:
  - mysql
tags:
  - 数据类型
---

mybatis 接收 timestamp 对象时报类型转化错误，

```
Caused by: java.sql.SQLException: Value '0000000-00-00 00:00:00' can not be represented as java.sql.Timestamp
```
**需要MySQL中字段设置默认值(current_timestamp)，而不是 0000-00-00 00:00:00**

例如：
```
  `updated_at` datetime NOT NULL DEFAULT current_timestamp() ON UPDATE current_timestamp() COMMENT '更新时间戳'
```
