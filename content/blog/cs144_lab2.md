+++
title = "CS144 Lab2 学习笔记"
date = 2020-09-04T17:58:15+08:00
+++

CS144 是一门关于计算机网络的斯坦福大学的公开课。课程的实验是动手写一个用户态的 TCP 协议，本文是第三篇。

- 课程链接: https://cs144.github.io/

- 本文讲义: https://cs144.github.io/assignments/lab2.pdf

- 本文相关代码: https://github.com/hexyoungs/tcplab/tree/lab2

## update 20200928

2020 年秋季的课程也开始了，网络上之前的 pdf 已经找不到了，故休息一段时间，等课程同步到相关进度后，再继续这个系列。

<!-- more -->

## 讲义

Lab2 的内容是实现 TCP 的接收端，讲义从接收端的指责说起，引出我们需要实现的两个重要点，然后给出样板代码和测试用例留作实验。

简单摘要一下，TCP 的接收端有两个职责：

1. 告诉发送端，目前已经成功重组了多少数据（acknowledgment）
2. 告诉发送端，什么范围的数据是目前允许接收的（flow control window）

为了实现这两个点，TCP 协议有一系列相关的字段和约定，我们要做的就是实现这些约定。

## 要点

### seqno/absolute seqno/stream index 的转换

要实现 ack（职责一），首先要理解下面的三个定义，讲义里面为我们做了辨析，见下表

|区别|TCP 协议中的序列号|绝对序列号|字节流的 index|
| --- | --- | --- | --- |
| 起始值 | 从 ISN 开始 | 从 0 开始 | 从 0 开始|
| SYN 和 FIN | 包含 SYN 和 FIN | 包含 SYN 和 FIN | 不包含|
| 值范围 | 32 位，溢出后变为 0 | 64位 | 64位 |
| 术语 | seqno | absolute seqno | stream index |

有了这些辨析，任务一就出现了，实现一个 WrappingInt32，负责 <u>TCP 协议中的序列号</u>、<u>绝对序列号</u>以及 Lab1 中需要的<u>字节流的 index</u> 这三者的互转

整体难度不大，只需要理清楚概念，实现并通过测试用例即可。

### flow control window

TCP 协议的封装和解包在实验中并不要求实现，课程库已经包含在内了，实验重点是实现关键数据的获取和数据包的接收逻辑。

接收端需要提供对发送端的反馈，因此需要实现 ackno 和 window size 的函数，这两个值即为关键数据。

flow control window 的定义和作用讲义也提供了，我这里摘要一下：

“它是一个 stream index / seqno 的区间，整个 window 的最左端，就是 ackno，window 的长度即为 size。window 的作用就是通知发送端: 接收端期望接收在这个范围内的数据。以此减少发送端做无用功的可能性。”

如图

![](https://i.imgur.com/gfqzVDI.png)

只要数据包和 window 存在重叠部分(图中的情况均应接收)，那么接收端就应该接收数据并且开始整合数据。

因此清楚了 window 的定义和作用之后，就可以实现接收端的核心逻辑了：

1. 负责初始化 ISN，在接受到第一个 SYN 的包时，它的 seqno 就是 ISN
2. 判断该数据包是否是再我们需要的接收窗口中
    - 如果是，那么调用 Lab1 实现的工具进行整合，并且返回 true
    - 否则直接丢弃

接收逻辑描述起来很简单，但细节较多，需要配合测试用例逐一通过。这个过程中可能发现 Lab1 的某些实现是不符合要求的，在这里也可以一并修改。

## 结语

这一节的细节比较多，需要小心仔细地实现，遇到自己的理解和测试用例发生偏差时，多考虑是自己的理解问题，重新设计自己的实现。

完整实现在 repo 的 lab2-ans 分支供同学们参考。

## 参考资料

- [rfc793](https://tools.ietf.org/html/rfc793)


