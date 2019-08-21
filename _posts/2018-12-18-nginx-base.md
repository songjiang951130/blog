---
title: nginx 负载均衡
date: 2018-12-18T21:55:33.000Z
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

推荐官方链接：[https://nginx.org/en/docs/http/ngx\_http\_upstream\_module.html](https://nginx.org/en/docs/http/ngx_http_upstream_module.html)

nginx配置

```text
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

**upstream**

声明一组服务。每个服务可以监听不同的端口。另外，服务可以监听 `tcp` 和 `unix-domain` socket

常见配置：

#### 轮询+权重\(默认配置\)

可以配置权重，当有7个请求打来时，会有5个请求打在backend1上，其余两个请求分别打到剩余的服务上。

> 好处：支持根据服务器性能分配权重

#### ip\_hash

以客户端的ip地址前三位做hash运算 参考博客： [博客地址](https://www.cnblogs.com/xiaohuo/archive/2012/08/13/2636468.html)

```cpp
for (i = 0; i < 3; i++) {  
    hash = (hash * 113 + iphp->addr[i]) % 6271;  //iphp->addr[i]为ip的点分十进制法的第i段
}
```

ip以点分十进制的方式，取的ip地址前三位做hash运算，这样说明用户在同一个局域网里（可以回顾一下计算机网络）

> 好处：同一个用户（ip）可以访问同一台机器，那么可以维持 `session`

#### url\_hash \(第三方\)

> 好处：同一URL请求会打在同一台服务器上，增加缓存命中率，特别适合配合 `squid` 使用

#### fair \(第三方\)

按后端服务器的响应时间分配,响应时间短的优先分配

> 好处：服务器负载更加均匀

