---
layout:     post
title:      Linux I/O Multiplexing
tuc:        true
categories: [C/C++, System Programming]
---

程序需要处理多路I/O，有以下几种方案：
### 1. 阻塞I/O
单个线程无法在多个文件描述符上阻塞，故每个线程仅能处理一路I/O，这是个比较奢侈的方案，仅使用在少部分场景中。
### 2. 非阻塞I/O
非阻塞I/O在遇到I/O没有准备好的情况时会立即返回，给单个线程处理多路I/O提供了可能。但是这种方式也存在一个弊端：线程必须使用轮询的方式向自己管理的多路I/O发送请求，轮询并不是一个高效的CPU使用方式。
### 3. 异步I/O
### 4. I/O Multiplexing


I/O Multiplexing技术支持程序在多路I/O上阻塞，并且在其中某路I/O准备好时收到通知。

I/O Multiplexing的实现主要由三个：select(), poll(), epoll(), pselect()和ppoll()是select()和poll()的改进版本，主要增加了信号掩码的支持。

在使用I/O Multiplexing时，需要首先构建出我们感兴趣的I/O集合，然后调用一个函数，直到这些I/O中的一个已经准备完成该，该函数才返回。

#### 4.1 select()
```c
#include <sys/select.h>

int select(int nfds, fd_set* readfds, fd_set* writefds, fd_set* exceptfds, struct timeval* timeout);

int pselect(int nfds, fd_set* readfds, fd_set* writefds, fd_set* exceptfds,
            const struct timespec* timeout, const sigset_t* sigmask);
```
其中，readfds, writefds和exceptfds分别代表监控的读、写和异常文件描述符的集合，timeout表示等待时间。

#### 4.2 poll()
```c
#include <poll.h>

int poll(struct pollfd* fd, nfds_t nfds, int timeout);

struct pollfd {
  int fd;
  short events;
  short revents;
};
```

#### 4.3 Event Poll
```c
#include <sys/epoll.h>

int epoll_create1(int flags);
int epoll_ctl(int epfd, int op, int fd, struct epoll_event* event);
int epoll_wait(int epfd, struct epoll_event* events, int maxevents, int timeout);
```

