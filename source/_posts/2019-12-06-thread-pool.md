---
title: java 线程池介绍
categories:
  - 并发
tags:
  - 线程池
  - ThreadPoolExecutor
---

## ThreadPoolExecutor
java 1.5 提供的线程池，池化思想有很多实现线程池、数据库连接池，主要是对于有限资源的利用。直接进行大量 new Thread(new Runnable) 操作时，会占用内存区域的本地栈(native stack)，甚至抛出错误 OutOfMemoryError。
### 构造方法
```java
    public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue) {
        this(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue,
             Executors.defaultThreadFactory(), defaultHandler);
    }
```
### 参数说明
#### corePoolSize
线程池核心线程数，workQueue 未满时，work线程数量会是核心线程数
#### maximumPoolSize
线程池最大线程数，workQueue 未满时，会增加到最大线程数

[相关链接](https://stackoverflow.com/questions/17659510/core-pool-size-vs-maximum-pool-size-in-threadpoolexecutor)

corePoolSize == maximumPoolSize，即为固定的线程数
比如 Executors（线程池工具类） :
```java
    //单线程池，限制为1
    public static ExecutorService newSingleThreadExecutor(ThreadFactory threadFactory) {
        return new FinalizableDelegatedExecutorService
            (new ThreadPoolExecutor(1, 1,
                                    0L, TimeUnit.MILLISECONDS,
                                    new LinkedBlockingQueue<Runnable>(),
                                    threadFactory));
    }
```
```java
    //固定线程池，限制并发数为nThreads
    public static ExecutorService newFixedThreadPool(int nThreads) {
        return new ThreadPoolExecutor(nThreads, nThreads,
                                      0L, TimeUnit.MILLISECONDS,
                                      new LinkedBlockingQueue<Runnable>());
    }
```
### defaultThreadFactory
设置了每个线程的名字，优先级，非后台线程
```
name = "pool-" + poolNumber.getAndIncrement() + "-thread-" + threadNumber.getAndIncrement();
```

### BlockingQueue
阻塞队列用于存放待执行的Runnable任务

## 线程池如何实现线程复用的
1. 首先利用原子```AtomicInteger ctl```限制了最大线程数
2. Work 是任务类，启动后执行方法

基本用法：
```
```


execute部分源码
```
 public void execute(Runnable command) {
        //获取现在核心的并发数
        int c = ctl.get();
        if (workerCountOf(c) < corePoolSize) {
            //小于核心数直接添加为核心任务
            if (addWorker(command, true))
                return;
            c = ctl.get();
        }
        //任务成功入队之后，还需要double-check(因为期间可能线程已死或者进入方法后线程池shutdown)
        if (isRunning(c) && workQueue.offer(command)) {
            int recheck = ctl.get();
            if (! isRunning(recheck) && remove(command))
                reject(command);
            //线程死掉（过期之类的），此时新建一个线程
            else if (workerCountOf(recheck) == 0)
                addWorker(null, false);
        }
        else if (!addWorker(command, false))
            reject(command);
    }
```
