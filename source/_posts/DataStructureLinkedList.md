---
layout: post
title: 链表
date: 2019-5-7 10:32
category: 数据结构
tags: [数据结构]
description: 
---



## 单向链表

​	链表是一种动态的数据结构，它由一组地址可以是连续，也可以是不连续的地址空间来存放数据。链表中的每个元素为一个结点，每个结点由一个数值和指针构成，指针指向下一个数据结点，最后结点的指针为NULL。

### 性质：

- 只能通过next获取其下一个元素的指针

### 常用形式

```C++
	Head-> Data|Next -> Data|Next -> Data|Next -> ...... -> Data|Next -> NULL
	//插入数据
	Head-> Data|Next ->           -> Data|Next -> ...... -> Data|Next -> NULL
        				|        |
        				Data|Next 
	//删除数据
	Head-> Data|Next --------------> Data|Next -> ...... -> Data|Next -> NULL
        				Data|Next 
```



#### 单向链表定义

- C/C++定义

```C++
struct ListNode {
	int val;
	ListNode *next;
};
```

- Python定义

```Python
class ListNode(object):
	def __init__(self, x):
		self.val = x
		self.next = None
```



#### 初始化



#### 插入结点



#### 删除结点



### 单向链表训练题

####  [链表翻转](<https://leetcode-cn.com/problems/reverse-linked-list/>)

**实现思路**： 被反转的链表考虑特殊和一般形式进行分析：

```
NULL				//特殊情况，直接返回

1-2-3-4-5-NULL
反转过程描述如下：
1-NULL
2-1-NULL
3-2-1-NULL
4-3-2-1-NULL
5-4-3-2-1-NULL
```

**特殊情况**：

- 指针为空

[LeetCode实现代码](https://github.com/DepInjoy/BaseHouse/blob/master/Interview/%E9%93%BE%E8%A1%A8/%E9%93%BE%E8%A1%A8%E5%8F%8D%E8%BD%AC.cpp)



####  [每k个节点一组翻转链表](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)

**实现思路**：从头结点开始查找K个元素并存储在stack中，之后将这K个节点一一弹出并形成成一个链表，并将没有查找到的元素链接在上述链表的尾部。下面以示例展示过程：

```C++
链表：1->2->3->4->5，k=2

读取2个元素，压入stack(1,2)，将元素弹出形成链表（2->1）,并将未读取元素链接其后，形成（2->1->3->4->5）
再读取2个元素，压入stack(3,4)，将元素弹出形成链表（2->1->4->3）,并将未读取元素链接其后，形成（2->1->4->3->5）
再读取1个元素，不足看个元素，无操作，整个过程结束。
```

**特殊情况**:

- 指针为空
- k=0
- 链表中元素数目小于k。

[LeetCode验证实现代码](https://github.com/DepInjoy/BaseHouse/blob/master/Interview/%E9%93%BE%E8%A1%A8/%E6%AF%8Fk%E4%B8%AA%E8%8A%82%E7%82%B9%E4%B8%80%E7%BB%84%E7%BF%BB%E8%BD%AC%E9%93%BE%E8%A1%A8.cpp)



#### [探测环](https://leetcode-cn.com/problems/linked-list-cycle/)

```C++
//无环形式：
1->2->3->4->5->null
//有环形式：
1->2->3->4
   |	 |
   7<-6<-5
```

**实现思路**：

- set判重 ：利用向set中插入时，如果元素已经存在，则将原来的数据覆盖。**时间复杂度O(N)，空间复杂度为O(N)。** 

- 快慢指针，快慢相遇。快慢指针相遇的思想在于，快指针和慢指针起初都指向指针的头部，之后快指针每次移动两步，慢指针每次移动一步，如果快指针和慢指针相等则该有环，否则检测到空指针则认为该链表无环。**时间复杂度为O(N)。**

**特殊情况**：

- 指针为空

[LeetCode实现代码](https://github.com/DepInjoy/BaseHouse/blob/master/Interview/%E9%93%BE%E8%A1%A8/%E6%8E%A2%E6%B5%8B%E6%98%AF%E5%90%A6%E6%9C%89%E7%8E%AF.cpp)



#### [环形链表入口查找](<https://leetcode-cn.com/problems/linked-list-cycle-ii/>)

**实现思路**：

- 从头结点开始查询，并将读取到的元素存储于hash set中，在后续读取的元素中查询是否存在于set中，如果查找到，则该结点即为环形入口，如果查询不到，则返回为NULL。**时间复杂度为O(NlogN)，空间复杂度为O(N)。**

- 利用快慢指针（均从头开始，慢指针每次走一步，快指针每次走两步，如果不存在环，则返回为NULL；如果存在环则快指针和慢指针重合。此时指针位于相对于头结点偏离环形元素数的位置）。之后将快指针或者慢指针重置为头结点，快指针和慢指针，每次都移动一步，两者再次相遇即为环形入口。

**殊情况**：

- 指针为空
- 不存在环

[LeetCode实现代码](https://github.com/DepInjoy/BaseHouse/blob/master/Interview/%E9%93%BE%E8%A1%A8/%E7%8E%AF%E5%BD%A2%E9%93%BE%E8%A1%A8%E5%85%A5%E5%8F%A3%E6%9F%A5%E6%89%BE.cpp)



#### [链表相邻元素交换](https://leetcode-cn.com/problems/swap-nodes-in-pairs/comments/)

**实现思路**：将相邻的两个元素交换。

```C++
假设链表为：1->2->3->4->5

读取两个元素：2->1->3->4->5
再读取连个元素：读取两个元素：2->1->4->3->5
再读取一个元素，结束。
```

**特殊情况**：

- 指针为空
- 元素数目为奇数

[LeetCode实现代码](https://github.com/DepInjoy/BaseHouse/blob/master/Interview/%E9%93%BE%E8%A1%A8/%E9%93%BE%E8%A1%A8%E7%9B%B8%E9%82%BB%E5%85%83%E7%B4%A0%E4%BA%A4%E6%8D%A2.cpp)