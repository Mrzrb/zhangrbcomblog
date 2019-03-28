---
title: java LinkedList
date: 2019-03-28 01:03:54
tags:
keywords:
description:
---

# java Linked List 源码学习

嘿嘿，其实最近一直在看java相关的东西，上下班通勤的时候再看王争老师的数据结构，正好看到链表这一章，就自己实现了一下链表。 完了又去看了一下jdk对链表的实现。发现java对链表的实现还是挺优雅的。通过接口，可以让实现的链表扩展其他功能，比如Deque等

java的链表继承于```AbstractSequentialList```并实现了```List``` ```Deque``` ```Clonable``` ```java.io.Serializable ``` 接口

![](http://zhangrb-image.oss-cn-beijing.aliyuncs.com/image/20190328164737.png)

![](http://zhangrb-image.oss-cn-beijing.aliyuncs.com/image/20190328165145.png)

提供两个构造函数，默认的构造函数和一个接受```Collection <? extends E>```的构造函数

- ```Collection <? extends E>```的构造函数内部调用了```addAll```方法来进行链表的初始化

如下是java addAll的代码

```java
    @Override
    public boolean addAll(int index, Collection<? extends E> c) {
        checkPositionIndex(index);
        Object[] a = c.toArray();

        int newNum = a.length;
        if (newNum == 0) {
            return false;
        }

        Node<E> pred, succ;

        if (index == size) {
            succ = null;
            pred = last;
        } else {
            succ = node(index);
            pred = succ.prev;
        }

        for (Object o : a) {
            E e = (E) o;
            Node<E> newNode = new Node<>(pred, e, null);
            if (pred == null) {
                first = newNode;
            } else {
                pred.next = newNode;
            }
            pred = newNode;
        }

        if (succ == null) {
            last = pred;
        } else {
            pred.next = succ;
            succ.prev = pred;
        }

        size += newNum;
        modCount++;
        return true;
    }
```
``LinkList``提供了``linkFirst`` ``linkLast`` ``LinkBefore`` 来供``LinkList``进行内部的修改

对``LinkList``进行添加操作的有 ``add`` ``push`` 

它俩都是对``addFirst``的调用， 2️而``addFirst``是对``LinkFirst``的调用, ``LinkFirst``代码如下

```java
    private void linkFirst(E e) {
        final Node<E> f = first;
        final Node<E> newNode = new Node<>(null, e, f);
        first = newNode;

        if (f == null) {
            //考虑链表为空的情况
            last = newNode;
        } else {
            f.prev = newNode;
        }
        size++;
        modCount++;
    }
```

删除的操作和添加类似，在此不做赘述。 可以打开jdk源码来看一下，都很简单。

还有值得一提的是``LinkedList``里面有一个私有类``ListItr``用于对``LinkedList``的遍历，

``ListItr`` 实现了 ``ListIterator``接口，可以方便的对``LinkedList``进行遍历