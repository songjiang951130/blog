---
title: rocketmq 资料整理
date: '2019-09-02'
categories:
  - 消息队列
tags:
  - rocketmq
  - spring-boot
  - doc
---

## 中文官网：
[http://rocketmq.cloud/zh-cn](http://rocketmq.cloud/zh-cn)
## 源码地址
[https://github.com/apache/rocketmq](https://github.com/apache/rocketmq)
## web console项目(console-ng)
[https://github.com/apache/rocketmq-externals/tree/master/rocketmq-console](https://github.com/apache/rocketmq-externals/tree/master/rocketmq-console)

![image.png](https://upload-images.jianshu.io/upload_images/6318820-3393cdbb1000aae4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



## spring boot 整合
### github
#### demo
[https://github.com/apache/rocketmq-spring](https://github.com/apache/rocketmq-spring)
#### 文档
[https://github.com/apache/rocketmq-spring/blob/master/README_zh_CN.md](https://github.com/apache/rocketmq-spring/blob/master/README_zh_CN.md)

maven 配置：

```
<dependency>
    <groupId>org.apache.rocketmq</groupId>
    <artifactId>rocketmq-spring-boot-starter</artifactId>
    <version>2.0.3</version>
</dependency>
```