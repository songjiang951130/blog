---
title: hexo-leancloud-counter-security 配置
categories:
  - hexo
tags:
  - hexo-leancloud-counter-security
---

# 安装

```
npm install hexo-leancloud-counter-security --save
npm install babel-runtime --save
```
hexo-leancloud-counter-security 依赖 babel-runtime 模块

还需要在项目的配置文件 _config.yml 中设置 (而不是主题 next 中配置文件中)

```
leancloud_counter_security:
  enable_sync: true
```

# 错误
## Error: Cannot find module 'babel-runtime/regenerator'
少安装了依赖 babel-runtime

## TypeError: Cannot read property 'enable_sync' of undefined
未配置 enable_sync







