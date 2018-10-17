---
title: redis-adlist - 带你搞懂redis
date: 2018-10-16 00:14:49
description: 本文详解了redis list的实现过程
tags:
- redis
- source code
categories: 
- redis
- 带你搞懂redis
---

# Adlist

## listNode

```c
typedef struct listNode {

    // 前置节点
    struct listNode *prev;

    // 后置节点
    struct listNode *next;

    // 节点的值
    void *value;

} listNode;
```

双端链表结构

## list

```c
/*
 * 双端链表结构
 */
typedef struct list {

    // 表头节点
    listNode *head;

    // 表尾节点
    listNode *tail;

    // 节点值复制函数
    void *(*dup)(void *ptr);

    // 节点值释放函数
    void (*free)(void *ptr);

    // 节点值对比函数
    int (*match)(void *ptr, void *key);

    // 链表所包含的节点数量
    unsigned long len;

} list;
```
<!--more-->
## listCreate

```c
/*
 * 创建一个新的链表
 *
 * 创建成功返回链表，失败返回 NULL 。
 *
 * T = O(1)
 */
list *listCreate(void)
{
    struct list *list;

    // 分配内存
    if ((list = zmalloc(sizeof(*list))) == NULL)
        return NULL;

    // 初始化属性
    list->head = list->tail = NULL;
    list->len = 0;
    list->dup = NULL;
    list->free = NULL;
    list->match = NULL;

    return list;
}
```

## listAddNodeHead
```c
/* Add a new node to the list, to head, contaning the specified 'value'
 * pointer as value.
 *
 * On error, NULL is returned and no operation is performed (i.e. the
 * list remains unaltered).
 * On success the 'list' pointer you pass to the function is returned. */
/*
 * 将一个包含有给定值指针 value 的新节点添加到链表的表头
 *
 * 如果为新节点分配内存出错，那么不执行任何动作，仅返回 NULL
 *
 * 如果执行成功，返回传入的链表指针
 *
 * T = O(1)
 */
list *listAddNodeHead(list *list, void *value)
{
    listNode *node;

    // 为节点分配内存
    if ((node = zmalloc(sizeof(*node))) == NULL)
        return NULL;

    // 保存值指针
    node->value = value;

    // 添加节点到空链表
    if (list->len == 0) {
        list->head = list->tail = node;
        node->prev = node->next = NULL;
    // 添加节点到非空链表
    } else {
        node->prev = NULL;
        node->next = list->head;
        list->head->prev = node;
        list->head = node;
    }

    // 更新链表节点数
    list->len++;

    return list;
}
```

## 若干函数
```c
list *listAddNodeTail(list *list, void *value);
list *listInsertNode(list *list, listNode *old_node, void *value, int after);
void listDelNode(list *list, listNode *node);
listIter *listGetIterator(list *list, int direction);
listNode *listNext(listIter *iter);
void listReleaseIterator(listIter *iter);
list *listDup(list *orig);
listNode *listSearchKey(list *list, void *key);
listNode *listIndex(list *list, long index);
void listRewind(list *list, listIter *li);
void listRewindTail(list *list, listIter *li);
void listRotate(list *list);
```

