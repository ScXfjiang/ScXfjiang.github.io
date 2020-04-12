---
layout:     post
title:      C++ STL Multithread Programming
toc:        true
categories: [Multithread Programming, C/C++]
---
## 综述
C++11标准在STL层次引入了对多线程的支持，最大的好处是使得跨平台开发更加容易。本文主要对STL多线程解决方案进行总结，内容涵盖另一篇博客**[Pthreads Basics](http://127.0.0.1:4000/multithread%20programming/c/c++/2020/04/06/Pthreads_Basics.html)**中的全部主题并会对两种多线程解决方案进行对比，建议读者对比阅读。

## 线程的创建、终止和错误处理
STL对线程做了一层封装，使用std::thread类表示。[cppreference: std::thread](https://en.cppreference.com/w/cpp/thread/thread)

![std::thread](/assets/images/posts/post_2020-04-12-STL_Multithread_Programming_Basics_std_thread.png)

### 线程的创建
创建一个std::thread对象就代表创建一个线程。 类似于Pthreads，std::thread对象在被创建后线程函数立即执行。

#### Thread Id
STL中使用std::thread::id类表示Thread Id，并在std::thread中提供了get_id()方法。

### 线程的终止
线程在以下情况下会终止：
* 线程函数正常return返回
* 被其他线程取消

#### Joinable vs. Detached (资源回收)
std::thread提供join()表示当前线程等待该线程执行完毕并回收资源，也提供detach()方法设置线程为Detached状态。

### 错误处理
STL多线程解决方案使用异常处理多线程中出现的错误。