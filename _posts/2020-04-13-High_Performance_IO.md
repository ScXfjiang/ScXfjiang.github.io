---
layout:     post
title:      High-Performance I/O
toc:        true
categories: [System Programming, C/C++]
---
## Ⅰ. 对Cache的优化
磁盘I/O的Cache可以分为以下三个Level：

| Level       |                                                                     |
| ----------- | ------------------------------------------------------------------- |
| User Cache  | 绝大多数程序员对Cache的优化在这个层次，例如C Standard I/O。                |
| Page Cache  | Linux系统上所有的I/O操作均要经过Page Cache，这部分是内核缓冲，无法控制。     |
| Disk Cache  | 基本上所有磁盘都有Cache。                                              |

如果不是库的重度开发者，通常我们不需要考虑Cache优化问题，直接使用指定格式文件的I/O库即可，例如JSON、TFRecord等。

PS：即便不考虑Cache优化问题，也要理解I/O在不同级别想要解决的问题及相关抽象。例如：系统级别的POSIX I/O仅仅是将文件看做是没有任何格式的一个个字节，提供Write/Read/Seek等最基本的操作；语言级别的C Standard I/O是对POSIX I/O的封装，主要目的是管理User Cache，减少不必要的系统调用，等等。

## Ⅱ. 对并发的优化
TODO
