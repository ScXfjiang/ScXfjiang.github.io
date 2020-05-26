---
layout:     post
title:      Hash Table
toc:        true
categories: [Algorithm]
---
## 1. Hash Table Basics
### 1.1 Motivation
**Associated Container** (aka, **Map**, **Dictionary**) is an **Abstract Data Type (ADT)** composed of a collection of **key-value pairs**, such that each possible key appears at most once in the collection. Operations associated with such ADT allow:
* the addition of a pair to the collection
* the removal of a pair from the collection
* the modification of an existing pair
* the lookup of a value associated with a particular key

Such ADT can be implemented by **Hash Table** or **Tree** data structure.

### 1.2 Big Picture and Concepts
#### Big Picture
1. Calculate the slot location in hash table by key
2. Resolve collision by chaining or open addressing

![Hash Table Big Picture](/assets/posts/Hash_Table/Big_Picture.jpg)

注：为什么是key-value pair而不是value？因为要处理collision。

#### Concepts
* **Hash Function**: calculate slot location of hash table by key
* **Collision**: more than one keys maps to the same slot location in the hash table
* **Chaining**: a method to resolve collision
* **Open Addressing**: a method to resolve collision

## 2. Hash Function
### 2.1 What is a good hash function?
A good hash function satisfies the assumption of **simple uniform hashing**:
each key is equally likely to hash to any of the $$m$$ slots, independently
of where any other key has hashed to.

### 2.2 Common Hash Functions
* Divide Method
* Multiplication Method
* Universal Hashing

## 3. Resolve Collision
### 3.1 Chaining
TODO

### 3.2 Open Addressing
TODO

## 4. std::hash and std::unordered_map

### 2.3.1 std::hash
[cppreference - std::hash](https://en.cppreference.com/w/cpp/utility/hash)

```c++
template<typename Key>
struct hash;
```

std::hash是一个模板类，同时也是一个Functor，提供了operator()方法，返回size_t类型的slot location。

STl中对不同类型的Key做了特化处理：
![std::hash](/assets/posts/Hash_Table/std_hash.png)

### 2.3.2 user defined hash function
C++中自定义hash function一般是自己写std::hash的特化，主要就是重写operator()的逻辑。
TODO

### 2.3.3 std::unordered_map
TODO