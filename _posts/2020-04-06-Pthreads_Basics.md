---
layout:     post
title:      Pthreads Basics
toc:        true
categories: [Multithread Programming, C/C++]
---
## Pthreads
Linux内核只为线程提供了底层原语，多线程模型是在用户空间实现的，POSIX对Linux线程库进行了标准化，产生了Pthreads，它是目前C/C++项目的主要线程解决方案。
## (一) 线程的创建、终止和错误处理
### 1. 线程的创建
```c
int pthread_create(pthread_t *thread,
                   const pthread_attr_t *attr,
                   void *(*start_routine)(void *),
                   void *arg);
```
注：start_routine返回void\*，指向Exit Code，如果不需要获取Exit Code，返回NULL即可；此外，用户还可以调用pthread_exit(void\* rval_ptr)终止线程并设置指向Exit Code的指针，如果不需要获取Exit Code，传入NULL即可。
#### Thead ID
类似于**Process Id (PID)**，每个线程都对应一个**Thread ID (TID)**，其类型是pthread_t，POSIX并没有规定它必须是一个算数类型。Process ID由Linux内核分配，在整个系统中是唯一的；Thread ID由Pthreads分配，它只有在所属的进程上下文中才有意义。
```c
// 获取Thread ID
pthread_t pthread_self();

// 比较Thread ID
int pthread_equal(pthread_t t1, pthread_t t2)
```

### 2. 线程的终止、Exit Code与资源回收
线程在以下情况下会终止：
1. 线程函数执行完毕正常返回，这和main()函数结束类似

2. 线程调用pthread_exit()主动终止，这和调用exit()函数类似
```c
void pthread_exit(void* rval_ptr);
```
3. 被同一进程内另一个线程通过pthread_cancel()函数取消，这和通过kill()发送SIGKILL信号类似
```c
int pthread_cancel(pthread_t thread);
```
如果线程主动退出(情况1和2)且需要获取Exit Code，需要将线程函数的返回值或rval_ptr指向Exit Code；如果线程被取消，由rval_ptr指定的内存单元就设置为PTHREAD_CANCELED。可以通过pthread_join()获取Exit Code。 

#### Joinable vs. Detached (资源回收)

在任何一个时间点上，一个线程要么处在joinable状态，要么处在detached状态。一个joinable的线程可以被其他线程回收资源，在被其他线程回收之前，相关资源是不释放的；如果一个线程处在detached状态，相关资源在线程终止时立即被系统回收。线程在创建时默认是joinable状态，为了避免资源泄漏，每个joinable的线程都应该要么被显示地被其他线程join，要么将自己设置成detached状态。

```c
// 调用线程阻塞直到thread终止
int pthread_join(pthread_t thread, void **rval_ptr);

// 将thread设置为detached状态
int pthread_detach(pthread_t thread);
```

---
[例 ] 获取线程的Exit Code
```c++
// 本文仅介绍Pthreads主体逻辑，不做任何防御式编程。

#include <pthread.h>
#include <stdlib.h>
#include <stdio.h>

void* thrd1_func(void* arg) {
  printf("thread 1 is running\n");
  return (void*)"thread 1 exit\n";
}

void* thrd2_func(void* arg) {
  printf("thread 2 is running\n");
  pthread_exit((void*)"thread 2 exit\n");
}

int main() {
  pthread_t thread1;
  pthread_t thread2;
  void* ret1 = NULL;
  void* ret2 = NULL;

  pthread_create(&thread1, NULL, thrd1_func, NULL);
  pthread_create(&thread2, NULL, thrd2_func, NULL);

  pthread_join(thread1, &ret1);
  printf("thread 1 exit code: %s", (char*)ret1);
  pthread_join(thread2, &ret2);
  printf("thread 2 exit code: %s", (char*)ret2);

  return 0;
}

// output
// thread 1 is running
// thread 2 is running
// thread 1 exit code: thread 1 exit
// thread 2 exit code: thread 2 exit
```
---

### 3. 错误处理
多数Pthreads函数在成功时返回0，否则返回error number。

## (二) 线程同步
Pthreads使用同步对象实现线程间的同步，本节将介绍其中的Mutex、RWLock、Condition Variable和Barrier。它们的编程范式基本相同：**首先初始化一个多个线程可见的共享同步对象，然后各个线程使用该共享对象进行同步。**

