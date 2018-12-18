---
title: nginx 基本知识
date: 2018-12-18 21:55:33
categories:
- nginx
tags:
- 反向代理
- 负载均衡
---

# nginx
## 什么是nginx
nginx是高性能的http服务器，反向代理服务器

正向代理：为客户端做代理，常见翻墙

反向代理：为服务器端做代理，常见 负载均衡

### 反向代理
推荐官方链接：https://nginx.org/en/docs/http/ngx_http_upstream_module.html

nginx配置
```nginx
upstream backend {
    server backend1.example.com       weight=5;
    server backend2.example.com:8080;
    server unix:/tmp/backend3;

    server backup1.example.com:8080   backup;
    server backup2.example.com:8080   backup;
}

server {
    location / {
        proxy_pass http://backend;
    }
}
```
指令详解：
##### upstream
声明一组服务。每个服务可以监听不同的端口。另外，服务可以监听 ```tcp``` 和 ```unix-domain``` socket

常见配置：

轮询(默认配置)
可以配置权重，当有7个请求打来时，会有5个请求打在backend1上，其余两个请求分别打到剩余的服务上。

好处：支持根据服务器性能分配权重

ip_hash:以客户端的ip地址做hash运算
参考博客： 
[博客地址](https://www.cnblogs.com/xiaohuo/archive/2012/08/13/2636468.html)

```c++
for (i = 0; i < 3; i++) {  
    hash = (hash * 113 + iphp->addr[i]) % 6271;  //iphp->addr[i]为ip的点分十进制法的第i段
}
```

ip以点分十进制的方式，取的ip地址前三位做hash运算，这样说明用户在同一个局域网里（可以回顾一下计算机网络）

好处：可以维持 ```session``` 
