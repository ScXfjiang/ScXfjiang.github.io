---
layout:     post
title:      C++ Polymorphism
toc:        true
categories: [C/C++]
---
## 一、动多态（运行时多态）
C++的运行时多态是通过virtual table机制实现的。C++会为每个声明virtual function的class创建一个**vtbl (virtual table)**，在构建该class实例的时候会为该实例添加**vptr (virtual pointer)** 指向该class的vtbl。需要说明的是，C++运行时多态只能通过pointer和reference实现，原因是对pointer和reference进行类型转换不会修改该对象的vptr。

下面以一个例子对virtual table机制进行说明：

```c++
class Animal {
 public:
  virtual void Run();
};

class Bear final : public Animal {
 public:
  void Run() override;
};

int main() {
  Animal* pa = new Bear();
  pa->Run();  // Bear::Run()

  return 0;
}
```
pa指向Bear实例的起始地址，它会根据Bear实例的vptr找到class Bear的vtbl，然后调用Bear::Run()。

## 二、静多态
C++中的静多态使用**奇异递归模板(Curiously Recurring Template Pattern, CRTP)** 实现。奇异递归模板将子类作为模板参数传递给父类，以此种方式使得父类获得子类的类型信息。静多态同样需要依赖pointer或reference实现，原因与动多态相同。

下面是一个具体例子：
```c++
template <typename Derived>
struct Base {
  void interface() {
    static_cast<Derived*>(this)->implementation();
  }
};

struct Derived1 : Base<Derived1> {
  void implementation() {
    std::cout << "Derived1 implementation" << std::endl;
  }
};

struct Derived2 : Base<Derived2> {
  void implementation() {
    std::cout << "Derived2 implementation" << std::endl;
  }
};

int main() {
  Derived1 d1;
  d1.interface();

  Derived2 d2;
  d2.interface();

  return 0;
}
```
