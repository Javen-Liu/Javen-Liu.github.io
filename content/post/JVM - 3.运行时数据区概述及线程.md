---
title: JVM 笔记 - 3 运行时数据区概述及线程
tags: 
    - JVM
    - 笔记
categories:
    - JVM
keywords: "JVM"
date: 2020-7-14 08:46
top_img:
cover: https://gitee.com/Javen-Liu/blogimage/raw/master/img/2020-7-18-1595057081263.png
---

# 概述

![概述](https://gitee.com/Javen-Liu/blogimage/raw/master/img/20200718152857.png)

内存是非常重要的系统资源，是硬盘和CPU的中间仓库和桥梁，承载着操作系统和应用程序的实时运行。JVM内存布局规定了Java在运行过程中内存申请、分配、管理的策略，保证了JVM的高校稳定运行。**不同的JVM对于内部才能的划分方式和管理机制存在着部分差异。**

![内存空间](https://gitee.com/Javen-Liu/blogimage/raw/master/img/20200718155420.png)

------------

- Java虚拟机定义了若干种程序运行期间会使用到的运行时数据区，其中有一些会随着虚拟机启动而创建，随着虚拟机退出而销毁
- 另外一些则是与线程一一对应，这些与线程对应的区域会随着线程开始和结束而创建和销毁

![运行时数据区](https://gitee.com/Javen-Liu/blogimage/raw/master/img/20200718154857.png)

灰色的为单独线程私有的，红色为多个线程共享的，即：

- 每个线程：独立包括程序计数器（PC）、虚拟机栈（VMS）、本地方法栈（NMS）

- 线程间共享：堆（Heap）、堆外内存（永久代或元空间、代码缓存）

# 线程

- 线程是一个程序里的运行单元，JVM允许一个应用有多个线程并行的执行。
- 在HotSpot JVM里，每个线程都与操作系统的本地线程直接映射。
- 操作系统负责所有线程的安排调度到任何一个可用的CPU上，一旦本地线程初始化成功，它就会调用Java线程中的 run() 方法。