---
layout:     post
title:      "[C++设计模式] Auto Registering Factory"
toc:        true
categories: [C/C++, Design Pattern]
---
## 一个简单的例子
在本例中不引入泛型设计，工厂只生产Kernel这一种类型的对象。
```c++
#include <unordered_map>
#include <functional>
#include <string>
#include <iostream>
#include <cassert>

class Kernel final {
 public:
  Kernel(const std::string& name) : name_(name) {}

 private:
  std::string name_;
};

class KernelFactory final {
 public:
  struct Registration final {
    Registration(const std::string& name) {
      std::cout << "Registering Kernel: " << name << std::endl;
      assert(Get()->find(name) == Get()->end());
      assert(Get()->emplace(name, [name]() { return new Kernel(name); }).second);
    }
  };

  static Kernel* New(const std::string& name) {
    std::cout << "Creating Kernel: " << name << std::endl;
    assert(Get()->find(name) != Get()->end());
    return Get()->at(name)();
  }

  static std::unordered_map<std::string, std::function<Kernel*()>>* Get() {
    static std::unordered_map<std::string, std::function<Kernel*()>> creators;
    return &creators;
  }
};

#define REGISTER_KERNEL(name) \
  static KernelFactory::Registration registration_##name(#name)

REGISTER_KERNEL(fc);
REGISTER_KERNEL(conv);
REGISTER_KERNEL(relu);

int main() {
  KernelFactory::New("fc");
  KernelFactory::New("conv");
  KernelFactory::New("relu");

  return 0;
}

// output:
// Registering Kernel: fc
// Registering Kernel: conv
// Registering Kernel: relu
// Creating Kernel: fc
// Creating Kernel: conv
// Creating Kernel: relu
```

### 设计要点
* 将对象构造方法封装在static变量creators中；
* 使用辅助nested class Registration，通过构造无用的static Registration临时对象将构造方法添加进creators中(注册)；
* static变量的初始化发生在main函数前，所以creotors的初始化和构造方法的注册均发生在main函数执行前；

## 泛型设计 Level 01
需求：
1. 支持不同类型的Kernel；
2. 构造Kernel时允许传入任意参数；

