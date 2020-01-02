---
layout: post
title: 'Paper Note:Libmonitor: A Tool for First-Party Monitoring'
date: 2019-12-30
author: sf
color: rgb(255,210,32)
cover: 'http://on2171g4d.bkt.clouddn.com/jekyll-banner.png'
tags: Paper-note 闲的没事
---
# 小结 #
libmonitor 的工作是通过重写main exit 和 pthread_create 完成的。
简单的梳理了一下代码，了解了一下原理
重写 main 函数是行不通的 在linux 上是可行的 _start 调用 _lib_start_c 调用 main ok 
但是在freeBSD 上行不通 _start 直接调用 main( 原因我还不太， 大概是静态引用？)

总之就是调用了lib_start_main 然后执行所有自己的代码， 在fork exit 也是先执行自己的 再执行原有的。
关键处是 ldysm(LD_NEXT )使得程序有能力找到 下一个模块中的代码
