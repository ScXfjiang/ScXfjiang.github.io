---
layout:     post
title:      muduo notes
toc:        true
categories: [C/C++, System Programming]
---
connection per thread
connected socket per thread

线程的创建和销毁会有比较大的资源开销，而且如果连接数太高系统会无法承受。线程池的设计可以解决上述问题，这也是一个通用的优化方案，不过我们还可以继续向下挖掘。

现有的设计中，每个线程都把处理socket的事情全部完成了，包括连接、read、write，能否将一部分工作剥离出来交给其他线程处理呢？



