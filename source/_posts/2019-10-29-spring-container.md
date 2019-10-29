---
title: spring 容器
categories:
  - spring
---

# 概述
ApplicationContext 代表spring容器，负责bean配置、实例化、组装。容器通过读取配置元数据对bean进行实例化、组装。元数据有xml、java注解、java代码。

ApplicationContext 有多种实现。通常在单节点系统中，容器会创建一个```ClassPathXmlApplicationContext```或```FileSystemXmlApplicationContext```对象。XML是旧版本的方式，现在推荐使用注解或者java代码的方式。

- 基于注解配置： 在Spring2.5中有过介绍支持基于注解的配置元数据
- 基于Java配置： 从Spring3.0开始，由Spring JavaConfig提供的许多功能已经成为Spring框架中的核心部分。这样你可以使用Java程序而不是XML文件定义外部应用程序中的bean类。使用这些新功能，可以查看@Configuration,@Bean,@Import和@DependsOn这些注解

[原文档链接](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#beans-basics)