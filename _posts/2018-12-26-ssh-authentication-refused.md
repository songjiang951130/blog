---
title: sshd Authentication refused
date: '2018-12-26T10:29:03.000Z'
categories:
  - ssh
tags:
  - ssh
  - 免密登录
---

# 2018-12-26-ssh-Authentication refused

配置相关 ssh 后还是无法免密登录，后来发现了一篇文章说是 ssh 安全问题 cat /var/log/secure

```bash
Authentication refused: bad ownership or modes for directory /home/songjiang
```

很明显这个提示说：这个文件的所有人和 modes（文件夹默认是755，文件是644） 有误，所以授权拒绝了。因为其他开发人员乱动了文件权限所以我这个文件夹变成了 777

相关命令

```bash
chmod 755 /home/songjiang
```

把用户目录 /home/songjiang 改为 755 后，却出现无法访问的问题。原来里面有不属于我的文件且无法读取，删除或者chmod一下就好

另外 .ssh 文件夹下modes 如下：

```bash
-rw-r--r-- 1 songjiang dev  397 Dec 24 20:20 authorized_keys
-rw------- 1 songjiang dev 1671 Dec 24 20:02 id_rsa
-rw-r--r-- 1 songjiang dev  413 Dec 24 20:02 id_rsa.pub
-rw-r--r-- 1 songjiang dev    0 Dec 24 20:20 known_hosts
```

ps:吐槽一下，乱改文件权限真的烦

