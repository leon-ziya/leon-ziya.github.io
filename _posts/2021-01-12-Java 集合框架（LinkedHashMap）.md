---
layout:     post
title:      Java集合框架系列
subtitle:    LinkedHashMap
date:       2021-01-12
author:     子崖
header-img: img/post-bg-ios10.jpg
catalog: 	 true
tags:
    - Java
    - 集合框架
    - LinkedHashMap
---


# Java 集合框架（LinkedHashMap）

## 1. LinkedHashMap底层分析

1. HashMap是一个无序的Map，因为每次根据key的hashCode映射到Entry数组上，所以遍历出来的顺序并不是写入的顺序
2. JDK推出了一个基于HashMap但是有顺序的LinkedHashMap来解决有排序需求的场景
3. 它的底层是继承于HashMap实现的，由一个双向链表所构成。
4. LinkedHashMap的排序方式有两种：
   1. 根据写入顺序排序
   2. 根据访问顺序排序

5. 其中根据访问顺序排序时，每次get都会访问的值移动到链表末尾，这样重复操作就能得到一个按照访问顺序排序的链表

### 1.1 默认是插入顺序



### 1.2 根据成员变量（accessOrder）可设置为按照访问顺序

```java
LinkedHashMap<String,String> map = new LinkedHashMap<>(16,0.75F,true);
```



![](/home/jianglai/图片/Flink/java-linkedHashMap-1.png)

![](/home/jianglai/图片/Flink/java-linkedHashMap-2.jpg)
