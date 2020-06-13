---
layout:     post
title:      Linux Pipe and FIFO
toc:        true
categories: [System Programming, C/C++]
---
## 一、(Unnamed) Pipe
**(Unnamed) Pipe**是比较早期进程间通信方法，其局限在于没有名字，所以某个进程无法根据名字找到指定的Pipe，只能在有亲属关系的进程间使用（不考虑文件描述符传递）。

Pipe是**半双工**的，但一般当做单工使用，若要实现全双工，需要创建两个Pipe。
### 1. Pipe的创建与使用
```c
#include <unistd.h>

int pipe(int fd[2]);
```
该接口返回两个文件描述符，$$fd[0]$$用来读，$$fd[1]$$用来写。

<br/>

在单个进程内创建完Pipe对象后进程和内核的模型：
![pipe in single process](/assets/posts/Pipe_and_FIFO/pipe_in_single_process.png)

fork子进程后进程和内核的模型：
![pipe between two processes 1](/assets/posts/Pipe_and_FIFO/pipe_between_two_processes_1.png)

关闭不必要的文件描述符后形成单工Pipe：
![pipe between two processes 2](/assets/posts/Pipe_and_FIFO/pipe_between_two_processes_2.png)

### 2. Pipe的销毁
Pipe是一个多个进程间共享的内核对象，可以想象它应该是使用一个多个进程间共享的临时文件来实现的，用户只需要在使用完Pipe后关闭它，而不用去管Pipe的生命周期，其销毁由内核负责。


## 二、FIFO (First In, First Out, Named Pipe)
**FIFO**解决了Pipe无法通过名字访问的缺陷，它又被称为**Named Pipe**。

与Pipe相同，FIFO也是**半双工**的。

### FIFO的创建与使用
```c
#include <sys/types.h>
#include <sys/stat.h>

int mkfifo(const char* pathname, mode_t mode);
```
mkfifo()通过pathname返回FIFO对象的名称供进程访问，解决了Pipe的匿名问题。

**对FIFO的写总是往文件末尾添加数据，对FIFO的读总是从开头返回数据。**

### FIFO的销毁
与Pipe相同，在进程中只需关闭FIFO文件即可，FIFO对象的生命周期管理由内核负责。