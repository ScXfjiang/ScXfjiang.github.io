---
layout:     post
title:      gRPC Notes
toc:        true
categories: [C/C++, System Programming]
---
## Stub (桩代码)
A **Method Stub** or simply **Stub** is **a method interface with mock implementation**,
which you can think of as a **placeholder** of the whole program.

Stub这个概念被发明出来的初衷是保证在一些接口没有实现完整的情况下仍然可以运行或测试系统。 

理解gRPC中Stub更好的方法是将其理解为Proxy模式中的Agent，gRPC使用protocol buffer定义并生成不同语言通用的Agent，然后由gRPC的用户使用不同语言实现Client，以此实现Proxy模式中Agent和Client的解耦。

## Interface Definition Language (IDL)
**Interface Definition Language (IDL)**通常用于**RPC框架**上下文中。RPC框架采用C/S模式，在Client和Server端可能采用不同的编程语言，IDL为Client/Server端统一了接口定义，RPC框架的使用者可以根据这个统一的接口定义在Client和Server端使用不同的语言进行开发。

gRPC使用protocol buffer作为IDL定义了Client和Server端的接口，用户在此基础上在Client和Server端可使用不同语言进行开发，如下图所示：
 
![gRPC Big Picture](/assets/posts/gRPC/grpc_big_picture.png)