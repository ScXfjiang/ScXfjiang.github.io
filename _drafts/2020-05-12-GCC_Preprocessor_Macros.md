---
layout:     post
title:      "GCC Preprocessor: Macros"
toc:        true
categories: [Compiler]
---
<br/>
<br/>
Reference: [The C Preprocessor](https://gcc.gnu.org/onlinedocs/cpp/)

GCC Preprocessor主要包含以下几个功能：
1. Inclusion of header files: 头文件包含
2. Macro expansion: 宏的使用
3. Conditional compilation: 条件编译
4. Line control: 略
5. Diagnostics: 预处理阶段的错误检查

本文阐述宏的使用。
<br/>
<br/>

**<span style="font-size:1.25em;">注意，宏的功能仅仅是做文本替换，可以算作是词法分析的一部分，在这个阶段，预处理器还仅仅将程序看作是a sequence of characters。我们认为宏本身并不会做传统意义上编译理论中的词法分析、语法分析和语义分析</span>。**

### 先验知识

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

#### 3. Macro不会替换字符串内的对象
```c
#define X 100

"X, Y, Z"
// "X, Y, Z"

X, Y, Z
// 100, Y, Z
```

### 1. Object-like Macros
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
<br/>
**When the preprocessor expands a macro name, the macro’s expansion replaces the macro invocation, then the expansion is examined for more macros to expand.**
```c
#define BUFFERSIZE BUFSIZE
#define BUFSIZE 1024

BUFFERSIZE
// -> BUFSIZE
// -> 1024
```
通过这个例子我们可以看到Object-like macro的展开是一个迭代的过程，Preprocessor需要多次迭代才能完成全部的宏替换。

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

### 2. Function-like Macros
GCC Preprocessor还允许定义带有参数的Macro，使其看起来像function一样。
```c
#define ADD(x, y) (x + y)

ADD(3, 5)
// -> (3 + 5)
```

<br/>

**Macro arguments are completely macro-expanded before they are substituted into a macro body, unless they are stringized or pasted with other tokens.**

**这一点非常重要，意味着Function-like macro在被调用时实参不会是Function-like macro和Object-like macro，Stringizing和Concatenation情况除外，后面介绍**。

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

#### Tips 03: 调用Function-like macro时参数允许为空
Function-like使用comma$$,$$来判别参数个数，comma的数量一致即可。
```c
#define FOO(arg1, arg2, arg3) arg1, arg2, arg3

FOO(a, b, c)
// -> a, b, c

Foo(, b, c)
// -> , b, c

Foo(,,)
// -> , ,
```

### 3. Stringizing
Stringizing是Function-like macro的常用用法，它将Function-like macro实参转化为string constant。

```c
#define WARN_IF(EXP) \
do { if (EXP) fprintf (stderr, "Warning: " #EXP "\n"); } while (0)

WARN_IF (x == 0);
// do { if (x == 0) fprintf (stderr, "Warning: " "x == 0" "\n"); } while (0);
```

注意，前面提到过：Function-like macro在被调用时如果实参是个macro，那么这个macro在调用前被展开，保证实参不会是macro。
但Stringizing是个例外，一旦对参数进行Stringizing操作，该参数不会被提前展开。

如果我们希望将macro argument展开后的对象转变成string constant怎么做呢？答案是定义two-level macros：
```c
#define xstr(s) str(s)
#define str(s) #s
#define foo 4

str(foo)
// -> "foo"

xstr(foo)
// -> xstr (4)
// -> str (4)
// -> "4"
```

### 4. Concatenation
Concatenation是Function-like macro的常用用法，它可以将Function-like macro的多个参数concate起来。

```c
struct command {
  char *name;
  void (*function) (void);
};

struct command commands[] = {
  { "quit", quit_command },
  { "help", help_command },
  // ...
};

#define COMMAND(NAME)  { #NAME, NAME##_command }

struct command commands[] = {
  COMMAND(quit),
  COMMAND(help),
  // ...
};
```

注意，前面提到过：Function-like macro在被调用时如果实参是个macro，那么这个macro在调用前被展开，保证实参不会是macro。 但Concatenation是个例外，一旦对参数进行Concatenation操作，该参数不会被提前展开。

如果我们希望将macro argument展开后的对象concate在一起，同样需要定义two-level macros。

### 5. Variadic Macros
前面的Function-like macro只允许定义固定数量的参数，Variadic Macros允许定义任意数量的参数。语法如下：
```c
#define eprintf(…) fprintf (stderr, __VA_ARGS__)
```
When the macro is invoked, all the tokens in its argument list after the last named argument (this macro has none), including any commas, become the variable argument. This sequence of tokens replaces the identifier $$\_\_VA\_ARGS\_\_$$ in the macro body wherever it appears.
