---
layout:     post
title:      Python/C Integration
toc:        true
categories: [Python, C/C++]
---
Python语言被设计成可以和C/C++、Java、C#等编程语言混合编程。Python解释器就是使用C/C++语言编写的，所以能够和这些语言混合编程并不奇怪，只需要在设计Python解释器时留好接口即可，实际上Python之所以被称为"脚本"语言，就是因为它可以和其他语言进行混合编程，Python只需要处理好上层的"脚本"逻辑，将具体底层逻辑的控制权交给更擅长的其他编程语言处理。

Python和C/C++混合编程分为两个方面：
* Python调用C/C++，在本文称之为Extending Python with C/C++；
* C/C++调用Python，在本文称之为Embedding Python in C/C++；

本文重点介绍前者，后者有空补充。

# Ⅰ. Extending Python with C/C++
在Python中调用C/C++代码可分为两大部分内容：
* C Extension Module: 定义函数
* C Extension Type: 定义类

## 1. C Extension Module
### 1.1 All by Hand
Python解释器本身由C/C++编写，它留有一些C/C++接口使得用户可编写指定格式的C/C++程序并以.so动态库的形式与Python解释器交互。

Python解释器与.so动态库的交互方式非常简单，直接import然后把它当做是普通的Python Module即可。例如hello.so，可以直接import hello即可使用。

我们下面看一个例子：

```c++
/********************************************************************
 * hello.c
 * A simple C extension module for Python, called "hello"; compile
 * this into a ".so" on python path, import and call hello.message;
 ********************************************************************/

#include <Python.h>
#include <string.h>

/* module functions */
static PyObject *message(PyObject *self, PyObject *args) {
  char *fromPython, result[1024];
  // convert Python -> C
  if (!PyArg_Parse(args, "(s)", &fromPython)) {
    return NULL;  // null = raise exception
  } else {
    strcpy(result, "Hello, ");
    strcat(result, fromPython);
    // convert C -> Python
    return Py_BuildValue("s", result);
  }
}

/* registration table  */
static PyMethodDef hello_methods[] = {
    {"message", message, METH_VARARGS, "func doc"}, // name, &func, fmt, doc
    {NULL, NULL, 0, NULL}                           // end of table marker
};

/* module definition structure */
static struct PyModuleDef hellomodule = {
    PyModuleDef_HEAD_INIT, "hello", // name of module
    "mod doc",                      // module documentation, may be NULL
    -1,                             // size of per-interpreter module state, -1 = in global vars
    hello_methods                   // link to methods table
};

/* module initializer */
PyMODINIT_FUNC PyInit_hello() { return PyModule_Create(&hellomodule); }
```
<br/>
将其编译为.so动态库：
```bash
export PY_INCLUDE=/usr/include/python3.6
gcc hello.c -I${PY_INCLUDE} -fPIC -shared -o hello.so
```

<br/>
之后将hello.so当做是普通的Python Module使用：
```python
>>> import hello
>>> hello.message("World")
'Hello, World'
>>> dir(hello)
['__doc__', '__file__', '__loader__', '__name__', '__package__', '__spec__', 'message']
```

<br/>
这个例子的重点是使用Python预留的C/C++接口将C/C++程序编译成Python解释器可以import的.so动态库。这种方法最接近本质，但操作起来比较麻烦，需要用户记住很多Python预留的API且要按照指定格式去编写C/C++代码。

下面我们使用SWIG工具自动生成上述C/C++代码。

### 1.2 Use SWIG
```c++
/********************************************************************
 * hellolib.h
 * Define hellolib.c exports to the C namespace, not to Python
 * programs--the latter is defined by a method registration
 * table in a Python extension module's code, not by this .h;
 ********************************************************************/

extern char *message(char *label);
```


```c++
/*********************************************************************
 * hellolib.c
 * A simple C library file, with a single function, "message",
 * which is to be made available for use in Python programs.
 * There is nothing about Python here--this C function can be
 * called from a C program, as well as Python (with glue code).
 *********************************************************************/

#include <string.h>
#include <hellolib.h>

static char result[1024];

char *message(char *label) {
  strcpy(result, "Hello, ");
  strcat(result, label);
  return result;
}
```

```c++
/******************************************************
 * hellolib.i
 * Swig module description file, for a C lib file.
 * Generate by saying "swig -python hellolib.i".
 ******************************************************/

%module hellowrap

%{
#include <hellolib.h>
%}

extern char *message(char*);    /* or: %include "../HelloLib/hellolib.h"   */
                                /* or: %include hellolib.h, and use -I arg */
```

首先使用Swig生成wrapper文件：
```bash
swig -python hello.i
```
这会生成两个新文件：hello.py和hello_wrap.c

然后编译：
```bash
gcc hello.c hello_wrap.c -I${PY_INCLUDE} -I. -fPIC -shared -o _hello.so
```

使用，直接import hello即可，hello.py回链接_hello.so：
```python
>>> import hello
>>> hello.message("World")
'Hello, World'
```
## 2. C Extension Type
### 2.1 All by hand
略
### 2.2 Use SWIG
```c++
// number.h

class Number {
 public:
  Number(int start);    // constructor
  ~Number();            // destructor
  void add(int value);  // update data member
  void sub(int value);
  int square();    // return a value
  void display();  // print data member
  int data;
};
```

```c++
// number.cpp

#include "number.h"
#include "stdio.h"

Number::Number(int num) {
  data = num;                  // python print goes to stdout
  printf("Number: %d\n", num);  // or: cout << "Number: " << data << endl;
}

Number::~Number() { printf("~Number: %d\n", data); }

void Number::add(int value) {
  data += value;
  printf("add %d\n", value);
}

void Number::sub(int value) {
  data -= value;
  printf("sub %d\n", value);
}

int Number::square() {
  return data * data;  // if print label, fflush(stdout) or cout << flush
}

void Number::display() { printf("Number=%d\n", data); }
```

```c++
/********************************************************
 * number.i
 * Swig module description file for wrapping a C++ class.
 * Generate by running "swig -c++ -python number.i".
 * The C++ module is generated in file number_wrap.cxx; 
 * module 'number' refers to the number.py shadow class.
 ********************************************************/

%module number

%{
#include "number.h"
%}

%include number.h
```
编译与使用过程与C Extending Module一直。

注意，SWIG是接口协议，无需关心具体实现。

# Ⅱ. Embedding Python in C/C++
TODO