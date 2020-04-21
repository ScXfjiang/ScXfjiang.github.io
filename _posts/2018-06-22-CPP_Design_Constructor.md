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
方向：根据类的使用场景明确对象的初始化方式，以此设计构造函数。

例如：
* 如果不希望以Copy Intialization的方式初始化对象，那么就将构造函数声明成explicit；
* 如果不希望编译器默认创建Copy Constructor就将其delete掉；
* ...

有了思考方向，构造函数的设计并不复杂，后面再陆续补充一些细节。
