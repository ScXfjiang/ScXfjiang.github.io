---
layout:     post
title:      Linux Signal
toc:        true
categories: [System Programming, C/C++]
---
<br/>
<br/>

信号是一种软件中断，它为进程提供了处理异步事件的机制。异步事件可由系统外部产生，例如用户按下$$CTRL + C$$；也可由程序或内核产生，例如程序发生除$$0$$操作。此外，作为进程间通信的一种基本方式，信号可由一个进程发送给另一个进程。

## 1. Signals Supported by Linux
Linux使用正整数表示Signal种类，并赋予其以$$SIG$$开头的别名，例如$$SIGINT$$、$$SIGABRT$$、$$SIGKILL$$等。

下面列出几个例子和它们的默认处理方式：

| Signal      |                                     Description                                                       |      Default Action         |
|:-----------:| ----------------------------------------------------------------------------------------------------- | --------------------------- |
| SIGABRT     | 由abort()函数产生并发送给调用者。<br/>[例] assert()函数在条件不满足时就会调用abort()发送SIGNAL。                | Teminate with core dump     |
| SIGALRM     | 由alarm()或setitimer()函数产生并发送给调用者。                                                             | Teminate                    |
| SIGBUS      | Hardware or memory alignment error                                                                    | Teminate with core dump     |
| SIGCHLD     | 当Child Process结束时，内核发送SIGCHLD给Parent Process。<br/>如果对Child Process的生命周期感兴趣，需要修改此默认行为。|  Ignored  |
| SIGCONT     | The kernel sends this signal to a process when the process is resumed after being stopped. <br/>[例] This signal is commonly used by terminals or editors that wish to refresh the screen.|  Ignored  |
| SIGFPE      | Arithemetic exception, and not solely those related to floating-point operations.                     | Teminate with core dump     |
| ...         | ...                                                                                                   | ...                         |

Linux所支持的全部Signal类型及其详细描述可在任意一本Linux编程手册中查到。
