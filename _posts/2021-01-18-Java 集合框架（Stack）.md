
---
layout:     post
title:      Java集合框架系列
subtitle:    Stack
date:       2021-01-18
author:     子崖
header-img: img/post-bg-ios10.jpg
catalog: 	 true
tags:
    - Java
    - 集合框架
    - Stack
---

# Java 集合框架（Stack）

## Stack简述

Stack就是我们常听到的栈。

### Stack特点

1. Stack继承自Vector，实现了List、RandomAccess、Cloneable、Serializable。
2. Stack---“先进后出”原理
3. Stack本质是List，具备List的所有方法。

## Stack的添加元素方法

Vector的add方法

![Java-Vector-add()](../img/Java-Vector-add().png)

Stack的push方法

![Java-Stack-push()](../img/Java-Stack-push().png)

Stack的 push方法所调用的Vector的addElement方法

![Java-Stack-push-addElement()](../img/Java-Stack-push-addElement().png)

Stack的push方法所调用Vector的addElement方法和Vector的add方法基本相同的。

## Stack的常用方法

1. push入栈
2. peek获取栈顶元素，元素不出栈
3. pop栈顶元素出栈并返回

## Stack和ArrayList的区别

Stack和ArrayList的最大区别是：

**Stack是线程安全的**

