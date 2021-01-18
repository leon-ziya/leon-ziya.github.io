---
layout:     post
title:      Java集合框架系列
subtitle:    HashMap
date:       2021-01-12
author:     子崖
header-img: img/post-bg-ios10.jpg
catalog: 	 true
tags:
    - Java
    - 集合框架
    - HashMap
---

# Java 集合框架（HashMap)

## HashMap介绍

HashMap主要用来存放键值对，它基于哈希表的Map接口实现，是常见的Java集合之一。

JDK1.8之前HashMap是由==数组+ 链表==组成的，数组是HashMap的主体，链表则是主要为了解决哈希冲突而存在的("拉链法"解决冲突)

JDK1.8之后HashMap是由==数组+链表+红黑树==组成的，当链表长度超过8时会自动转换成红黑树，当红黑树节点小于6时，又会转化成链表。

新增了红黑树作为底层数据结构，在数量较大且哈希碰撞较多时，能够极大的增加检索效率。

## 实现原理简述

HashMap基于Hash算法实现的，我们通过put(key, value)存储， get(key, value)来获取。

当传入key时，HashMap会根据key.hashCode()计算出Hash值，根据hash将value保存在bucket里。
当计算出的hash值相同时，我们称之为hash冲突（碰撞）， HashMap的做法是使用链表或红黑树存储相同的hash值的value，当hash冲突的个数大于TREEIFY THRESHOLD （默认8）时，链表转换为红黑树。

## 底层原理简析

1. 首先， HashMap通过Key的hashCode经过==绕动函数==处理后的到hash值。

2. 然后，通过(n - 1)&hash 判断当前元素存放的位置（n是数组的长度）

3. 如果当前位置存在元素，就判断==该元素==与==要存入的元素==的==hash值==以及==key==是否相同

4. 如果相同则直接==覆盖== ; 不同就通过==拉链法==解决冲突

   ==扰动函数：== 就是HashMap的hash方法，可以减少哈希碰撞

   `JDK1.8中HashMap的Hash方法：`

   ```java
   static final int hash(Object key){
       int h;
       return (key == null ) ? 0 : (h = key.hashCode())^(h >>> 16)
   }
   // key.hashCode()：返回散列值也就是hashcode; ^ 按位异或; >>> 无符号右移
   i = （table.length -1 ） &  hash; // 这一步是在后面添加元素putVal()方法中进行定位
   ```

   这里HashMap有个很巧妙的设计：

   > 1. 我们知道，只要某个对象，它的hashCode()返回值相同，那么程序调用hash(Object key)所计算的到的hash码就相同
   > 2. 为了是的数组元素均匀分布
   > 3. 使用（hash%length），但是取模运算开销很大
   > 4. 所以使用了(length -1 ) & hash得到该对象的保存位
   > 5. 只有当leongth总是2的n次方时, hash&(length -1) 等价于(hash % length)
   > 6. 所以，在初始化时，数组长度要求总是2的幂

   这里还有个高位参与运算：

   > 1. hashCode()得到的是一个32位的int类型的值
   > 2. 通过hashCode()的高16位==异或==低16位实现的
   > 3. 主要是从速度、功效、质量来考虑的
   > 4. 这样做可以在数组的length很小的时候，也能保证考虑到高低Bit都参与到Hash的计算中，同时不会有太大的开销

![](../img/java-HashMap-hash()中高位运算.png)

## 添加元素

1. 判断键值对数组==table==是否为空或null，否则执行resize()进行扩容;
2. 根据键值key计算hash值的到插入的数组索引i，如果table[i] == null, 直接新建节点添加，转向**6**, 如果table[i]不为空，则转向**3**;
3. 判断table[i]的首个元素的hash值和key是否和新添加的键值key一样，如果一样直接覆盖，转向**4**; 
4. 判断table[i]是否为TreeNode，即table[i]是否是红黑树，如果是红黑树，则直接在树中插入键值，否则转向**5**;
5. 遍历table[i], 判断链表长度是否大于8,大于8的话把链表转换为红黑树，在红黑树中执行插入操作，否则进行链表插入操作；遍历过程中如发现key已经存在直接覆盖value即可;
6. 插入成功之后，判断实际存在的键值对数量size是否超过了最大容量threshold, 如果超过，进行扩容。
7. 如果新插入的key不存在，则返回null， 如果新插入的key存在，则返回原key对应的值（==注意==：新插入的vlaue会覆盖原value值）

> 面试题：
>
> 一个HashMap中数组上有5个元素，某个链表上有3个元素，问此HashMap的size是多大？==答案==：7
> 		只要是调用put方法添加元素，就会调用==++size==（这里有个例外是插入重复的键值对，不会调用，但是重复的key元素不会影响size），所以，是7

## 扩容机制

使用resize方法进行扩容，会伴随着一次重新分配，并且会遍历hash表中的所有元素，是非常耗时的，所以在编写程序的时候，要尽量避免resize。





## HashMap常用方法测试

```java
import java.util.Collection;
import java.util.HashMap;
import java.util.Set;

public class HashMapDemo {

    public static void main(String[] args) {
        HashMap<String, String> map = new HashMap<String, String>();
        // 键不能重复，值可以重复
        map.put("san", "张三");
        map.put("si", "李四");
        map.put("wu", "王五");
        map.put("wang", "老王");
        map.put("wang", "老王2");// 老王被覆盖
        map.put("lao", "老王");
        System.out.println("-------直接输出hashmap:-------");
        System.out.println(map);
        /**
         * 遍历HashMap
         */
        // 1.获取Map中的所有键
        System.out.println("-------foreach获取Map中所有的键:------");
        Set<String> keys = map.keySet();
        for (String key : keys) {
            System.out.print(key+"  ");
        }
        System.out.println();//换行
        // 2.获取Map中所有值
        System.out.println("-------foreach获取Map中所有的值:------");
        Collection<String> values = map.values();
        for (String value : values) {
            System.out.print(value+"  ");
        }
        System.out.println();//换行
        // 3.得到key的值的同时得到key所对应的值
        System.out.println("-------得到key的值的同时得到key所对应的值:-------");
        Set<String> keys2 = map.keySet();
        for (String key : keys2) {
            System.out.print(key + "：" + map.get(key)+"   ");

        }
        /**
         * 另外一种不常用的遍历方式
         */
        // 当我调用put(key,value)方法的时候，首先会把key和value封装到
        // Entry这个静态内部类对象中，把Entry对象再添加到数组中，所以我们想获取
        // map中的所有键值对，我们只要获取数组中的所有Entry对象，接下来
        // 调用Entry对象中的getKey()和getValue()方法就能获取键值对了
        Set<java.util.Map.Entry<String, String>> entrys = map.entrySet();
        for (java.util.Map.Entry<String, String> entry : entrys) {
            System.out.println(entry.getKey() + "--" + entry.getValue());
        }
        
        /**
         * HashMap其他常用方法
         */
        System.out.println("after map.size()："+map.size());
        System.out.println("after map.isEmpty()："+map.isEmpty());
        System.out.println(map.remove("san"));
        System.out.println("after map.remove()："+map);
        System.out.println("after map.get(si)："+map.get("si"));
        System.out.println("after map.containsKey(si)："+map.containsKey("si"));
        System.out.println("after containsValue(李四)："+map.containsValue("李四"));
        System.out.println(map.replace("si", "李四2"));
        System.out.println("after map.replace(si, 李四2):"+map);
    }

}

```

