---
layout: post
title: 'Paper Note:A Data-centric Profiler for Parallel Program'
date: 2019-12-30
author: sf
color: rgb(255,210,32)
cover: 'http://on2171g4d.bkt.clouddn.com/jekyll-banner.png'
tags: Paper-note
---
# Abstract #
    识别出可以加强的数据局部行的地方很困难，我们通过扩展hpctoolkits完成了支持数据中心对数据的分析
    降低运行时间开销和空间开销
    在5种benchmark上展示了新的数据中心的分析
    展示了由于不好的局部性导致的性能瓶颈和 通过按照本论文发现的 性能优化（non-trivial）

# INTRODCUTION #

    有两种数据局部性 *spatial and temporal* , spatial  是指访问一个地方后访问相邻的地方， temporal 是指访问同一个地方多次。
    multi-socket 系统具有额外的内存结构，这对性能有影响(?)
    该系统中的处理器具有高贷款的链接 比如 AMD 中的 HyperTransport 或者intel 中的 QuickPath 
    为了提供这样的搞到款， 内存备份去 并且其中的一部分直接被附加到每个cpu上， 每个处理器有他自己的 memory contraller 去访问直接附加的内存， 这样的机构具有 NUMA latency , 访问本地的内存就被叫做 local access 