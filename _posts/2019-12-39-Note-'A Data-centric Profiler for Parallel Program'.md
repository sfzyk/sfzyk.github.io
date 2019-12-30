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

    有两种数据局部性 spatial and temporal , spatial  是指访问一个地方后访问相邻的地方， temporal 是指访问同一个地方多次。
    multi-socket 系统具有额外的内存结构，这对性能有影响(?)
    该系统中的处理器具有高带宽的连接 比如 AMD 中的 HyperTransport 或者intel 中的 QuickPath 
    为了提供这样的高带宽， 内存被分区 并且其中的一部分直接被附加到每个cpu上， 每个处理器有他自己的 memory contraller 去访问直接附加的内存， 这样的机构具有 NUMA latency , 访问本地的内存就被叫做 local access， 访问其他处理器的内存就叫做 remote access 
    识别数据局部性并经 使用 simulation 或者 measurement,
    simulation 
比如CPROF, MEMSpy, ThreadSpotter SLO,MACPO, 通过cache simulator 计算一些或者全部的内存访问。主要有两点不足， 1 收集全部的信息开销过大 尽管采样技术能够降低开销， 但是也会降低精确度， 2 精确度严重取决于使用的cache simulator（缓存模拟器）。对于cache simulator 来说模拟内存的全部行为是非常贵的，都做了或多或少的简化。
    measurement
不像simulator measurement-based 工具使用hpc,measurement-based tools 可以被分成 数据中心的和代码中心的。在比较好的情况下，代码中心的工具哪呢钢构毕图 VTune Oprofile CodeAnalyst gprof 可以将性能指标映射到 statements accessing data (??),这种可以发现有问题的代码区 但是不能发现有问题的变量， 代码中新的工具则相反 可以发现有问题的变量和动态的内存区域，两者混合起来会刚好
    数据心的工具有些聚焦于sequential codes, 其他的聚焦于threaded codes  中的 NUMA problems
没有一种支持 comprehensive analysis of all kinds of data locality problem.进一步的 现存的工具只支持 modest number of cores on a single node system. 没有一个挑战集群中的可伸缩的问题，    显而易见，代码中新的 measurement tools 必须有能力面对非平反的集群情况。
    为了应对这种挑战，我们扩展了hpctoolkit 性能工具 with data centric capabilities to measure 和 分析程序在并行系统上的执行， 我们的工具有三种图特的能力 对于 data centric measurement and analysize 
    
