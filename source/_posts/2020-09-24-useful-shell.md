---
title: 记录有用的shell
categories:
  - shell
tags:
  - thread
  - Linux
---

## java 进程查看

```
jps
jps -lvm  #查看进程启动参数等信息
```
## 进程所在端口
存在有时候进程起了，我们忘记了进程所在端口了，可以用一下命令查询。
```
netstat -nltp | grep  进程号
```
- n 显示ip地址，不展示进程名
- t 展示tcp链接
- p 展示进程id和进程名字
- l 默认项展示正在监听的（状态为 LISTEN)

效果如下：
```
[root@10-9-167-250 ~]# netstat -nltp | grep nginx
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      41220/nginx: master
tcp6       0      0 :::80                   :::*                    LISTEN      41220/nginx: master
```
备注 tcp6 表示 tcp 支持ip V6

## 进程中线程信息
todo
