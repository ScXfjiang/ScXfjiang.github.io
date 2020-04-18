---
layout:     post
title:      High-Performance I/O
toc:        true
categories: [System Programming, C/C++]
---
## Ⅰ. 对Cache的优化

| Level       |                                                                     |
| ----------- | ------------------------------------------------------------------- |
| User Cache  | 绝大多数程序员对Cache的优化在这个层次，例如C Standard I/O。                |
| Page Cache  | Linux系统上所有的I/O操作均要经过Page Cache。                            |
| Disk Cache  | 基本上所有磁盘都有Cache。                                              |

## Ⅱ. 对并发的优化
TODO