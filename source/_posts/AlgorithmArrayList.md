---
layout: post
title: 数组和链表
date: 2019-2-28 21：06
category:算法
tags: [算法]
description: 
---



|  算法  | 查询 | 插入 | 删除 |
| :----: | :--: | :--: | :--: |
|  数组  | o(1) | o(N) | o(N) |
| 单链表 | o(N) | o(1) | o(1) |
| 双链表 | o(N) | o(1) | o(1) |



单链表定义

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

