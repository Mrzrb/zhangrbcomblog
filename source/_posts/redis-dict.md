---
title: redis字典详解 - 带你搞懂redis
keywords: redis,字典,dict,redis字典详解
date: 2018-10-16 17:03:51
tags:
- redis
- source code
categories: 
- redis
- 带你搞懂redis
---

# 字典结构
字典的结构如图所示

![](http://zhangrb-image.oss-cn-beijing.aliyuncs.com/18-10-26/39055441.jpg)

一个字典结构(dict)里面记录了dictType,有两个哈希表(为了rehash)，rehashidx记录了当前rehash的进度

## dictEntry结构
dictEntry 结构内包含:

- key
- v
- next

key 储存了键， v储存了值，v的结构如下所示，v可以储存一个指针、一个uint64_t的数，或者一个int64_t的值, next则是为了解决hash冲突指向下一个具有相同hash值的实体节点

```
 union {
        void *val;
        uint64_t u64;
        int64_t s64;
    } v;
```
<!--more-->
## dictht 结构

dictht结构内包含:

- table
- size
- sizemask
- used

table是一个dictEntry的数组，size记录了table数组的大小，sizemask为哈希表大小掩码为size-1，used记录了table中已有节点的数量

## dictType 结构

dictType 结构包含:

dictType里面包含了针对dict类型的特定的操作函数，如hash计算函数(hashFunction)，键复制函数(keyDup)等等.

## dict 结构
dict是字典的数据结构，dict包含:
- type
- privdata
- dictht[2]
- rehashidx
- iterators

type定义了dict的类型(操作dict的一系列方法)

privdata是dict的一些私有数据(用来给特定的类型特定函数的可选参数)

dictht是字典哈希表，有两个是为了进行rehash

rehashidx记录了rehash进度,当前没有进行rehash的时候为-1

iterators是当前字典正在运行的安全迭代器的数量

# 字典元素添加过程解析

## 向字典中加入元素的过程

向字典中加入元素的过程如下图所示：

![](http://zhangrb-image.oss-cn-beijing.aliyuncs.com/18-10-26/85703852.jpg)

## 添加过程中出现键冲突？

如果在上述过程中计算出的index在对应的ht的table中已经有元素了，redis此时会在该元素前面插入一个元素实体键值为要插入的键值.

# 字典rehash

## 字典rehash过程

字典会让本身的负载因子(used/size)维持在一个合理的值，如果负载因子过大，会降低字典的效率，过小会浪费宝贵内存空间。

字段通过rehash来保持负载因子合理，过程如下

- 为dict的ht[1]分配空间，如果是扩大操作，分配的空间大小为第一个大于ht[0].used*2的2^n的值，如果是收缩操作，大小为第一个大于ht[0].used的2^n的值
- 将ht[0]所有键值对rehash到ht[1]
- 释放ht[0],将ht[1]设置为ht[0],并在ht[1]创建一个新的空哈希表

## 触发字典rehash的条件

- 字典的负载因子大于等于1并且服务器没有在执行BGSAVE或者BGREWITEAOF
- 字典的负载因子大于5并且服务器在执行BGSAVE或者BGREWITEAOF
- 字典负载因子小于0.1，会执行收缩操作

## dict渐进式rehash

rehash不是一次性完成的，在rehash的时候字典会同时操作(delete,find,update)两个hash表。

如：
```c
dictEntry *dictFind(dict *d, const void *key)
{
    dictEntry *he;
    unsigned int h, idx, table;

    // 字典（的哈希表）为空
    if (d->ht[0].size == 0) return NULL; /* We don't have a table at all */

    // 如果条件允许的话，进行单步 rehash
    if (dictIsRehashing(d)) _dictRehashStep(d);
//！！！！！！注意在这里进行了rehash单步操作
    // 计算键的哈希值
    h = dictHashKey(d, key);
    // 在字典的哈希表中查找这个键
    // T = O(1)
   //........等等
}
```

# 结尾
字典部分就详解完毕了，如果后续有补充，会在本文进行修改添加，欢迎关注哦~~