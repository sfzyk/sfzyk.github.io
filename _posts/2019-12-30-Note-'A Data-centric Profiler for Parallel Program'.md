---
layout: post
title: 'Paper Note:A Data-centric Profiler for Parallel Program'
date: 2019-12-30
author: sf
color: rgb(255,210,32)
cover: 'http://on2171g4d.bkt.clouddn.com/jekyll-banner.png'
tags: Paper-note performance locality
---
    针对数据局部性做的hpctoolkit扩展，在几个地方做了优化提升了效率
    
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
### simulation ###
比如CPROF, MEMSpy, ThreadSpotter SLO,MACPO, 通过cache simulator 计算一些或者全部的内存访问。主要有两点不足， 1 收集全部的信息开销过大 尽管采样技术能够降低开销， 但是也会降低精确度， 2 精确度严重取决于使用的cache simulator（缓存模拟器）。对于cache simulator 来说模拟内存的全部行为是非常贵的，都做了或多或少的简化。
### measurement ###
不像simulator measurement-based 工具使用hpc,measurement-based tools 可以被分成 数据中心的和代码中心的。在比较好的情况下，代码中心的工具哪呢钢构毕图 VTune Oprofile CodeAnalyst gprof 可以将性能指标映射到 statements accessing data (??),这种可以发现有问题的代码区 但是不能发现有问题的变量， 代码中新的工具则相反 可以发现有问题的变量和动态的内存区域，两者混合起来会刚好
### 现状分析 ###
数据中心的工具有些聚焦于sequential codes, 其他的聚焦于threaded codes  中的 NUMA problems
没有一种支持 comprehensive analysis of all kinds of data locality problem.进一步的 现存的工具只支持 modest number of cores on a single node system. 没有一个挑战集群中的可伸缩的问题，显而易见，代码中新的 measurement tools 必须有能力面对非平反的集群情况。
为了应对这种挑战，我们扩展了hpctoolkit 性能工具 with data centric capabilities to measure 和 分析程序在并行系统上的执行， 我们的工具有三种图特的能力 对于 data centric measurement and analysize 
+ 识别所有的数据局部性问题
hpctoolkit 只能分析和测量threaded programs 中的，对tempporal,spatial 和NUMA 中的数据局部性问题没办法。
+ 可以应对大规模的混合程序同时使用了MPI 和 OpenMPI.
+ 对优化结果提供了具有启发性的分析

# MOTIVATION #
动机主要有两点
+ importance of data-centric profiling for analyzing data locality problems
+ scalability has become an important concernfor data-centric profilers

### 数据中心的 profiling ###

code-centric 同 data-centric 的不同之处，有两点
+ code-centric 可以之处原代码中较高延迟的一行 而不用从指令去映射， 但是无法区别同一行中两个变量的延迟 对应的 data-centric 的profiling 可以分辨这种情况。

+ data-centric 中可以将所有的针对同一变量的内存访问联系到一个变量上，帮助找到那些模式导致了性能不佳同时帮助操作数据


### scalability is important ###

+ hpc 社区关注
+ 内存访问暗示限制性能和能耗的重要因素
+ 在大规模数据集上是 desiable ，比强迫用户用构造小数据集有用的多

data-centric  profiler 应该是较少的额外开销（时间和空间），运行时的overhead 只来自于两部分：
+ 收集性能信息 
+ 追踪变量分配
收集性能信息可以通过合理的采样来减少时间开销
追踪变量分配同样非常困难，大部分研究假定变量不会频繁的分配，然而这种假定靠不住

空间开销同样不可小觑，内存的访问数据是同时间和规模成比例的，但是变量的分配是会增长到不合理的大的程度的。
除了开销之外，已经存在的工具还不会展示数据的可伸缩性。 比如Fig2 的例子， 度量会被分散到100次种并不呈现一个热点，如果一个循环出现在所有的MPI 进程中，那就他就应该被展示出来因为可能是一个 **problematic array**
为了解决上面的问题，我们提出了hpctoolkit 的扩展

