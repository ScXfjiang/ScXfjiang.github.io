---
layout:     post
title:      不同编程语言对String的抽象
toc:        true
categories: [C/C++]
---
## 一、C & \<string.h>
### 1. String Model
* 在C语言中，字符串的类型是**字符数组**，以空字符'\0'结尾。
* **字符串常量**存储在只读内存区，对其的修改行为是未定义的。

一个常见的操作，使用字符串常量初始化字符数组：
```c
char str[] = "Hello World\n";
```
与
```c
char str[13] = {'H', 'e', 'l', 'l', 'o', ' ', 'W', 'o', 'r', 'l', 'd', '\n', '\0'};
```
完全相同。

### 2. \<string.h>
C语言中与字符串处理相关的库函数有两类，一类以str开头，另一类以mem开头。
```c
char* strcpy(char* dest, const char* src);
char* strncpy(char* dest, const char* src, size_t count);

char* strcat(char* dest, const char* src);
char* strncat(char* dest, const char* src, size_t count);

int strcmp(const char* lhs, const char* rhs);
int strncmp(const char* lhs, const char* rhs, size_t count);

char* strchr(const char *str, int ch);
char* strrchr(const char* str, int ch);

size_t strspn(const char* dest, const char* src);
size_t strcspn(const char* dest, const char* src);

char* strpbrk(const char* dest, const char* breakset);
char* strstr(const char* str, const char* substr);

size_t strlen(const char* str);
```

以mem开头的库函数主要提供一种更高效的字符串处理方式，这些库函数也不仅仅用于字符串处理：
```c
void* memcpy(void* dest, const void* src, size_t count);
void* memmove(void* dest, const void* src, size_t count);

int memcmp(const void* lhs, const void* rhs, size count);

void* memchr(const void* prt, int ch, size_t count);

void* memset(void* dest, int ch, size_t count);
```

## 二、C++ & \<string>
TODO


## 三、Python
TODO
