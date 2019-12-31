---
layout: post
title: 'Paper Note:Watching for Software Inefficiencies withWitch'
date: 2019-12-30
author: sf
color: rgb(255,210,32)
cover: 'http://on2171g4d.bkt.clouddn.com/jekyll-banner.png'
tags: Paper-note
---

# Abstact #

现有工具效率不高，我们提出了 

# Introduction #
即使是高性能计算机 也只利用了 5-15%的性能呢个，

inefficiencies :
+ 结算了无用的几果
+ 不必要的数据移动
+ 过度同步  
论文引用
+ inefficiencies 在内存子系统中尤为恶劣，因为带宽是优先的，而且是共享使用的 

重复初始化， 寄存器溢出， 阻塞hotpaths . 缺少inline hot fcutions ，因为別称导致的优化缺失，计算或者计算或者存储 已经得到或者几乎不变的量， 多核中的 contention 和假共享， 都是京单的错误用法。
尽管编译器对付这种低效很有用， 但是实践中， 由于 抽象层， 动态哭，多语言，符合类型，别名 ，指数爆炸等，都会导致编 无用的内存操作 

粗力度的剖析器包括： ... ... 

积极的一面是：这些润形式开销很小，并且不会打扰程序本身的运行
糟糕的一面是 hotspots 不能区分efficient inefficient resource usage， 举例来说spec cpu2006 gcc 中的一个例子
    基本块很多都很小，但是仍然要全部清0,这就产生了 dead store
现有的工具都不能检测出来，， hotspot 也许已经没有优化的共建了（高度优化的数学库），相反的，一个进行了大量数学计算的地方更有可能进行无效计算
细粒度的profilers ......  使用具体的目标分析了动态的指令，检测了无效计算或者数据的移动，他们可以检测出低效 not detected by coarse-grained profilers. 可以只处源代码中哪里已经进行了重初始化（Listing 1), 好处是指出来了，坏处是极大增加了开销

## contributions ##
+ 开发了轻量级的框架 witch 
+ 开发了一套抽样模式克服硬件的限制，（而且跑得就像预期那样号 exciting !)
+ 基于witch 开发了低效检测工具 我们的工具只需要非常小的内存开销
+ 克服了实践的挑战，证明了准确度是 sota 的
+ 应用我们的开发高出了结果 有10x speedup

# Related Work and Motivation #
检测低效的方法很多 我们将其分类为
硬件方法，软件方法
硬件方法：... ... 需要新的硬件支持
软件方法：我们的主要考虑
经典的编译器优化 ： value numbering， constant propoagations , commmon subexpression elimination 
最近 static analysis 被使用来检测性能的Bug, static analysis 就会收到别名限制，等等约束
动态分析九可以解决这些界限， 一遍工作表明， deadwrite 是低效的主要原因，  他们使用的工具deadspy 跟踪了所有的内存操作 ... ... 

... ... 

witch 