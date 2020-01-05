---
layout: post
title: 'Note:A Primer on Memory Consistency and Cache Coherence'
date: 2019-12-30
author: sf
color: rgb(255,210,32)
cover: 'http://on2171g4d.bkt.clouddn.com/jekyll-banner.png'
tags: note memory
---
# intro #
在现代内存系统中， 许多个core 可以都可以读或者写一个 单一的共享地址 ， 这些设计具有很好的性质， 毕图高性能，低功耗。 当然还有正确性。

一个将内存系统正确性公认的分类是将其分为consistencyt 和 cohernence. , 并不是说计算机系统做了这种区分，而是这样区分方便一些，

不像 consistency , coherence 对软件不可见并且也不要求，然而能，作为一致性模型的支撑掉条，大部分共享内存的系统，都是实现了coherence 协议来 提供coherence ,

## consistency ## 

一致性模型定义了正确的内存行为 在loads 和加载中， 在没有对缓存的引用的情况下。
一个一致性模型定义了这种行为是正确（用户就必须采取其他行为来保证）的 还是错误（系统要处理这种情况）的
共享内存正确性 就被 内存一致性模型指定， 或者说就是一种内存模型。
consistency = shaed memory correctness 

# chapter 2 # 

## 基本的系统模型 ##
在这里，我们考虑的基本模型是多核cpu的同时具有共享内存，LLC 是被所有核共享的， 在这本书内 cache 指的是每个core 所独享的， 而不是LLC .
本书不讨论无关的细节，比如
+ 指令cahe | 多界别cache ,多核之间共享的cache ， 虚拟地址cache ,tlb, coherent direct memeory access . 

## 问题： incoherence 怎麽发生的 ## 
由于有多个角色访问了caches 和 memory 在现在系统中， 这些角色就是 cpu cores DMA engines 和能够读写cache 内存的外部设备，
## 定义 conherence ##
我们定义的基础是SWMR siingle-wirter-multiple-reader 
我们的定义 coherence 不变量 

+ SWMR single write multiple read
+ Data-Value invarisant 数据不能乱变

### sidebae 其他对于 conherence 的定义 ###
我们的定义指定了访问其他核的内存位置的时候的不变里那个， 和具有特殊变量的核间传递 
一种 consistency-like 的方式来执行 coherence 是通过定义sequential consistency .
SC 这种内存模型要系统执行所有进行的读写操作完全按照每个线程的顺序。
每一个load 都会的到最近一次的stroe 结果。

a coherent system must appear to execute all threads’ loads and stores to a single memory location in a total order that respects the program order of each thread.

This definition highlights an important distinction between coherence and 
consistency: coherence is specified on a per-memory location basis, whereas consistency is specified with respect to all memory locations

有一些其他的定义coherence 使用2个 不变量
+ 每一个 store 最终会对所有的核可见 
+ 对于同一个内存位的写操作是串行化的， 就是对于所有的核来说顺序是相同的。

另外的定义还有包含三个不变量的 
+  一个核对a 的读操作会读到 上一个该核对a的写操作 ， 除非有其他核在中间写了a 
+  一个核对a 的读操作会读到 其他核对a的写操作 如果该核 "“are sufficiently separated in time"

+ 对于同一个内存位的写操作是串行化的， 就是对于所有的核来说顺序是相同的。

### 维持这些不变量 ###
大部分的coherence protocals 是"invalidate protocals" ， 就是为了维护这些不变量的。 

+ 如果一个核想要读取内存位置，就先给其他核发信息，终结其他核的 read-wirte state 
+ 如果一个核要写内存位置，终结其他核的任何其他转台态，

chapters 6-9 会详细叙述 ， invalidate protovals 


### Coherence 的粒度 ###
在实践中coherence 使用cache block 的粒度进行维护，我们进行描述也是使用 block 这种等级的coherence 

### Scope of Coherence ###

+ 在所有获取共享地址空间内的数据都存在， L1 L2 LLC main memeory L1 instruciton cache TLB 
+ conherence 对架构来说是不可见的， 准确来说，一个系统也许是incoherent 并且同时是correct 如果满足某一种memory consistency model 的话。着看起来很费解，但是却蕴含着一个道理：
+ memory consistency model 并不显式的要求cohernce 或者要求实现那个协议。

# chapter 3 #
 这里描述内存一致性模型， 
 这个模型定义了什么是正确的，所以程序员知道预期的表显示什么， 开发者知道要提供什么， 

 ## 共享内存行为面临的问题 ##
 首先要定义 shared memeory behavior ，为什么要这么做呢。因为一个例子，这个例子中大部分人都回认为 r2 最终会拿到 NEW , 然而在今天的许多架构上 不是的。

### 一个核如何打乱内存访问问 ##
现代的核

+ Store-store 打乱。
如果核具有 Non-fifo wirte buffer。 比如第一个stroe没命中缓存，第二个却命中了， 或者第二个store 和前面的有所关联，尽管没有coherence 的问题 ，也许coherence 仍然让cache 是不可见的，但是stroe 已经被打乱了孙需

+ Load-Load 打乱
现代的核会打乱 load-load ，就像 store store 那样 

+ ls sl 打乱
也会有
## 什么是一个内存一致性模型 ##
 A memory consistency model, or, more simply, a memory model, is a specification of the allowed behavior of multithreaded programs executing with shared memory

## consistency vs conherence ##
看起来像是 
conherence - > consistency 
但其实不是的

+ conherence 是为了让多核系统中的cache invisiable ，然而，一旦是invisiable 的了，还有什么行为可言？
+ conherence 只在一块cache 上做文章， 多个cache block 的交互不管， 真实的程序有数不清的cache blocks 
+ 实现一个内存系统可以 不要conherence 甚至不要 cache 

 尽管不要求conherence 也能实现 consistency 但是大多数的 内存系统还是实现了conhernece 作为consistency的基础
## basic of sequenial consistency ##
最naive 的模型就是 SC . 第一次被证实提出 是 Lamport 
就是提出的是 consistency model, 具有一个 memory order，只要满足这个就可以认为是满足的
## 关于SC 的形式化定义 ##

+ 所有的核将自己的load store 按照程序顺序 插入 内存顺序
+ 所有的load 都从最近的 store 获得内容，当然是内存顺序

就是字面意义上所有的顺序都不能够交换
## naive SC implementations ##

+ 多任务 唯一核 
首先，把所有的线程执行在一个核上， T1 的指令执行在C 上 直到上下文切换到了T2上， 上下文切换的时候所有内存相关的工作都应该完成

+ 分配器
在所有的核 和内存之间搞一个分配器  假定所有的核都按照程序流程搞， 分配器就可以让所有的  核遵循SC
## 评价 ##
1 好处 简单形式化
2 坏处 不符合性能要求，因为都有bottleneck 
第一个是有一个single core 第二个是有single switch memory ，都没有并行， 但实际上是可以并行的
## A basic sc implementation with cache conherence ##




