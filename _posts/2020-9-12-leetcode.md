---
layout: post
title: 'Note:leetcode'
date: 2020-9-12
author: sf
color: rgb(255,210,32)
cover: 'http://on2171g4d.bkt.clouddn.com/jekyll-banner.png'
tags: leetcode
---

## 1577 数的平方等于两数乘积的方法数 ##

给你两个整数数组 nums1 和 nums2 ，请你返回根据以下规则形成的三元组的数目（类型 1 和类型 2 ）：
类型 1：三元组 (i, j, k) ，如果 nums1[i]2 == nums2[j] * nums2[k] 其中 0 <= i < nums1.length 且 0 <= j < k < nums2.length  
类型 2：三元组 (i, j, k) ，如果 nums2[i]2 == nums1[j] * nums1[k] 其中 0 <= i < nums2.length 且 0 <= j < k < nums1.length  
1 <= nums1.length, nums2.length <= 1000  
1 <= nums1[i], nums2[i] <= 10^5  
https://leetcode-cn.com/problems/number-of-ways-where-square-of-number-is-equal-to-product-of-two-numbers  
（1）
map算法sum2的变种，我使用的是collections.Counter避免重复计算  n*n*log(n)
（2）
还可以双指针，两侧指针向中间靠拢时必定会得到那个值。 可以假设目标值如果存在一定在指针内侧。 这样优化可以有  n*n 的算法


## 1578. 避免重复字母的最小删除成本 ##
给你一个字符串 s 和一个整数数组 cost ，其中 cost[i] 是从 s 中删除字符 i 的代价。

返回使字符串任意相邻两个字母不相同的最小删除成本。

请注意，删除一个字符后，删除其他字符的成本不会改变。
示例 1：
输入：s = "abaac", cost = [1,2,3,4,5]
输出：3
解释：删除字母 "a" 的成本为 3，然后得到 "abac"（字符串中相邻两个字母不相同）。

示例 2：

输入：s = "abc", cost = [1,2,3]
输出：0
解释：无需删除任何字母，因为字符串中不存在相邻两个字母相同的情况。

示例 3：

输入：s = "aabaa", cost = [1,2,3,4,1]
输出：2
解释：删除第一个和最后一个字母，得到字符串 ("aba") 。


提示：

    s.length == cost.length
    1 <= s.length, cost.length <= 10^5
    1 <= cost[i] <= 10^4
    s 中只含有小写英文字母
 https://leetcode-cn.com/problems/minimum-deletion-cost-to-avoid-repeating-letters
 
 
（1）
一次扫描相连的同字母取一个代价最大的留下即可 On 

## 1579. 保证图可完全遍历 ##
Alice 和 Bob 共有一个无向图，其中包含 n 个节点和 3  种类型的边：

    类型 1：只能由 Alice 遍历。
    类型 2：只能由 Bob 遍历。
    类型 3：Alice 和 Bob 都可以遍历。

给你一个数组 edges ，其中 edges[i] = [typei, ui, vi] 表示节点 ui 和 vi 之间存在类型为 typei 的双向边。请你在保证图仍能够被 Alice和 Bob 完全遍历的前提下，找出可以删除的最大边数。如果从任何节点开始，Alice 和 Bob 都可以到达所有其他节点，则认为图是可以完全遍历的。

返回可以删除的最大边数，如果 Alice 和 Bob 无法完全遍历图，则返回 -1 。
链接：https://leetcode-cn.com/problems/remove-max-number-of-edges-to-keep-graph-fully-traversable

并查集贪心算法，先插入类型3的边 然后是类型12，的多余的边就是可以排除的 On 并查集减枝可以看作on



 