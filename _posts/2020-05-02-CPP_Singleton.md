---
layout:     post
title:      "[C++设计模式] Singleton"
toc:        true
categories: [C/C++, Design Pattern]
---
C++中实现单例模式比较简单，就是定义一个static变量并提供一个访问接口。

## 一个简单的例子
```c++
class Foo;

Foo* GlobalFoo() {
  static Foo* global_foo = new Foo;
  return global_foo;
}
```
* global_foo为static变量，在main()函数运行前已被初始化；
* global_foo定义在GlobalFoo()中的目的是缩小global_foo的作用域；

## 泛型设计
```c++
template<typename T>
class Global final {
 public:
  template<typename... Args>
  static void New(Args&&... args) {
    assert(*GetPPtr() == nullptr);
    *GetPPtr() = new T(std::forward<Args>(args)...);
  }
  static T* Get() {
    assert(*GetPPtr() != nullptr);
    return *GetPPtr();
  }
  static void Delete() {
    assert(*GetPPtr() != nullptr);
    delete *GetPPtr();
    *GetPPtr() = nullptr;
  }

 private:
  static T** GetPPtr() {
    static T* ptr = nullptr;
    return &ptr;
  }
};
```
