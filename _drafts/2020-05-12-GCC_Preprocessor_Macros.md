---
layout:     post
title:      "GCC Preprocessor: Macros"
toc:        true
categories: [Compiler]
---
Reference: [The C Preprocessor](https://gcc.gnu.org/onlinedocs/cpp/)

GCC Preprocessor主要包含以下几个功能：
1. Inclusion of header files: 头文件包含
2. Macro expansion: 宏的使用
3. Conditional compilation: 条件编译
4. Line control: 略
5. Diagnostics: 预处理阶段的错误检查

本文阐述宏的使用。

## 先验知识

#### 1. 行连接与注释处理
Preprocessor进行预处理指令和宏展开前会首先执行下面两个操作：
1. 将位于换行符前的反斜杠$$\backslash$$和换行符删除，将若干指令行合并成为一行；
2. 将注释替换成1个空白符；

#### 2. Macro的作用域
Macro definitions take effect at the place you write them.

```c
foo = X;
#define X 4
bar = X;

// foo = X
// bar = 4
```

## 1. Object-like Macros
```c
#define BUFFER_SIZE 1024

char* buf = (char*)malloc(BUFFER_SIZE);
// -> char* buf = (char*)malloc(1024);
```

```c
#define NUMBERS 1, \
                2, \
                3

int x[] = { NUMBERS };
// -> int x[] = { 1, 2, 3 };
```

#### Tpis 01: Object-like Macro的嵌套
When the preprocessor expands a macro name, the macro’s expansion replaces the macro invocation, then the expansion is examined for more macros to expand.
```c
#define BUFFERSIZE BUFSIZE
#define BUFSIZE 1024

BUFFERSIZE
// -> BUFSIZE
// -> 1024
```
通过这个例子我们可以看到Object-like macro的展开是一个迭代的过程，Preprocessor需要多次迭代才能完成全部的宏替换，这一点与Function-like macro相反。

我们再看一个例子：
```c
#define BUFSIZE 1020
#define BUFFERSIZE BUFSIZE
#undef BUFSIZE
#define BUFSIZE 37

BUFFERSIZE
// -> BUFSIZE
// -> 37
```
第一次迭代将BUFFERSIZE替换成BUFSIZE，第二次迭代将BUFSIZE替换成37。

## 2. Function-like Macros
GCC Preprocessor还允许定义带有参数的Macro，使其看起来像function一样。
```c
#define ADD(x, y) (x + y)

ADD(3, 5)
// -> (3 + 5)
```

### Macro Arguments


#### Tips 01: Function-like macro仅在有括号时才会被展开
Function-like macro is only expanded if its name appears with a pair of parentheses after it. If you write just the name, it is left alone. This can be useful when you have a function and a macro of the same name, and you wish to use the function sometimes.
```c
extern void foo();
#define foo() /* optimized inline version */

foo();        // Macro foo()
funptr = foo; // function foo
```

#### Tips 02: 定义Function-like macro的时候macro名字后不要有空格
否则会被解析成Object-like macro
```c
#define lang_init ()    c_init()

lang_init()
// -> () c_init()()
```

