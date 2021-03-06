+++
title = "CS144 Lab0 学习笔记"
date = 2020-08-23T13:27:15+08:00
+++

CS144 是一门关于计算机网络的斯坦福大学的公开课。课程的实验是动手写一个用户态的 TCP 协议，非常吸引我，于是就入坑了。我会用一个系列来记录自己的学习笔记。

- 课程链接: https://cs144.github.io/

- 本文讲义: https://cs144.github.io/assignments/lab0.pdf

- 本文相关代码: https://github.com/hexyoungs/tcplab/tree/lab0

<!-- more -->

## 准备工作

课程的讲义虽然是公开的，但作业内容的模板代码是私有的，可能是为了防止学生作弊或者代码泄露，因此我们理论上是没有权限接触到课程代码的，但我在 github 上搜索到了另一位同学的仓库，里面的代码是可以配套使用的，在修复了一个小编译错误之后，我把代码上传到了 github，同学们可以从开头的链接处获取到，**仅供学习使用**。

## 讲义

讲义内容可以大致分为实验和任务两个部分，作为开篇，Lab0 的实验和任务是热身向的。

在实验部分，我们利用 telnet、netcat 等工具，进行 HTTP 报文的发送，进行邮件的发送，以及体验 TCP 的监听和接收功能。实验部分是想让我们了解 TCP 协议的可靠性的特点（保证字节流在不可靠的网络中的可靠传输）。

紧接着便是任务部分，第一个任务是利用 sponge 这个课程提供的库，编写一个发送 HTTP GET 请求，然后阅读返回的 TCP 字节流的小程序，叫做 `webget`。实现比较容易，复杂的部分被 sponge 库做了，我们只需要浏览相关的头文件，了解一些基础接口，便可以在 10 行左右实现功能。

第二个任务是让我们实现一个单线城的，in-memory 的可靠字节流，说人话就是实现一个 Stream 的抽象，可以读、可以写，并且满足保证读写的顺序一致。由于是单线程，并且不考虑并发读写，我们只需要利用 cpp 标准库里面的容器(这里用了 dequeue )保存数据，然后适配所有的接口并且通过测试用例即可。

## 要点

Lab0 的要点其实就一点，TCP 是可靠的字节流。

另外，我在做实现时，学习到了下面两点碎片知识。

第一是，string 和 dequeue<uint8_t> 的交互，可以直接使用 string 的构造函数，将 uint8_t 数组转换为 string。

```cpp
std::string s(data, data + len);
```

反过来，想要将 string 放到容器里面，可以直接使用 `std::copy` 配合容器的 `std::back_inserter`

```cpp
std::copy(s.begin(), s.end(), std::back_inserter(data));
```


第二是，POSIX API `getaddrinfo` 实际上是可以传入 `service` 字符串来获取标准端口的。这是我第一次这样使用。

## 结语

整个 Lab0 难度不高，内容也不多，但可以调动我们的积极性，开始进入学习状态，期待下一个 Lab。

另外，完整实现在 repo 的 lab0-ans 分支供同学们参考。

