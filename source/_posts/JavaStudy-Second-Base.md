---
title: Java 入门 -- 基本程序设计
date: 2019-03-24 01:27:29
tags:
keywords:
description:
categories: 
- java
- java语言程序设计
---

# Java 入门 -- 基本程序设计

java程序基于类。一个基础的java程序如下:
```java
public class HelloWorld{
  public static void main(String[] args){
    System.out.println("Hello World");
  }
}
```

<!--more-->
以上就是一个简单的Java程序。很简单~ 🌝  Java是强类型语言，所以在声明变量的时候需要指定变量的类型。

## Java数值类型

java的数值类型有如下几种类型:(图片摘自《Java程序设计》)

![](https://i.loli.net/2019/01/28/5c4dda93d652d.jpg)
![](https://i.loli.net/2019/01/28/5c4ddab4ed953.jpg)

### 书中一个计算分秒的小程序

```java
package chapter.one;

import java.util.Scanner;

/**
 *
 * @author zrb
 */
public class Welcome {
    public static void main(String[] args){
        Scanner input = new Scanner(System.in);
        System.out.print("请输入秒数：");

        Integer second = input.nextInt();

        Integer minute = second / 60;
        Integer secondLeft = second % 60;

        System.out.println("对应的分秒数为： " + minute + "分" + secondLeft + "秒");

    }
}
```