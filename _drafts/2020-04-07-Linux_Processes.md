---
layout:     post
title:      Linux Processes
toc:        true
categories: [System Programming, C/C++]
---
# Ⅰ. 进程控制
## (一) 什么是进程？
**进程(Process)**是Linux系统中仅次于文件的基本抽象，指的是正在运行的程序，包括**程序本身**和**运行环境**。
## (二) 进程的创建
大多数操作系统只是提供单个系统调用来启动新的进程，而Linux提供了两个系统调用：fork()和exec()，这将**创建进程**和**加载二进制镜像**分离，虽然在大多数情况下这两个任务是顺序执行的，但分离后对两个任务可以有更多的空间来实践和改进。
### 1. Process ID
每个进程都有一个全局唯一的**Process ID**。
* Process ID有操作系统分配，该ID虽然是全局唯一的，但可以复用；
* 操作系统中有一些系统专用进程，例如Process ID为$$0$$的进程通常是*调度进程*，Process ID为$$1$$的进程通常是*init进程*；
下面是获取Process Id的接口：

```c
#include <unistd.h>

pid_t getpid();   // 获取当前进程process ID
pid_t getppid();  // 获取当前进程parent process ID
```

### 2. fork()
一个现有的进程可以通过调用fork()函数创建一个新进程。
```c
#include <unistd.h>

pid_t fork();
```

调用fork()的进程被称为**Parent Process**，由fork()创建的新进程被称为**Child Process**。在调用成功时，fork()返回两次，在Child Process的返回值是$$0$$，在Parent Process的返回值是Child Process Id；在调用失败时，fork()仅在Parent Process返回$$-1$$，不会创建Child Process。

Child Process和Parent Proces**共享代码段**，在成功调用fork()创建Child Process后，Child Process和Parent Process会分别继续执行后面的指令。

fork()有以下两种用法：
1. Parent Process希望复制自己，使Parent Process和Child Process分别执行同一代码段的不同分支。这在网络服务进程中是常见的：Parent Process等待Client的请求，当请求达到时调用fork()，使Child Process处理此请求，Parent Process则继续等待下一个请求。
2. Parent Process希望新建一个进程执行不同的程序，这种情况下Child Process在被创建后立刻调用exec()。一个常见的例子是shell。

### 3. exec()
不存在单一的exec()函数，而是存在一系列exec()函数。当进程调用一种exec()函数时，该进程执行的程序完全替换成新程序。
```c
#include <unistd.h>

extern char **environ;

int execl(const char *path, const char *arg, ...
          /* (char  *) NULL */);
int execlp(const char *file, const char *arg, ...
           /* (char  *) NULL */);
int execle(const char *path, const char *arg, ...
           /*, (char *) NULL, char * const envp[] */);
int execv(const char *path, char *const argv[]);
int execvp(const char *file, char *const argv[]);
int execvpe(const char *file, char *const argv[], char *const envp[]);
```

## (三) 进程的销毁
### 1. 进程终止方式
5种正常终止方式：
* 从main()函数返回；
* 调用exit()；
* 调用_exit()或者_Exit()；
* 最后一个线程返回；
* 最后一个线程调用pthread_exit()；

3中异常终止方式：
* 调用abort()；
* 接到一个信号；
* 最后一个线程被其他线程取消；

```c
#include <stdlib.h>

void exit(int status);
```

### 2. atexit()
atexit()用来注册一些在进程结束时要调用的函数。
```c
#include <stdlib.h>

int atexit(void (*handler)(void));
```
atexit()调用成功时，会注册handler作为终止函数，handler会在程序调用exit()或从main()函数返回时运行。

### 3. 等待Child Process终止
TODO

# Ⅱ. 进程间通信
# Ⅲ. 跨机进程间通信
