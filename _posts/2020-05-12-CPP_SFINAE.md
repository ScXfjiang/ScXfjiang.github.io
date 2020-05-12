---
layout:     post
title:      "[C++] SFINAE"
toc:        true
categories: [C/C++]
---
**SFINAE (Substitution Failure Is Not An Error)**规则适用于Function/Class Template的实例化，在完成Template Argument Deduction后的Template Argument Substitution阶段起作用，意思是在Template Argument Sbustitution阶段由于模板参数替换导致的一些无意义的构造不会在本阶段报错，我们可以使用该特性"SFINAE out"掉一部分不希望存在的模板实例。

1. SFINAE一般配合模板偏特化使用 (这里是为了逻辑上的一致性，分别定义多个模板也可以，因为偏特化本身就是重新定义新的模板)；
2. SFINAE一般用于Function Template，但是也可以用于Class Template；

### SFINAE for function template
下面的Function Template Func仅有偏特化实现，只在T为整型时才有定义，其他类型会报未定义错误。
```c++
template<typename T, typename U>
void func();

template<typename T>
void func<T, typename std::enable_if<std::is_integral<T>::value>::type>() {
  // TODO
}
```

std::enable_if是type trait

std::is_integral是value trait

这里的SFINAE发生在std::enable_if中，如果第一个模板参数为true，则U推导为void，否则该trait的type无定义。

也可以使用带有缺省模板参数的模板：
```c++
template<typename T, typename U = typename std::enable_if<std::is_integral<T>::value>::type>
Func() {
  // TODO
}
```

### SFINAE for class template
下面的Class Template Foo仅有偏特化实现，只在T为整型和浮点型时才有定义，其他类型会报未定义错误。
```c++
template<typename T, typename U>
class Foo;

template<typename T>
class Foo<T, typename std::enable_if<std::is_integral<T>::value>::type> {
  // TODO
};

template<typename T>
class Foo<T, typename std::enable_if<std::is_floating_point<T>::value>::type> {
  // TODO
};
```

也可以使用多个带有缺省模板参数的模板：
```c++
template<typename T, typename U = typename std::enable_if<std::is_integral<T>::value>::type> {
  // TODO
}

template<typename T, typename U = typename std::enable_if<std::is_integral<T>::value>::type> {
  // TODO
}
```

