---
layout:     post
title:      Java集合框架系列
subtitle:    ArrayList/Vector
date:       2021-01-12
author:     子崖
header-img: img/post-bg-ios10.jpg
catalog: 	 true
tags:
    - Java
    - 集合框架
    - ArrayList/Vector
---

# Java 集合框架(ArrayList/Vector)

## ArrayList 介绍

1. ArrayList的底层是数组队列，相当于是动态数组
2. 与Java中的数组相比，它的容量能动态增长
3. 在添加大量元素之前，应用程序可以使用ensureCapacity操作来增加ArrayList实例的容量，这可以减少递增式再分配的数量.
4. 继承于　AbstractList; 实现了List、RandomAccess、Cloneable、 java.io.Serializable接口;
5. ArrayList继承了 AbstractList， 实现了List，他是一个数组队列，提供了相关的添加、删除、修改、遍历等功能。
6. ArrayList实现了RandomAccess接口，提供了随机访问的功能。RandomAccess是Java用来被List实现，为List提供快速访问功能的，在ArrayList中，我们可以通过元素的序号快速获取到元素对象，这就是快速随机访问。
7. ArrayList实现了Cloneable接口，即覆盖了函数clone()，能被克隆。
8. ArrayList实现了Serializable接口，这意味着ArrayList支持序列化，能够通过序列化去传输。
9. ArrayList不是线程安全的，建议单线程使用ArrayList
10. 多线程中可以使用Vector或者CopyOnWriteArrayList

## ArrayList 源码





## ArrayList源码分析

在源码中System.arraycopy()和Arrays.copyOf()方法广泛使用于实现数组复制中。

### System.arraycopy() 

如在add(int index, E element)方法中很巧妙的用到了arraycopy()方法让数组自己复制自己实现让index开始之后的所有成员后移一个位置：

**在此列表中的指定位置插入指定元素**

**先调用rangeCheckForAdd对index进行界限检查; 然后调用ensureCapacityInternal方法保证capacity足够大;**

**再将从index开始之后的所有成员后移一个位置；将element插入到index位置，最后， size 加1**

```java
public void add (int index, E element){
    rangeCheckForAdd(index);
    ensureCapacityInternal(size + 1);
    System.arraycopy(elementData, index, elementData, index + 1, size - index);
    elementData[index] = element;
    size ++;
}
// elementData: 源数组；index：源数组中的起始位置；elementData: 目标数组；index + 1 : 目标数组中的起始位置；size -index： 要复制的数组元素的数量
```





### Arrays.copyOf()

又如toArray()方法中用到了copyOf()方法：

**以正确的顺序(从第一个到最后一个元素)返回一个包含次列表中所有元素的数组**

**返回的数组将是`安全的`因为该列表不保留对它的引用，即该方法必须分配一个新的数组**

**因此，调用者可以自由地修改返回的数组，此方法充当基于队列和基于集合的API之间的桥梁**

```java
public Object[] toArray(){
    return Arrays.copyOf(elementData, size);
}
// elementData : 要复制的数组；size： 要复制的长度
```

### 两者的联系和区别：

#### 联系

从源码中可以发现：copyOf()内部调用了arraycopy()方法

#### 区别

arraycopy()需要目标数组，将源数组拷贝到你自己自定义的数组里，而且可以选择拷贝的起点和长度，以及放入新数组中的位置；

copyOf() 是系统自动在内部新建一个数组，并返回该数组;

## ArrayList的扩容机制

ArrayList的扩容机制提高了性能，如果每次只能扩容一个，那么频繁的拷贝会降低性能，而ArrayList的扩容机制避免了这种情况

### ensureCapacity(int minCapacity)

```java
// 如有必要，增加此ArrayList实例的容量，以确保它至少能容纳元素的数量
public void ensureCapacity(int minCapacity) {
        int minExpand = (elementData != DEFAULTCAPACITY_EMPTY_ELEMENTDATA)
            // any size if not default element table
            ? 0
            // larger than default for default empty table. It's already
            // supposed to be at default size.
            : DEFAULT_CAPACITY;

        if (minCapacity > minExpand) {
            ensureExplicitCapacity(minCapacity);
        }
    }
```



### ensureCapacityInternal(int minCapacity)

```java
// 得到最小扩容量
private void ensureCapacityInternal(int minCapacity) {
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
              // 获取默认的容量和传入参数的较大值
            minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
        }
        ensureExplicitCapacity(minCapacity);
}
```