# data-centric profiling 硬件支持 #
为了解决精确的测量out-of-order 的处理器， Dean 发明了 instruction-based sampling (IBS),perfromce monitoing unit 周期性的选择instrution 采样来检测。当采样得到的数据金土pipeline时，PMU 记录下其和关键时间的occurrence,latencies 和内存的寻址效率。当带样指令完成后。pmu触发一次中断，告诉检测器指令是可用的， 第一个使用IBS 的工具是DEC's Dynamic Continuous Proiling Infrastructure DCPI
最近的AMD 就使用了IBS 
IBM POWER5 和后来的处理器使用了IBS 相似的策略。 当一个标志事件记录的时候，POWER 处理器 设置两个特殊的寄存器， 一个SIAR 记录指令的地址，SDAR 记录effective address touched by a sampled instruction ,如果这个指令是内存相关的。PMU可以设置当中断到来的时候九发出中断，这样hpctoolkit 就可是使用这两个寄存器来检测标志事件code or data .
也有一个处理器具有data-centric measurement 能力 
intel Nehalm 处理器和之后的 Itanium 处理器也支持
这些data-centric 的共同点有
+ 都是针对内存的抽样
+ 以性能事件或者延时的方式表示一次访存带来的cost
+ 支持访问被抽样指令的IP和 effective address
使用 时间或者latency 来表示访存cost ，使用IP 来链接cost 和 memory accesses.effective address 联系costs 和 变量



# 对于hpctoolkit的 data-centric 能力
简化的流程图 analyzer profiler 
三个主要部件
+ online call path profiler 
+ a post-mortem analyzer 
+ GUI

profiler 接受优化过得二进制文件


### Online call path profiler ###
profiler 在运行时倍加在进入程序的地址空 （对于静态的可执行文件来说 
，当程序执行的时候， profiler 就会开始采样，捕捉采样事件的全部上下文信息，追踪变量，或者使用 LD_PRELOAD 动态加载可执行文件，

+ Trigger samples 
首先开启每个core's PMU 开始 instruction-based sampling，当 pmu 触发一个采样时间的时候 profiler  接受到信号，并且读取PMU 的寄存器解析对应的 performance metrics 

+ Capturing full calling contexts 
hpctoolkit 解析每一个call stack at each sample event .使用在线算法定位每一个帧的返回地址，

为了支持data-centric 分析需要进行两个改变 ： 
首先我们要调整 cc的叶子节点， 避免 采样的指令 和 样本事件的偏移
其次我们创建多重cct for each thread ,对于不同的sample feature 插入到不同的树中

+ Tracking varibles 
static data. （ .bss 区中的数据 ， 每一个静态变量都有一个命名词条，指示了这个变量在内存中的位置。
这种变量的加卸载和模块的加载卸载一致
Heap-allocated data 这种数据是在堆上动态分配的，由malloc 类函数执行，

+ Unknown variable 
C++ template 使用low level system call brk 分配内存，这个调用并不返回分配的范围，而是直接设置dtaasegment , 处于这种考虑， hpctoolkit 不追踪C++ template container allocations，栈变量同样被视为 unknow Variable，栈变量很少成为数据局部性的瓶颈。

静态变量的检测使用的开销非常小，但是记录堆变量的开销是非常大的，我们有三种策略减小这种开支：
+ 我们不追踪所有的内存地址， 一般而言较大的内存数组更有可能局部性问题，我们简单的设置一个门开，小于该值的变量将不被记录 4K , 我们仍然会记录free 但是不记录cc ,所以开销并不大

+ 我们使用内联汇编直接读取指令的上下文信息，避免了堆栈的开销，我们的实现开销小于libc's  getcontext

+ 因为在较深的调用关系中记录调用栈信息是非常昂贵的，所以我们识别他们的最长公共前缀，提升效率。

第一种办法导致了不准确我们只在必要的时候使用。其余两种总是使用

### 关联所有的标准到变量 ###
HPCToolkit  per-forms  data-centric  attribution  on-the-fly。
首先创建三种不同的ccts ，记录不同类型的变量， static heap unknown 
对于每一次采样， searches map of heap allocated variables using effective address
"It is worth noting that the memory allocation call path may reside in a different thread than the one the PMU sample takes for an access" ??
从其他的线程拷贝cp 是不需要所得， 因为cp是不可改变的。
如果拷贝的cp已经在local thread cct 中了 ，九把他们联合起来

这个cct 拷贝合并 的操作能够处理同循环内多重分配的问题 ， 如果发生了那种事庆， 实际上会被线上解决当作一个变量， 对不同变量的访问被区分为 variable's allocated paths 

### analyze 和 合并GUI  ###
略

### case study ###
