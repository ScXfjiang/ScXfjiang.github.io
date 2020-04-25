---
layout:     post
title:      "[C++] 运算符重载"
toc:        true
categories: [C/C++]
---
## 1. Intruduction
通常编程语言使用**运算符(Operator)**来表示常用的操作和计算，目的是使代码看起来更加简洁清爽。

例如：
```c++
x + y * z
```
要比
```c++
add(x, multiply(y, z))
```
看上去更清晰。

## 2. Unary/Binary/Ternary Operator
通常编程语言根据**操作数(Oprand)**的个数对Operator进行分类：

| Operator    |    Number of oprands   |         Example             |
|:-----------:|:----------------------:|:---------------------------:|
| Unary       |1                       |    !                        |
| Binary      |2                       |    + - * /                  |
| Tenary      |3                       |    :? (the only one in C++) |

在C++中, Ternay Operator只有一个，即条件运算符。

## 3. C++ Operator Overloading
### 3.1 Overloading Binary Operators
对Binary Operator来说，下面两种定义方式是等价的：
* 被定义成只有一个参数的non-static member function；
* 被定义成有两个参数的non-member function；

如果同时定义，将触发overload resolution。下面的代码将触发重载解析错误：
```c++
class Foo final {
 public:
  void operator+(Foo other) {}
};

void operator+(Foo x, Foo y) {}

int main() {
  Foo x, y;
  x + y;

  return 0;
}

// error: ambiguous overload for ‘operator+’ (operand types are ‘Foo’ and ‘Foo’)
```

### 3.2 Overloading Unary Operators
对Unary Operator来说，下面两种定义方式是等价的：
* 被定义成没有参数的non-static member function；
* 被定义成只有一个参数的non-member fucntioin；

与Binary Operator相同，如果同时定义，将触发overload resolution。

### 3.3 Type Conversion Operator
Type Conversion Operator属于Unary Operator，因为有些特殊且较为常用，所以单独拿出来介绍。

在C++中实现Type Conversion从根本上说只有两种方式：
* Constructor
* Conversion Operator

其他方式是这两种方式的包装，例如static_cast()实际上调用二者之一。二者都可以被explicit修饰，是否使用explicit取决于是否希望发生Copy Initialization，这部分内容详见我的另一篇博客：[C++ Constructor设计](http://127.0.0.1:4000/c/c++/2018/06/22/CPP_Design_Constructor.html)。

使用Constructor实现Type Conversion的问题在于Constructor没有返回值，只能实现别人向自己的转换，不能实现自己向别人的转换。Conversion Operator有返回值，所以这个问题得到了解决。

例如：
```c++
class Foo final {
 public:
  operator bool() { return true; }
};
```
