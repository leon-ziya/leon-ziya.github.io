---
layout:     post
title:      Java集合框架系列
subtitle:    HashSet
date:       2021-01-12
author:     子崖
header-img: img/post-bg-ios10.jpg
catalog: 	 true
tags:
    - Java
    - 集合框架
    - HashSet
---

# Java 集合框架(HashSet)

## HashSet介绍

1. HashSet是一个不允许存储重复元素的集合
2. 主要有两个变量：map 和PRESENT
3. map: HashMap类型， 主要存放最终的数据
4. PRESENT： 是所有写入map的Value值

## 构造函数

```java
public HashSet() {
        map = new HashMap<>();
    }

    public HashSet(int initialCapacity, float loadFactor) {
        map = new HashMap<>(initialCapacity, loadFactor);
    }    
```



## 添加元素函数

```java
public boolean add(E e) {
        return map.put(e, PRESENT)==null;
    }
```

比较关键的就是这个add()方法。

可以看出它是将存放的对象当做了HashMap 的健,value都是相同的PRESENT。

由于HashMap的key是不能重复的,所以每当有重复的值写入到HashSet时,value会被覆盖,但key不会受到影响,这样就保证了HashSet中只能存放不重复的元素。

 HashSet的原理比较简单,几乎全部借助于HashMap来实现的。
