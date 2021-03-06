---
layout: post
title: MySQL索引数据结构那些事
description: 敢于去打败曾经的梦魇，才算是真正的成长。
category: blog
tags:
  - mysql
  - 索引
categories:
  - MySQL
author: zhoubohan
date: 2018-06-10 00:00:00
---

# MySQL索引的数据结构

## 1.B-tree

    B-tree为了能加快访问速度，是一种多路自平衡搜索树，B-tree允许每个节点有更多子节点。

![mysql_index-1](/images/mysql_index/mysql_index-1.png)

<b>B-tree(m叉树)特点如下</b>
* 树的节点至多有m个孩子(m>=2)
* 除根节点和叶子节点外，其他每个节点至少有ceil(m/2)个孩子
* 若根节点不是叶子节点，则至少有俩孩子（特殊情况：没有孩子的根节点，即根节点为叶子节点，整棵树只有一个叶子节点）
* 所有叶子节点出现在同一层，且叶子节点不包含任何关键字信息
* 所有键值分布在整个树中
* 任何一个关键字出现且只出现在一个节点中
* 搜索可能在非叶子节点结束
* 在关键字全集内做一次查找，性能逼近二分查找
* 每个非终端结点中包含有n个关键字信息： (P1，K1，P2，K2，P3，......，Kn，Pn+1)。其中：
       <b>a)Ki (i=1...n)为关键字，且关键字按顺序升序排序K(i-1)< Ki; 
       b)Pi为指向子树根的接点，且指针P(i)指向子树种所有结点的关键字均小于Ki，但都大于K(i-1)。 
       c)关键字的个数n必须满足： [ceil(m / 2)-1]<= n <= m-1。</b>
## 2.B+tree

    B+tree是B-tree的变体

<b>与B-tree的不同点</b>
* 所有关键字储存在叶子节点中，内部节点（非叶子节点）不储存data
* 为所有叶子节点增加了一个链指针

![mysql_index-2](/images/mysql_index/mysql_index-2.png)

<b>B-tree 查找文件29的过程</b>
![mysql_index-3](/images/mysql_index/mysql_index-3.png)

1. 根据根节点指针找到文件目录跟磁盘块1，将其中信息导入到内存中(磁盘IO一次)
2. 此时位于磁盘块1中有两个文件17，35，由17<29<35可得知指针P2
3. 根据指针P2指向的磁盘块找到磁盘块3，将信息导入内存中(磁盘IO二次)
4. 此时位于磁盘块1中有两个文件26，30，由26<29<30可得知指针P2
5. 根据p2指针，我们定位到磁盘块8，并将其中的信息导入内存。(磁盘IO三次)
6. 此时内存中有两个文件名28，29。根据算法我们查找到文件29，并定位了该文件内存的磁盘地址


## 3.哈希索引（自定义哈希索引，InnoDB自适应哈希索引）

    MySQL只在memory引擎显示支持哈希索引
    哈希索引
