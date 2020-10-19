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
线程池核心线程数
#### maximumPoolSize
线程池最大线程数
#### keepAliveTime unit
空闲时间、时间单位
#### workQueue
任务暂存队列
#### defaultHandler
拒绝策略

AbortPolicy:丢弃任务并抛出RejectedExecutionException异常 (默认)

DiscardPolicy：也是丢弃任务，但是不抛出异常

DiscardOldestPolicy：丢弃队列最前面的任务，执行后面的任务

CallerRunsPolicy：由调用线程处理该任务

#### 问题：什么时候核心线程数会增大到最大线程数？
在任务提交过程中，任务小于核心线程数时，新提交的任务直接新建work来执行任务，线程数已经达到核心线程数队列也满了的时候，新建work来接收任务。

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
`
name = "pool-" + poolNumber.getAndIncrement() + "-thread-" + threadNumber.getAndIncrement();
`

### BlockingQueue
阻塞队列用于存放待执行的Runnable任务

## 线程池如何实现线程复用的
1. 首先利用原子`AtomicInteger ctl`限制了最大线程数
2. Work 是任务类，启动后执行方法


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
            //线程死掉（过期之类的），此时新建一个线程，任务已
            else if (workerCountOf(recheck) == 0)
                addWorker(null, false);
        }
        else if (!addWorker(command, false))
            reject(command);
    }
```
