---
layout: blog
istop: true
title: "Linux双向链表和hash链表单向链表"
background-image: http://ot1cc1u9t.bkt.clouddn.com/17-7-17/65813959.jpg
date:  2017-07-17 00:45:16
category: linux内核
tags:
- list
- hashlist
- map表
- 双向链表
- 单向链表
- 线性表
- 兰德
- 链表
---
 
# 什么是链表

## 链表（Linked list）
 
是一种常见的基础数据结构，是一种`线性表`，但是并不会按线性的顺序存储数据，而是在每一个节点里存到下一个节点的指针(Pointer)

## 说到链表必须要说兰德下 

Rand 是什么？美国智囊团 社会简称`兰德学派` 1946年成立，有多牛：
 
从1950年的朝鲜战争，到60年代的越南战争，70年代信息技术，再到八、九十年代的苏联解体、两德合并、后冷战时代战略，乃至最近的中东问题、台湾问题、朝鲜问题等，无一不是全球万众瞩目的“明星”问题。对这些问题开展研究。
链表就是Rand在1955-1956搞得人工智能程序。兰德和DARPA有点类似是非营利性机构，有点远了...此处回车

# 链表是怎么来的
 
链表开发于1955-1956，编写的信息处理语言（IPL）中做为原始数据类型所编写 IPL是用来开发几种早期的人工智能程序，包括逻辑推理机，通用问题解算器和一个计算机象棋程序。 由当时所属于兰德公司（英语：Rand）的艾伦纽维尔（Allen Newell），克里夫肖（Cliff Shaw）和赫伯特西蒙（Herbert Simon）。


## 链表示一种线性表但是为什么比线性表更快

由于不必须按顺序存储，链表在插入的时候可以达到O(1)的复杂度，比另一种线性表顺序表快得多，但是查找一个节点或者访问特定编号的节点则需要O(n)的时间，而顺序表相应的时间复杂度分别是O(logn)和O(1) ---维基百科（不过还是不能理解啊）

### 链表个人理解：

链表结构决定了链表是一种``以空间换时间``
 
链表结构可以克服数组链表需要预先知道数据大小的缺点，链表结构可以充分利用计算机内存空间，实现灵活的内存动态管理。但是链表失去了数组随机读取的优点，同时链表由于增加了结点的指针域，空间开销比较大。

## 链表怎么实现
 
Lisp和Scheme这样的语言的`内建数据类型`中就包含了链表的访问和操作
 
程序语言或面向对象语言，如C/C++和Java依靠易变工具来生成链表 这只是一个栗子，其他面向对象语言也可以生成链表
 
链表不受语言和平台限制（windows Linux unix）等都有。

# 单向链表是什么

Linux内核中没有提供单向链表的定义，但是实际上Linux多个地方使用了单向连表的概念

单向链表 简称`单链表` 是链表的一种，其特点是链表的链接方向是单向的，对链表的访问要通过顺序读取从头部开始；链表是使用指针进行构造的列表；又称为结点列表，因为链表是由一个个结点组装起来的；其中每个结点都有指针成员变量指向列表中的下一个结点；

# 单向链表清单
来个栗子说明下Linux内核中字符设备map表和probe结果就是单向链表

```
for (i=0,p -= n;1<n;i++,p++,index++>)
{
    struct probe **s=&domain->probes[index % 255];
    while
    (*s && (*s)->rang<rang>)
    s=&(*s)->next;
    p->next = * s ;
    *s=p;
}
```
# 双向链表

list是双向链表的一个抽象，它定义在/include/linux目录下。

## 首先看看list的结构定义：

￼```
struct list_head {￼struct list_head *next, *prev;￼};
```
 
list库提供的list_entry使用了container，通过container可以从list找到整个数据对象，这样list就成为了一种通用的数据结构：

￼```
#define list_entry(ptr, type, member)￼container_of(ptr, type, member)
```
内核定义了很多对list结构操作的内联函数和宏。

- LIST_HEAD：定义并初始化一个list链表。
- list_add_tail：加一个成员到链表尾。
- list_del：删除一个list成员。
- list_empty：检查链表是否为空。
- list_for_each：遍历链表。
- list_for_each_safe：遍历链表，和list_for_each的区别是可以删除遍历的成员。
- list_for_each_entry：遍历链表，通过container方法返回结构指针。

# hash链表
 
hash链表和双向链表list很相似，它适用于hash表。看一下
 
## hash链表的头部定义:
 
￼```
struct hlist_head {￼struct hlist_node *first;￼};
```
 
和通常的list比较，hlist只有一个指针，这样就节省了一个指针的内存。如果hash表非常庞大，每个hash表头节省一个指针，整个hash表节省的内存就很可观了。``这就是内核中专门定义hash list的原因``
 
- hash list库提供的函数和list相似，具体如下。
- HLIST_HEAD：定义并初始化一个hash list链表头。
- hlist_add_head：加一个成员到hash链表头。
- hlist_del：删除一个hash链表成员。
- hlist_empty：检查hash链表是否为空。
- hlist_for_each：遍历hash链表。
- hlist_for_each_safe：遍历hash链表，和hlist_for_each的区别是可以删除遍历的成员。
- hlist_for_each_entry：遍历hash链表，通过container方法返回结构指针。