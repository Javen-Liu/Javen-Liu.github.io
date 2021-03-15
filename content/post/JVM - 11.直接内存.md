---
title: JVM 笔记 - 11 直接内存
tags: 
    - JVM
    - 笔记
categories:
    - JVM
keywords: "JVM"
date: 2020-8-12 10:04
top_img:
cover: https://gitee.com/Javen-Liu/blogimage/raw/master/img/2020-7-18-1595057081263.png
---

# 直接内存概述

- 直接内存不是虚拟机运行时数据区的一部分，也不是《Java虚拟机规范》中定义的内存区域
- 直接内存是在Java堆外的、直接向系统申请的内存空间
- 来源于NIO，通过存在堆中的DirectByteBuffer操作Native内存
- 通常，访问直接内存的速度会优于Java堆。即读写性能高
  - 因此处于性能考虑， 读写频繁地场合可能会考虑使用直接内存
  - Java的NIO库允许Java程序直接使用直接内存，用于数据缓冲区

非直接缓冲区：

![](https://gitee.com/Javen-Liu/blogimage/raw/master/img/20200812103850.png)

直接缓冲区：

![](https://gitee.com/Javen-Liu/blogimage/raw/master/img/20200812104014.png)

- 当然直接内存也会导致OutOfMemory异常
- 由于直接内存在Java堆外，因此它的大小不会直接受限于-Xmx指定的最大堆大小，但是系统内存是有限的，Java堆和直接内存的总和仍然受限于操作系统能给出的最大内存
- 缺点：
  - 分配回收成本较高
  - 不收JVM内存回收管理
- 直接内存大小可以通过MaxDirectMemorySize设置
- 如果不指定，默认与堆的最大值-Xmx参数一致