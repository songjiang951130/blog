---
title: nginx 文件配置
date: '2018-12-19T21:52:40.000Z'
updated_at: '2000-12-19T21:52:40.000Z'
categories:
  - nginx
tags:
  - 文件配置
  - server
---

# config 写法

```text
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
    }
    include servers/*;
}
```

servers/myproject.conf

```text
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

在开始处理一个HTTP请求时，Nginx会取出header头中的Host，与每个server中的 server\_name进行匹配，以此决定到底由哪一个server块来处理这个请求。有可能一个Host与 多个server块中的server\_name都匹配，这时就会根据匹配优先级来选择实际处理的server块。 server\_name与Host的匹配优先级如下：

## 首先选择所有字符串完全匹配的server\_name，如www.testweb.com 。

## 其次选择通配符在前面的server\_name，如\*.testweb.com。

## 再次选择通配符在后面的server\_name，如www.testweb.\* 。

## 最后选择使用正则表达式才匹配的server\_name，如~^.testweb.com$。

