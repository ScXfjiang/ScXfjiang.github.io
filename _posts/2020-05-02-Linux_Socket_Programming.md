---
layout:     post
title:      Linux Socket Programming
toc:        true
categories: [System Programming, C/C++]
---
# Ⅰ. Introduction
Socket Programming是POSIX标准的一部分，它实现了不同计算机进程之间的互相通信。尽管Socket API可以使用不同的网络协议，但本文的讨论仅限于Internet的事实标准：TCP/IP。

# Ⅱ. Linux Socket Programming Basics
![TCP Client/Server Big Picture](/assets/posts/Linux_Socket_Programming/TCP_Server_Client_Big_Picture.png)
## 1.1 socket()
为了执行网络Socket I/O，一个进程首先要使用socket()初始化一个**Socket Endpoint**。与打开文件时返回的**File Descriptor**类似，socket()在成功时返回**Socket Descriptor (sockfd)**供其他接口访问Socket Endpoint。
```c
#include <sys/socket.h>

int socket(int family, int type, int protocol);
```

常见的参数：

| family      |                        说明                                        |
| ----------- | ------------------------------------------------------------------ |
| AF_INET     | IPv4协议                                                           |
| AF_INET6    | IPv6协议                                                           |

| type        |                        说明                                        |
| ----------- | ------------------------------------------------------------------ |
| SOCK_STREAM | 字节流套接字                                                       |
| SOCK_DGRAM  | 数据报套接字                                                       |
| SOCK_RAW    | 原始套接字                                                         |

| protocol    |                        说明                                        |
| ----------- | ------------------------------------------------------------------ |
| IPPROTO_TCP | TCP传输协议                                                        |
| IPPROTO_UDP | UDP传输协议                                                        |

## 1.2 bind()
初始化Socket Endpoint的时候没有设置IP Address和Port，需要调用bind()将IP Address和端Port与Socket Endpoint绑定。如果一个Server/Client并没有调用bind()进行IP Address和Port的绑定，那么内核就会为其设置本机的IP Address并临时分配一个Port。这种行为一般仅适用于Client，而Server一般要暴露一个众所周知的Port。
```c
#include <sys/socket.h>

int bind(int sockfd, const struct sockaddr* addr, socklen_t addrlen);
```

## 1.3 connect()
Client调用connect()与Server建立连接。例如，如果是TCP Socket，那么connect()会触发3-way handshake。
```c
#include <sys/socket.h>

int connect(int sockfd, const struct sockaddr* servaddr, socketlen_t addrlen);
```

## 1.4 listen()
listen()仅由TCP Server调用，它指示Linux Kernel此Socket Endpoint应该接受指向它的连接请求，第二个参数规定了Linux Kernel应该为该Socket Endpoint排队的最大连接数。
```c
#include <sys/socket.h>

int listen(int socketfd, int backlog);
```

为了了解backlog参数，需要了解Linux Kernel为任何一个Listening Socket维护两个队列：
1. 未完成的队列（incomplete connection queue）
2. 已完成的队列（completed connection queue）

如下图所示：
![Two Queues for TCP Listening Socket](/assets/posts/Linux_Socket_Programming/two_queues_for_TCP_listening_socket.png)

## 1.5 accept()
accept()仅由TCP Server调用，它接受一个Listening Socket File Descriptor，并返回从completed connection queue中返回一个已完成的连接，使用Connected Socket File Descriptor表示，如果completed connection queue为空，那么进程进入睡眠。

```c
#include <sys/socket.h>

int accept(int sockfd, struct sockaddr* cliaddr, socklen_t* addrlen);
```

区分Listening Socket和Connected Socket非常重要，一个服务器通常仅仅创建一个Listening Socket，它在该服务器的生命周期内一直存在，之后Linux Kernel为每个已经连接的Client创建一个Connected Socket，当完成服务时，这个Connected Socket也就会被关闭了。

如果我们对Client的IP Address和Port不感兴趣，那么就将第二、三个参数设置为空指针。

## 1.6 close()
关闭一个Socket，这个接口和关闭文件的是同一个。
```c
#include <unistd.h>

int close(int sockfd);
```
