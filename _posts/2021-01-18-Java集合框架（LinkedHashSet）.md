
---
layout:     post
title:      Java集合框架系列
subtitle:    LinkedHashSet
date:       2021-01-18
author:     子崖
header-img: img/post-bg-ios10.jpg
catalog: 	 true
tags:
    - Java
    - 集合框架
    - LinkedHashSet
---

# Java集合框架（LinkedHashSet）

## LinkedHashSet简述

LinkedHashSet继承自HashSet

LinkedHashSet内部使用的是LinkedHashMap存储数据

基于此，LinkedHashSet中的元素顺序可以保证，即==遍历==和==插入==顺序是一致的。

## LinkedHashSet特点

1. 底层基于LinkedHashMap存储数据
2. 保证元素的唯一性，由哈希表决定的(hashCode()和equals())
3. 可以保证元素的迭代顺序一致(有序), 存储和取出一致， 是由链表决定的

## LinkedHashSet使用场景

需要元素唯一，且要保证元素有序（存储和取出一致）
