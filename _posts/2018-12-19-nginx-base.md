---
title: nginx 文件配置
date: 2018-12-19 21:52:40
categories:
- nginx
tags:
- 文件配置
- server
---
## config 写法

```nginx
#user  root;
worker_processes  1;
#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;
    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       8080;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        }
    include servers/*;
}
```

servers/myproject.conf
```nginx
server{
    listen 80;
    server_name songjiang.damo.haibian-d.com;
    root /home/songjiang/myproject/damo/haibian_damo/public;
    charset utf-8;
    index index.php;
    access_log /tmp/access.log;
    error_log /tmp/error.log;
    location / {
        try_files $uri $uri/ /index.php?$uri&$args;
    }
    location ~* ^.+\.(js|ico|gif|jpg|jpeg|pdf|png|css)$ {
        expires 0;
    }
    location ~* .*\.php$ {
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```
同一个端口（80），不同的域名 可以匹配多个项目。



