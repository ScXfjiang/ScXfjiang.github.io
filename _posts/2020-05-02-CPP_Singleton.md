---
layout:     post
title:      "[C++设计模式] Singleton"
toc:        true
categories: [C/C++, Design Pattern]
---
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
