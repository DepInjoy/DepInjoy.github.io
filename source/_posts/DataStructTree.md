---
layout: post
title: 树
date: 2019-5-8 10:32
category: 数据结构
tags: [数据结构]
description: 
---



## 树

表示方式：

- 儿子-兄弟表示法
- 

### 定义

```C++
typedef struct TNode *Position;
typedef Position BinTree; /* 二叉树类型 */
struct TNode{ /* 树结点定义 */
    ElementType Data; /* 结点数据 */
    BinTree Left;     /* 指向左子树 */
    BinTree Right;    /* 指向右子树 */
};
```



### 基本性质

- 子树不相交
- 除了根节点之外，每个子树有且只有父结点。
- 一棵有N个节点的树有N-1个结点



### 二叉树

#### 遍历方式

​	为方便描述，假设数据为整型，对根结点的数据进行打印来表示对根节点处理。

##### 中序遍历

```C
void InorderTraversal( BinTree BT )
{
    if( BT ) {
        InorderTraversal( BT->Left );
        /* 此处假设对BT结点的访问就是打印数据 */
        printf("%d ", BT->Data); /* 假设数据为整型 */
        InorderTraversal( BT->Right );
    }
}
```

#### 前序遍历

```C
void PreorderTraversal( BinTree BT )
{
    if( BT ) {
        printf("%d ", BT->Data );
        PreorderTraversal( BT->Left );
        PreorderTraversal( BT->Right );
    }
}
```

#### 后序遍历

```C
 void PostorderTraversal( BinTree BT )
{
    if( BT ) {
        PostorderTraversal( BT->Left );
        PostorderTraversal( BT->Right );
        printf("%d ", BT->Data);
    }
}
```

#### 层次遍历

```C
void LevelorderTraversal ( BinTree BT )
{ 
    Queue Q; 
    BinTree T;
 
    if ( !BT ) return; /* 若是空树则直接返回 */
     
    Q = CreatQueue(); /* 创建空队列Q */
    AddQ( Q, BT );
    while ( !IsEmpty(Q) ) {
        T = DeleteQ( Q );
        printf("%d ", T->Data); /* 访问取出队列的结点 */
        if ( T->Left )   AddQ( Q, T->Left );
        if ( T->Right )  AddQ( Q, T->Right );
    }
}
```

#### 二叉搜索树

​	二叉搜索树(Binary Search Tree)，也称有序二叉树(Ordered Binary Tree)以及排序二叉树(Sorted Binary Tree)，指一颗空树或者具有下面性质的二叉树：

- 左子树上的所有节点的值都小于它的根节点的值。
- 右子树上的所有节点的值都小于它的根节点的值。

- 递归地，左子树和右子树也分别为二叉搜索树。



### 训练题

#### [二叉树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)

[LeetCode实现](https://github.com/DepInjoy/BaseHouse/blob/master/Interview/%E6%A0%91/%E4%BA%8C%E5%8F%89%E6%A0%91%E7%9A%84%E6%9C%80%E8%BF%91%E5%85%AC%E5%85%B1%E7%A5%96%E5%85%88.cpp)



#### [二叉搜索树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)

[LeetCode实现](https://github.com/DepInjoy/BaseHouse/blob/master/Interview/%E6%A0%91/%E4%BA%8C%E5%8F%89%E6%90%9C%E7%B4%A2%E6%A0%91%E7%9A%84%E6%9C%80%E8%BF%91%E5%85%AC%E5%85%B1%E7%A5%96%E5%85%88.cpp)

