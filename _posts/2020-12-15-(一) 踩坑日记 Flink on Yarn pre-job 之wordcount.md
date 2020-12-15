---
layout:     post
title:      踩坑日记-flink
subtitle:   flink on yarn 之 wordcount
date:       2020-12-15
author:     子崖
header-img: img/post-bg-map.jpg
catalog: 	 true
tags:
    - flink
    - flink on yarn
    - wordcount
---


#  (一) 踩坑日记 Flink on Yarn pre-job 之wordcount

## Flink on yarn 之 WordCount

### 背景

1. 实现Flink on yarn 的wordcount 案例时
2. 在自己本地的虚拟机上
3. hadoop实现的NameNode的HA
4. 集群配置好，hadoop启动了

### 现象

1. 在将任务以命令行的方式提交到yarn后报错

错误信息：

> 大致意思是，链接不上hdfs

### 分析

自己的hadoop的配置问题

在namenode上看不到datanode，一个都没有，但是ps查看，节点上的DataNode都存在的。

`我是在之前陪好的hadoop上做修改，然后将hadoop完整的同步到其他节点了，导致的是该节点上的namenode和datanode所生成的信息，也同步过去，三台节点的datanode和namenode完全一样了`-------就是乱套了……

`同步配置时， 切忌，小心谨慎`

### 验证

分别去hadoop目录下查看data下的data相关的目录中current中的VERSION是否相同
![](/home/jianglai/图片/NameNdoe不识别DataNode/linux01-datanode-version.png)

这是我的节点一中的data相关的VERSION内容，由于我的上述误操作，导致集群节点都是一样的

### 解决方案

删除所有data下的数据后，重新格式化NameNode
