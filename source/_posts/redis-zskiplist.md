---
title: redis-zskiplist - 带你搞懂redis
date: 2018-10-24 19:47:46
tags:
- redis
- source code
categories: 
- redis
- 带你搞懂redis
---


# skiplist

跳跃表有两个关键的结构skiplist和skiplistNode

skiplist 记录了跳跃表里面包含节点的数量，skiplistNode包含了每个节点的层数

## zslCreate

```c
zskiplist *zslCreate(void) {
    int j;
    zskiplist *zsl;

    // 分配空间
    zsl = zmalloc(sizeof(*zsl));

    // 设置高度和起始层数
    zsl->level = 1;
    zsl->length = 0;

    // 初始化表头节点
    // T = O(1)
    zsl->header = zslCreateNode(ZSKIPLIST_MAXLEVEL,0,NULL);
    for (j = 0; j < ZSKIPLIST_MAXLEVEL; j++) {
        zsl->header->level[j].forward = NULL;
        zsl->header->level[j].span = 0;
    }
    zsl->header->backward = NULL;

    // 设置表尾
    zsl->tail = NULL;

    return zsl;
}
```

<!--more-->

```zslCreate```的作用主要是初始化了跳跃表，以及初始化了zsl的header节点的level.


我们关键看27行 ```zslCreateNode```的代码
```c
zskiplistNode *zslCreateNode(int level, double score, robj *obj) {
    
    // 分配空间
    zskiplistNode *zn = zmalloc(sizeof(*zn)+level*sizeof(struct zskiplistLevel));

    // 设置属性
    zn->score = score;
    zn->obj = obj;

    return zn;
}
```

```zslCreateNode```创建了一个```zskiplistNode```并进行了初始化

> 未完待续