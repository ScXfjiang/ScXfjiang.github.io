---
layout:     post
title:      gRPC Notes
toc:        true
categories: [C/C++, System Programming]
---
## 一、先验知识
### 1. Interface Definition Language (IDL)
**Interface Definition Language (IDL)**通常用于**RPC框架**上下文中。RPC框架采用C/S模式，在Client和Server端可能采用不同的编程语言，IDL为Client/Server端统一了接口定义，RPC框架的使用者可以根据这个统一的接口定义在Client和Server端使用不同的语言进行开发。

gRPC使用protocol buffer作为IDL定义了Client和Server端的接口，用户在此基础上在Client和Server端可使用不同语言进行开发，如下图所示：
 
![gRPC Big Picture](/assets/posts/gRPC/grpc_big_picture.png)

### 2. Stub (桩代码)
A **Method Stub** or simply **Stub** is **a method interface with mock implementation**,
which you can think of as a **placeholder** of the whole program.

Stub这个概念被发明出来的初衷是保证在一些接口没有实现完整的情况下仍然可以运行或测试系统。 

理解gRPC中Stub更好的方法是将其理解为Proxy模式中的Agent，gRPC使用protocol buffer定义并生成不同语言通用的Agent，然后由gRPC的用户使用不同语言实现Client，以此实现Proxy模式中Agent和Client的解耦。

## 二、gRPC
### 1. gRPC提供的四种服务类型
#### 1.1 Unray RPCs
**Unary RPCs** where the client sends a single request to the server and gets a single response back, just like a normal function call.
```protobuf
rpc SayHello(HelloRequest) returns (HelloResponse);
```
#### 1.2 Server Streaming RPCs
**Server streaming RPCs** where the client sends a request to the server and gets a stream to read a sequence of messages back. The client reads from the returned stream until there are no more messages. gRPC guarantees message ordering within an individual RPC call.
```protobuf
rpc LotsOfReplies(HelloRequest) returns (stream HelloResponse);
```
#### 1.3 Client Streaming RPCs
**Client streaming RPCs** where the client writes a sequence of messages and sends them to the server, again using a provided stream. Once the client has finished writing the messages, it waits for the server to read them and return its response. Again gRPC guarantees message ordering within an individual RPC call.
```protobuf
rpc LotsOfGreetings(stream HelloRequest) returns (HelloResponse);
```
#### 1.4 Bidirectional Streaming RPCs
**Bidirectional streaming RPCs** where both sides send a sequence of messages using a **read-write stream**. The two streams operate independently, so clients and servers can read and write in whatever order they like: for example, the server could wait to receive all the client messages before writing its responses, or it could alternately read a message then write a message, or some other combination of reads and writes. The order of messages in each stream is preserved.
```protobuf
rpc BidiHello(stream HelloRequest) returns (stream HelloResponse);
```