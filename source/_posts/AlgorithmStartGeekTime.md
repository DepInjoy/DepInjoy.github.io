---
layout: post
title: 算法初识
date: 2019-2-25 21：47
category:算法
tags: [算法]
description: 
---

​	2019年的经济寒冬，是我从小到大感受到最慌张紧张的一年，不可否认，这种慌张感有很大一部分原因是来自于2019年之前的堕落和毫无规划的人生。这一年我感受到了职业的瓶颈期，不断打击我对自我的认可感。后来我开始记录生活中的美好，用微博这个社交圈去监督，同时也刻意开始培训自己的习惯，报班学习是其中一项，从今天开始学习极客时间上覃超的算法课程并并留下以博客的形式进行记录，希望这里有老师的分享也有我自己的见解，希望这端学习可以使我获得很大的成长。

## 算法时间复杂度

​	o()是表达一个算法复杂度的常用方式，常用的算法时间复杂度有：

| 时间复杂度表达式 |  意义解释  |
| :--------------: | :--------: |
|       o(1)       | 常数复杂度 |
|     o(logn)      | 对数复杂度 |
|       o(n)       | 线性复杂度 |
|      o(n^2)      |    平方    |
|      o(n^3)      |    立方    |
|      o(2^n)      |    指数    |
|      o(n!)       |   j阶乘    |

以图像的形式呈现如下图所示，可见随着n的增大，时间复杂度的差别更加明显。

![](../_img/算法复杂度.jpg)

以计算1到N的和为例，展示时间复杂度的计算

```C++
//循环N次，算法的时间复杂度为o(N)
sum = 0;
for(int i = 1;i < N;i++)
	sum += i;

//只运算一次，算法复杂度为o(1)
sum = N(N+1)/2
```

而对于斐波那契数组

```C++
//斐波那契数组：1，1， 2， 3，5， 8， 13， .....
//递归实现
def flib(n)
	if (n == 0 or n == 1)
		return n
	else
		return flib(n - 1) + flib(n - 2)
```

假设n为6，计算过程可以简化如下：

![](E:\00_Project\code\00_blog\source\source\_img\斐波那契额数组递归实现时间复杂度.jpg)

可见，斐波那契额数列的递归实现的时间复杂度为o(2^n)，虽然算法实现简单，但是其时间复杂度很高。这样也可以看出数据结构和算法对于算法的实现很重要。



## 常见数据结构操作复杂度

![](E:\00_Project\code\00_blog\source\source\_img\常见数据结构操作复杂度.jpg)

​                                                                            [图片来源](http://bigocheatsheet.com/)

## 常见排序算法复杂度

![](E:\00_Project\code\00_blog\source\source\_img\常见排序算法复杂度.jpg)

​						   [图片来源](http://bigocheatsheet.com/)

### 链表和数组

|  算法  | 查询 | 插入 | 删除 |
| :----: | :--: | :--: | :--: |
|  数组  | o(1) | o(N) | o(N) |
| 单链表 | o(N) | o(1) | o(1) |
| 双链表 | o(N) | o(1) | o(1) |

```C++
	/**********************  单链表  **********************/
	Head-> Data|Next -> Data|Next -> Data|Next -> ...... -> Data|Next -> NULL
	//插入数据
	Head-> Data|Next ->           -> Data|Next -> ...... -> Data|Next -> NULL
        				Data|Next 
	//删除数据
	Head-> Data|Next --------------> Data|Next -> ...... -> Data|Next -> NULL
        				Data|Next 
   
   /**********************  双链表  **********************/
   
```



下面测试的单链表的数据结构定义：

```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
```



#### 链表反转

首先，**链表的特殊性在于只能通过next获取其下一个元素的指针。** 被反转的链表考虑特殊和一般形式进行分析：

```C++
NULL				//特殊情况，直接返回

1-2-3-4-5-NULL
反转过程描述如下：
1-NULL
2-1-NULL
3-2-1-NULL
4-3-2-1-NULL
5-4-3-2-1-NULL
```

在[LeetCode 链表反转](https://leetcode.com/problems/reverse-linked-list/solution/)测试的代码如下：

```C++
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode* cur = head;
        ListNode* pre = NULL;
        while(cur){
            ListNode* tmp = cur->next;
            cur->next = pre;
            pre = cur;
            cur = tmp;
        }
        return pre;
    }
};
```



#### 探测环

```C++
//无环形式：
1->2->3->4->5->null
//有环形式：
1->2->3->4
   |	 |
   7<-6<-5
```



##### set判重 

​	利用向set中插入时，如果元素已经存在，则将原来的数据覆盖。**时间复杂度O(N)。** 

```C++
class Solution {
public:
    bool hasCycle(ListNode *head) {
        int count = 0;
        std:set<ListNode*> tmp;
        while(head){
            tmp.insert(head);
            count += 1;
            if(tmp.size() < count)
                return true;
            head = head->next;
        }
        return false;
    }
};
```



##### 快慢指针，快慢相遇

​	快慢指针相遇的思想在于，快指针和慢指针起初都指向指针的头部，之后快指针每次移动两步，慢指针每次移动一步，如果快指针和慢指针相等则该有环，否则检测到空指针则认为该链表无环。**时间复杂度为O(N)。**

```C++
class Solution {
public:
    bool hasCycle(ListNode *head) {
        ListNode* low = head;
        ListNode* fast = head;
        while(low && fast && low->next && fast->next){
            low = low->next;
            fast = fast->next->next;
            if(low == fast)
                return true;
        }
        return false;   
    }
};
```



#### [链表相邻元素交换](https://leetcode.com/problems/swap-nodes-in-pairs/)

```
输入：
1->2->3->4
//@返回
2->1->4->3
```



#### [环形链表](https://leetcode.com/problems/linked-list-cycle-ii/)



#### [每k个节点一组翻转链表](https://leetcode.com/problems/reverse-nodes-in-k-group/)





### 堆栈和队列

#### [判断大中小括号的有效性](https://leetcode.com/problems/valid-parentheses/description/)

```c++

```



#### [用栈实现队列](https://leetcode.com/problems/implement-queue-using-stacks/description/)

```C++

```



#### [用队列实现栈](https://leetcode.com/problems/implement-stack-using-queues/description/)

```C++

```



#### 优先队列





