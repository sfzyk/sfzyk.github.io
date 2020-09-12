---
layout: post
title: 'Note:lwn artcle'
date: 2020-9-11
author: sf
color: rgb(255,210,32)
cover: 'http://on2171g4d.bkt.clouddn.com/jekyll-banner.png'
tags: kernel controling groups
---
## 0

## 1 history ##
fourth berkeley 第一次有了set的概念
一个kill 调用 -1 可以对相同uid的进程发起调用，如有有权限情况下就是所有进程。

另外一个berkeley版本提出的概念是 job control 

Issues 
1 gourp 的命名， v6 unix 使用的名字就是关联的资源tty 
2 复用， 以前对于进程信号的发送和 到 /dev/tty是同一套机制，很快就分开了 因为相似不是相似的呢个
3 一个进程能否脱离其所属的组， 以前不能现在可以，
4 继承关系在这里扮演什么角色


## 2 history  aouble hierarchy ##

简要记录了一下 Hierarchies in computer account privileges
一些优先级和账户的拥有者相关 ， 另一些则和所属的机构相关
最后形成了两套hierarchy 一套是 reason , the reason account was create, 一套是 organissatio ，记录这个角色在学校的哪一个部分。 
交叉相乘就得到了权限系统。for each role and each affiliation there was a group of user accoutns. 
每一个账户都可能存在于多个不同的组中，
 
***So the first lesson from this tale is that a little complexity can be well worth the cost, particularly if it is well-chosen and can be supported by simple tools.***  

### two types of hierarchy ###
two hierarchies had quite different characters.

Reason might be called classification hierarchy . 
Organizations.

## devices in /sys about hierarchy ## 

three-level hierarchy is exactly what we find under /sys/dev,
legacy hierarchy and major id minor id 


/sys/class hierarchy is quei 