### 1. Mutex
```c
// init and destroy
int pthread_mutex_init(pthread_mutex_t* mutex,
                       const pthread_mutexattr_t* mutex_attr);
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;
int pthread_mutex_destroy(pthread_mutex_t* mutes);

// basic
int pthread_mutex_lock(pthread_mutex_t* mutex);
int pthread_mutex_unlock(pthread_mutex_t* mutex);

// try
int pthread_mutex_trylock(pthread_mutex_t* mutex);

// timed
int pthread_mutex_timedlock(pthread_mutex_t* mutex
                            const struct timespec* tsptr);
```

### 2. Readers-Writer Lock
```c
// init and destroy
int pthread_rwlock_init(pthread_rwlock_t* rwlock,
                        const pthread_rwlockattr_t* attr);
int pthread_rwlock_destroy(pthread_rwlock_t* rwlock);
pthread_rwlock_t rwlock = PTHREAD_RWLOCK_INITIALIZER;

// basic
int pthread_rwlock_rdlock(pthread_rwlock_t* rwlock);
int pthread_rwlock_wrlock(pthread_rwlock_t* rwlock);
int pthread_rwlock_unlock(pthread_rwlock_t* rwlock);

// try
int pthread_rwlock_tryrdlock(pthread_rwlock_t* rwlock);
int pthread_rwlock_trywrlock(pthread_rwlock_t* rwlock);

// timed
int pthread_rwlock_timedrdlock(pthread_rwlock_t* rwlock,
                               const struct timespec* tsptr);
int pthread_rwlock_timedwrlock(pthread_rwlock_t* rwlock,
                               const struct timespec* tsptr);
```

### 3. Condition Variable
Condition variable允许一组线程根据Condition进行同步。Condition本身是由Mutex保护的，Condition的改变和检查之前都必须先锁住Mutex。

```c
// init and destroy
int pthread_cond_init(pthread_cond_t* cond,
                      const pthread_condattr_t* cond_attr);
pthread_cond_t cond = PTHREAD_COND_INITIALIZER;
int pthread_cond_sdestroy(pthread_cond_t* cond);

// basic
int pthread_cond_signal(pthread_cond_t* cond);
int pthread_cond_broadcast(pthread_cond_t* cond);
int pthread_cond_wait(pthread_cond_t* cond, pthread_mutex_t* mutex);

// timed
int pthread_cond_timedwait(pthread_cond_t* cond, pthread_mutex_t mutex,
                           const struct timespec* tsptr);
```

