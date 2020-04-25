---
layout:     post
title:      C++ Smart Pointer
toc:        true
categories: [C/C++]
---
## 1. 概述
C++推荐用户使用RAII的方式管理对象的声明周期，Smart Pointer是重要的工具之一，它是裸指针的一个wrapper。

C++中共有四种Smart Pointer:
* std::auto_ptr (已完全被C++11 std::unique_ptr替代)
* std::unique_ptr
* std::shared_ptr
* std::weak_ptr

## 2. std::unique_ptr
```c++
#include <memory>

// Manage a single object
template<class T, class Deleter = std::default_delete<T>>
class unique_ptr;

// Manage a dynamically-allocated array of objects
template<class T, class Deleter>
class unique_ptr<T[], Deleter>;
```
* std::unique_ptr实现**exclusive ownership**语义（注：拥有的意思是负责析构），**不允许复制**(delete掉Copy Constructor和Copy Assignment Operator)，**只允许移动**；
* 在默认情况下，std::unique_ptr和裸指针有着相同的大小和速度；
* std::unique_ptr可以方便地转换成std::shared_ptr（std::shared_ptr提供了以std::unique_ptr为参数的Constructor和Move Assignment Operator）；
* 可自定义Deleter；

## 3. std::shared_ptr & std::weak_ptr
TODO
