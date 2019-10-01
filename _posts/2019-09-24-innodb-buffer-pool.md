---
title: innodb 缓冲池
date: '2019-09-24 15:18'
categories:
  - mysql
tags:
  - mysql
  - innodb
---

缓冲池是在内存中用于存放表和索引的一块区域。直接在内存中获取常用数据。在专有数据库中通常分配 ```80%``` 的物理内存给缓冲池。

为了提高大量的读操作，缓冲池分为多个页，每页可能包含多行数据。缓冲池通过实现一种 ```linked list``` 来管理缓存中的页。使用一种变体的LRU算法的移出低效数据。

了解如何利用缓冲池将经常访问的数据保留在内存中是MySQL优化的重要手段

# 缓冲池的LRU算法

缓冲池由变体的LRU算法管理。当有新的页面需要加入到缓冲池中时，移除最近最少使用的页面，将新页面加入到```linked list```的中点(从后面来看，并不是完全的中点)。中点将list分为两部分：
- 头部分到中点的子列表是表示最近常用的 yong 页面
- 尾部分到中点的子列表是表示最近不常用的 old 页面

## 算法示意图

![list 示意图](https://dev.mysql.com/doc/refman/5.7/en/images/innodb-buffer-pool-list.png)

算法将大量页面保留到新子列表中，旧子列表包含的是较少使用的待移除的页。  
默认算法操作如下：
- 3/8的缓冲池是待删除的旧子列表
- 列表的中点是新子列表的尾部与旧子列表的头相交的边界
- innodb 读取新的页面到缓冲池列表中，将旧的中点作为旧子列表的头部。页面可以由用户的sql或者innodb的预读<sup>[1]</sup>操作查到。
- 获取旧列表中的页面可以将该页由旧转新，该页面会被移动到新子列表的头部。用户操作的情况下，首次访问页面会设置为young。mysql自己的预读操作，首次访问可能直到页面移出缓存都不会发生。
- 对于数据库的操作，页面未访问将会移到列表尾部。列表中的页面都能变 young。旧列表的页面会插入到中点，最后，如果页面没有使用的，会移动到就列表的尾部，然后移除。

默认，查询出的页面会立刻移动到新子列表，意味着待在缓冲池的时间更长。整个表扫描（mysqldump或者select不加where的查询）造成大量数据页加入到缓冲池中，旧列表页也会有等量的页面移出，即使新数据再也没有使用。同样，由预读后台线程加载且仅访问一次的页面将移到新列表的开头。这些情况可能会将常用页面推到旧列表。

innodb 标准监听的输出包含这结果部分

# 缓冲池配置
可以通过一下几点配置缓冲池来提高性能

- 理想情况下，为其他进程分配足够的内存后，为缓冲池分配剩下的内存，这样可以避免频繁分页操作。缓冲池足够大，innodb 更像一个基于内存的数据库。
- 在具有足够内存的64位系统上，可以将缓冲池分成多个部分，以最大程度地减少并发操作之间争用。
- 将频繁访问的数据保留在内存中，拒绝全表扫描<sup>[2]</sup>。
- 自行配置预读操作，判断某些页面将要使用
- 配置后台刷新。控制后台刷新的时间，根据负载情况调整刷新频率
- 配置保存缓冲池，重启时保留之前的缓冲池

# 查看innodb标准监听器

```sql
SHOW ENGINE INNODB STATUS
```
缓冲池指标位于InnoDB Standard Monitor输出的BUFFER POOL AND MEMORY部分中，并且看起来类似于以下内容

```sql
----------------------
BUFFER POOL AND MEMORY
----------------------
Total large memory allocated 2198863872
Dictionary memory allocated 776332
Buffer pool size   131072
Free buffers       124908
Database pages     5720
Old database pages 2071
Modified db pages  910
Pending reads 0
Pending writes: LRU 0, flush list 0, single page 0
Pages made young 4, not young 0
0.10 youngs/s, 0.00 non-youngs/s
Pages read 197, created 5523, written 5060
0.00 reads/s, 190.89 creates/s, 244.94 writes/s
Buffer pool hit rate 1000 / 1000, young-making rate 0 / 1000 not
0 / 1000
Pages read ahead 0.00/s, evicted without access 0.00/s, Random read
ahead 0.00/s
LRU len: 5720, unzip_LRU len: 0
I/O sum[0]:cur[0], unzip sum[0]:cur[0]
```

>注意⚠️：Per second averages 基于上次输出的时间

## 表14.2 InnoDB缓冲池指标

| 指标 |解释|
|----|---|
|Total memory allocated|缓冲池分配的内存，单位 byte|
|Dictionary memory allocated|innodb 字段内存，单位byte|
|Buffer pool size|页面大小，byte|
|Free buffers|缓冲池空闲列表的页面总大小|
|Database pages|缓冲池LRU列表的页面总大小|
|Old database pages|缓冲池LRU旧子列表的页面总大小|
|Modified db pages|缓冲池中当前修改的页面数|
|Pending reads|等待读入缓冲池的缓冲池页面数|
|Pending writes LRU|从LRU列表的底部开始写入的缓冲池中的旧脏页数|
|Pending writes flush list|检查点期间要刷新的缓冲池页面数。|
|Pending writes single page|缓冲池中暂挂的独立页面写入数|
|Pages made young|缓冲池LRU列表中变年轻的页面总数（移至“新”页面的子列表的开头）|
|Pages made not young|缓冲池LRU列表中没有年轻的页面总数（保留在“旧”子列表中但没有年轻的页面）|
|youngs/s|每秒平均访问缓冲池LRU列表中的旧页面所导致的页面年轻|
|non-youngs/s|每秒平均访问缓冲池LRU列表中的旧页面导致的页面不年轻。|
|Pages read|从缓冲池读取的页面总数|
|Pages created|在缓冲池中创建的页面总数|
|Pages written|从缓冲池写入的页面总数|
|···|···省略|

<hr />
参考链接

1. [预读](https://dev.mysql.com/doc/refman/5.7/en/glossary.html#read-ahead)
2. [扫表优化](https://dev.mysql.com/doc/refman/5.7/en/innodb-performance-midpoint_insertion.html)