### ensureExlicitCapacity(int minCapacity)

```java
// 判断是否需要扩容，上面两个方法都要调用
private void ensureExplicitCapacity(int minCapacity) {
        modCount++;
        // 如果说minCapacity也就是所需的最小容量大于保存ArrayList数据的数组的长度的话，就需要调用grow(minCapacity)方法扩容。
        //这个minCapacity到底为多少呢？举个例子在添加元素(add)方法中这个minCapacity的大小就为现在数组的长度加1
        if (minCapacity - elementData.length > 0)
            //调用grow方法进行扩容，调用此方法代表已经开始扩容了
            grow(minCapacity);
    }
```



### grow（int minCapacity)

```java
// ArrayList扩容的核心方法
 private void grow(int minCapacity) {
       //elementData为保存ArrayList数据的数组
       ///elementData.length求数组长度elementData.size是求数组中的元素个数
        // oldCapacity为旧容量，newCapacity为新容量
        int oldCapacity = elementData.length;
        //将oldCapacity 右移一位，其效果相当于oldCapacity /2，
        //我们知道位运算的速度远远快于整除运算，整句运算式的结果就是将新容量更新为旧容量的1.5倍，
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        //然后检查新容量是否大于最小需要容量，若还是小于最小需要容量，那么就把最小需要容量当作数组的新容量，
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        //再检查新容量是否超出了ArrayList所定义的最大容量，
        //若超出了，则调用hugeCapacity()来比较minCapacity和 MAX_ARRAY_SIZE，
        //如果minCapacity大于最大容量，则新容量则为ArrayList定义的最大容量，否则，新容量大小则为 minCapacity。 
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
```

1. 定义旧容量为数组长度， 定义新容量是就容量的1.5倍
2. 检查新容量是否大于最小需求容量，若小于，则直接将最小需求容量当作新容量
3. 再判断新容量是否超出了ArrayList所定义的最大容量
4. 若超出最大容量了，就调用hugeCapacity()来比较最小需求容量和最大容量
5. 需求大于最大，则定义新容量为最大，若小于最大，则新容量为最小需求容量

## 注意

### 1.移位运算 

​		   ==<<（左移）; >>（带符号右移）; >>>（无符号右移）==

​			==右移一位相当于除2, 右移n位相当于除以2的n次方==

### 2. length属性、length()方法、size()方法

1. java中的length==属性==是针对数组来说的，代表数组的长度
2. java中的length()==方法==是针对字符串说的，代表字符串的长度
3. Java中的size()==方法==是针对泛型集合说的，代表这个泛型有多少个元素

### 3. ArrayList的四个内部类

```
(1)private class Itr implements Iterator<E>  
 (2)private class ListItr extends Itr implements ListIterator<E>  
 (3)private class SubList extends AbstractList<E> implements RandomAccess  
 (4)static final class ArrayListSpliterator<E> implements Spliterator<E>  
```

1. Itr     实现了Iterator接口， 同时重写了里面的hasNext()、next()、remove()等方法;
2. ListItr  继承Itr 实现了ListIterator接口，重写了hasPrevious()、nextIndex()、previousIndex()、previous()、set(E e)、add(E e)等方法
3. ==Interator==和==ListInterator==的区别：ListInterator在Interator的基础上增加了添加对象、修改对象、逆向遍历等方法。

## Vector

Vector也是实现了List接口， 底层数据结构和ArrayList类似，也是一个动态数组存放数据。

不同处是在add()方法的时候使用了synchronized进行同步写数据，但是开销较大，所以Vector是一个同步容器并不是一个并发容器

```java
public synchronized boolean add(E e){
    	modCount++;
    	ensureCapactiyHelper(elementCount + 1);
    	elementData[elementCount++] = e;
    return true;
}
```



指定位置插入数据

```
public void add(int index, E element) {
        insertElementAt(element, index);
    }
    public synchronized void insertElementAt(E obj, int index) {
        modCount++;
        if (index > elementCount) {
            throw new ArrayIndexOutOfBoundsException(index
                                                     + " > " + elementCount);
        }
        ensureCapacityHelper(elementCount + 1);
        System.arraycopy(elementData, index, elementData, index + 1, elementCount - index);
        elementData[index] = obj;
        elementCount++;
    }
```

