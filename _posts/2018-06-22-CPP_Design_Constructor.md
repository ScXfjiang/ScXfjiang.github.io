---
layout:     post
title:      C++ Constructor设计
toc:        true
categories: [C/C++]
---
## 1. C++对象初始化方式
C++在不同的上下文会选择不同的对象初始化方式，这部分比较琐碎不适合记忆，本节仅阐述与Constructor设计相关且比较常见的情形。

如果需要了解某种初始化方式的细节，请查阅如下表格：

| --------------------------- | --------------------------------------------------------------------------- |
| Direct Initialization       | [link](https://en.cppreference.com/w/cpp/language/direct_initialization)    |
| Default Initialization      | [link](https://en.cppreference.com/w/cpp/language/default_initialization)   |
| Copy Initialization         | [link](https://en.cppreference.com/w/cpp/language/copy_initialization)      |
| Reference Initialization    | [link](https://en.cppreference.com/w/cpp/language/reference_initialization) |
| Constant Initialization     | [link](https://en.cppreference.com/w/cpp/language/constant_initialization)  |
| Zero Initialization         | [link](https://en.cppreference.com/w/cpp/language/zero_initialization)      |
| Value Initialization        | [link](https://en.cppreference.com/w/cpp/language/value_initialization)     |
| Aggregate Initialization    | [link](https://en.cppreference.com/w/cpp/language/aggregate_initialization) |
| List Initialization         | [link](https://en.cppreference.com/w/cpp/language/list_initialization)      |

### 1.1 Direct Initialization
效果：调用non-default constructor完成对象的构造和初始化。
```c++
// 1. 
T object(arg1, arg2, ...);
new T(arg1, arg2, ...)

// 2. Explicit type conversion
T(other)
T(arg1, arg2, ...)

// 3. static_cast
static_cast<T>(other)

// constructor initializer list
Class::Class() : member(arg1, arg2, ...) {...}
```

### 1.2 Default Initialization
效果：调用default constructor完成对象的构造和初始化。
```c++
// 1.
T object;
new T

// 2. 当class的non-static data member没有在constructor initializer list中被初始化时会首先
// 进行 Default Initialization，然后再执行constructor函数体。
```

### 1.3 Copy Initialization
效果：调用non-explicit constructor完成对象的构造和初始化。
```c++
// 1. 赋值符号
T object = other;

// 2. function, pass argument by value
func(other);

// 3. function, return object
return object;
```

## 2. 设计Constructor
#### Item 01: 显示声明使用或拒绝编译器默认提供的默认构造函数。

#### Item 02: Initialization和Copy Constructor的关系是雷锋和雷峰塔的关系。

#### Item 03: Copy Constructor和Move Constructor一般加const。

#### Item 04: ......
