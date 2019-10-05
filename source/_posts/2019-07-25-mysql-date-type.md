---
title: mysql 时间类型文档翻译
date: '2019-07-25'
categories:
  - mysql
tags:
  - timestamp
  - datetime
  - date
---

date、datatime、timestamp 都是时间类型，这部分内容将介绍它们的特点，相似和不同。Mysql 识别date、datetime、timestamp的值按以下[几种方式格式化](https://dev.mysql.com/doc/refman/8.0/en/date-and-time-literals.html)。对于 date 和 datetime类型，"supported" 意味着可能支持早期的值，但是不保证。

date 类型只有date（年月日）部分，没有time（时分秒）部分。Mysql以 "YYYY-MM-DD" 格式取出展示date类型数据。数据范围 从'1000-01-01' 到 '9999-12-31'。

datetime 类型同时包含了date（年月日）部分和time（时分秒）部分。Mysql以 "YYYY-MM-DD hh:mm:ss" 格式取出展示datetime类型数据。数据范围从 '1000-01-01 00:00:00' 到 '9999-12-31 23:59:59'。

TIMESTAMP 数据类型同时包含了date和time部分。 数据范围从 '1970-01-01 00:00:01' UTC 到 '2038-01-19 03:14:07' UTC.

datetime 和 timestamp 后有六位数的精度（微秒），例如datetime 的精度范围是 '1000-01-01 00:00:00.000000' 到 '9999-12-31 23:59:59.999999'。

datetime 和 timestamp 可以根据当前时间进行初始化和更新。[详情点击](https://dev.mysql.com/doc/refman/8.0/en/timestamp-initialization.html)

个人补充例如：
```
`created_time` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '创建时间戳'
```

Mysql 将 timestamp 从当前时区转到UTC 进行存储，然后从 UTC 取出转为当前时区（这种情况不会出现在其他时间类型中，比如 datatime）。默认，每个mysql连接的当前时区是服务器的时区，也可以自定义时区。只要设置的时区不变，设置和取出的timestamp就是一致的。如果你存储timetamp值，再修改时区，那设置值和取出值就不一致了。

非法的 date、datetime、timestamp会转化为 0 比如'0000-00-00' 或  '0000-00-00 00:00:00'。

时间类型的注意事项
- Mysql 为字符串转为时间类型提供一个宽松的格式转化。在 data 或者 time部分，字符串的任意标点都可能作为时间的分割。某些情况下，这样的语法存在问题。比如，因为 ':'，'10:11:12'看起来是一个time类型。
- 限制月和日必须是合法的，而不仅仅是 1-12和1-31。特别是，取消了strict 模式，非法的值，比如'2004-04-31' 将被转为'0000-00-00'同时抛出警告；strict 模式时，非法的值将会报错。允许这种值，需要启用 ALLOW_INVALID_DATES，详情见 [5.1.11](https://dev.mysql.com/doc/refman/8.0/en/sql-mode.html) 部分
- timestamp 不允许 日和月为0，或者其他非法的日期。这种特殊情况将处理零值 '0000-00-00 00:00:00'
- 日期中用两位数字代表的年份是不确定的，Mysql使用以下规则来处理这些情况：
 1. 00-69范围转化为 2000-2069
 2. 70-99 转化为 1970-1999


详情请见 [11.3.7](https://dev.mysql.com/doc/refman/8.0/en/two-digit-years.html) 部分

个人补充说明：
UTC 比格林尼治天文台的时间更精准的，零时区的时间。