下面以一个"Producer-Consumer"例子展开对Condition variable的讨论：有两个线程prepare_msg()和process_msg()，prepare_msg()负责生产msg并将其放入消息队列queue中，process_msg()负责消费msg，同步逻辑是如果消息队列中没有msg，那么process_msg()挂起，prepare_msg()每准备好一个msg就发送信号给process_msg()，该信号会唤醒process_msg()使其消费msg。
```c++
// 本文仅介绍Pthreads主体逻辑，不做任何防御式编程。

#include <pthread.h>
#include <queue>
#include <iostream>
#include <cassert>

class Message final {
 public:
  Message(const int val) : val_{val} {}
  int GetVal() const { return val_; }
  void SetVal(const int val) { val_ = val; }

 private:
  int val_;
};

constexpr int NUM_MSG = 20;

pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;
pthread_cond_t cond = PTHREAD_COND_INITIALIZER;

std::queue<Message> queue;

void* prepare_msg(void* arg) {
  for (int i = 0; i < NUM_MSG; ++i) {
    pthread_mutex_lock(&mutex);
    Message msg(i);
    queue.emplace(msg);
    std::cout << "thrd 0: prepare and enqueue msg (" << msg.GetVal() << ")" << std::endl;
    pthread_mutex_unlock(&mutex);
    pthread_cond_signal(&cond);
  }
}

void* process_msg(void* arg) {
  while (true) {
    pthread_mutex_lock(&mutex);
    while (queue.empty()) { pthread_cond_wait(&cond, &mutex); }
    Message msg = queue.front();
    queue.pop();
    std::cout << "thrd 1: process and dequeue msg (" << msg.GetVal() << ")" << std::endl;
    pthread_mutex_unlock(&mutex);
  }
}

int main() {
  pthread_t producer;
  pthread_t consumer;

  pthread_create(&producer, NULL, prepare_msg, NULL);
  pthread_create(&consumer, NULL, process_msg, NULL);

  pthread_join(producer, NULL);
}

// out
// thrd 0: prepare and enqueue msg (0)
// thrd 0: prepare and enqueue msg (1)
// thrd 0: prepare and enqueue msg (2)
// thrd 0: prepare and enqueue msg (3)
// thrd 0: prepare and enqueue msg (4)
// thrd 0: prepare and enqueue msg (5)
// thrd 0: prepare and enqueue msg (6)
// thrd 0: prepare and enqueue msg (7)
// thrd 0: prepare and enqueue msg (8)
// thrd 0: prepare and enqueue msg (9)
// thrd 0: prepare and enqueue msg (10)
// thrd 0: prepare and enqueue msg (11)
// thrd 0: prepare and enqueue msg (12)
// thrd 0: prepare and enqueue msg (13)
// thrd 0: prepare and enqueue msg (14)
// thrd 0: prepare and enqueue msg (15)
// thrd 0: prepare and enqueue msg (16)
// thrd 1: process and dequeue msg (0)
// thrd 1: process and dequeue msg (1)
// thrd 1: process and dequeue msg (2)
// thrd 1: process and dequeue msg (3)
// thrd 1: process and dequeue msg (4)
// thrd 1: process and dequeue msg (5)
// thrd 1: process and dequeue msg (6)
// thrd 1: process and dequeue msg (7)
// thrd 1: process and dequeue msg (8)
// thrd 1: process and dequeue msg (9)
// thrd 1: process and dequeue msg (10)
// thrd 1: process and dequeue msg (11)
// thrd 1: process and dequeue msg (12)
// thrd 1: process and dequeue msg (13)
// thrd 1: process and dequeue msg (14)
// thrd 1: process and dequeue msg (15)
// thrd 1: process and dequeue msg (16)
// thrd 0: prepare and enqueue msg (17)
// thrd 0: prepare and enqueue msg (18)
// thrd 0: prepare and enqueue msg (19)
// thrd 1: process and dequeue msg (17)
// thrd 1: process and dequeue msg (18)
// thrd 1: process and dequeue msg (19)
```

几个常见的问题：

1. pthread_cond_wait()实现原理，为什么pthread_cond_wait()需要传入一个mutex？

   glibc源码：[glibc pthread_cond_wait.c](https://github.com/bminor/glibc/blob/master/nptl/pthread_cond_wait.c)

   pthread_cond_wait()的实现简化如下：
   ```c++
   int pthread_cond_wait(cond, mutex) {
     unlock(mutex);
     wait(cond);
     lock(mutex);
   }
   ```
   一次完整的生产消费关系如下图所示：
   ![condition variable](/assets/images/posts/post_2020-04-06-Pthreads_Basics_cond_var.jpg)

    每个红框都是由mutex保护的critical section，Condition的修改和检查均要有mutex保护。**传入mutex的原因**是希望挂起线程的时候将mutex释放掉，让其他线程修改或检查消息队列，等信号到来唤醒时候再去把mutex抢回来处理消息。

2. Q: 为什么使用while判断状态而不是if？

   A: 为了处理在多个线程竞争中某个线程唤醒后Condition仍然不满足的情况。如果发生这种情况，while可以保证线程再次进入阻塞状态等待唤醒信号。

3. Q: pthread_cond_signal()是否可以在由mutex保护的临界区中？

   A: 可以，有时候甚至是必须的。只要允许其他线程在pthread_cond_signal()操作前修改消息队列就可以，反之必须放在临界区中。

4. Q: process and dequeue msg的逻辑是否可以放在由mutex保护的临界区外？

   A: 不行，这里涉及消息队列的修改，必须放入临界区中。

### 4. Barrier
Barrier允许多个线程等待，直到指定数量的线程都达到某点，然后从该点继续执行。
```c
// init and destroy
int pthread_barrier_init(pthread_barrier_t* barrier,
                         const pthread_barrierattr_t* attr,
                         unsigned int count);
int pthread_barrier_destroy(pthread_barrier_t* barrier);

// basics
int pthread_barrier_wait(pthread_barrier_t* barrier);
```
Barrier在初始化时指定在允许所有线程继续执行前，必须到达Barrier的线程数目（Barrier Count）。到达pthread_barrier_wait()的线程在未满足Barrier Count的情况下会挂起，等待Barrier Count的满足；如果该线程是最后一个调用pthread_barrier_wait()的线程且满足了Barrier Count，所有线程被唤醒继续向前执行。