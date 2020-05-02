---
layout:     post
title:      "[C++] Rvalue References, Move Semantics, and Perfect Forwarding"
toc:        true
categories: [C/C++]
---
# Ⅰ. Types and Value Categories
在C++中，每个Expression都有两个互相独立的属性：**Type**和**Value Category**；前者是C++ Type System的一部分，后者与对象的生命周期管理有关。
## 1. Types
[cppreference: types](https://en.cppreference.com/w/cpp/language/type)

C++的Type System大家应该比较熟悉，这里不再赘述了。

需要注意的一点是**引用(reference)**属于Types范畴，分为**左值引用(lvalue reference)**和**右值引用(rvalue eference)**；而**左值(lvalue)**和**右值(rvalue)**属于Value Categories范畴，是独立于Types的另一个属性。

## 2. Value Categories
[cppreference: value categories](https://en.cppreference.com/w/cpp/language/value_category)

在C++中，每个Expression都具有下面三个Value Categories之一：
* lvalue(左值)
* prvalue(纯右值)
* xvalue(亡值)

prvalue和xvalue加在一起又被称为rvalue(右值)，**在本文所讨论的主题中，我们一般不会对prvalue和xvalue加以区分，一个Expression要么是左值，要么是右值，一个简化的判别标准是可以取地址的Expression是左值，不可以的是右值**。

常见的左值：
* 变量、函数；

常见的右值：
* 除字符串外的字面量，例如，42, true, nullptr；
* 返回类型是非引用的函数调用；
* 返回类型为对象的右值引用的函数调用，例如，std::move()；


# Ⅱ. Move Semantics

