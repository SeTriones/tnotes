# 协程是什么？

协程（coroutine）的 wikipeida 页面：https://en.wikipedia.org/wiki/Coroutine

协程可以被看做一个长期运行的计算任务，它在程序执行过程中随时可以被挂起和继续。

从这个介绍页面可以看出，协程的特点是：

* 非抢占式的，协程之间不存在优先级调度的问题，大家的优先级是均等的。协程之间的切换仅发生在当前运行的协程（定期地）主动让出 CPU 控制权或当前线程进入空闲或者阻塞状态（举例： NIO 或者磁盘 IO 阻塞）。
* 对比线程切换，协程的切换在用户态进行，避免了系统调用的高昂代价或者在切换过程中加锁对临界区进行保护。

# stackful & stackless coroutine

## stackful coroutine

## stackless coroutine

# longjmp 和协程

# Go 的协程实现

# Python 的协程实现

# 一个简单的协程实现
