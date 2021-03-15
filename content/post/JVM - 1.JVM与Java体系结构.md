---
title: JVM 笔记 - 1 JVM与Java体系结构
tags: 
    - JVM
    - 笔记
categories:
    - JVM
keywords: "JVM"
date: 2020-7-10 12:31
top_img:
cover: https://gitee.com/Javen-Liu/blogimage/raw/master/img/2020-7-18-1595057081263.png
---

# JVM与Java体系结构

## JVM的整体结构

HotSpot VM是目前市面上高性能虚拟机的代表作之一。

它采用解释器与解释编译期并存的架构。

![JVM结构](https://gitee.com/Javen-Liu/blogimage/raw/master/img/20200714161757.png)

## JVM的架构模型

Java编译器输入的指令流基本上是一种基于 **栈的指令集架构**，另外一种指令集架构则是基于 **寄存器的指令集架构**。

两者之间的区别：

基于栈式架构的特点：

- 设计和实现更简单，适用于资源受限的系统；
- 避开了寄存器的分配难题：使用零地址指令方式分配；
- 指令流中的执行大部分是零地址指令，其执行过程中依赖于操作栈。指令集更小，编译器容易实现；
- 不需要硬件支持，可移植性更好，更好实现跨平台。

基于寄存器架构的特点：

- 典型的应用是x89=6的二进制指令集：比如传统的pc；
- **指令集架构则完全依赖于硬件，可移植性差；**
- **性能优秀和执行更高效；**
- 花费更少的指令去完成一项操作；
- 在大部分情况下，基于寄存器架构的指令往往都是以一地址指令、二地址指令和三地址指令为主，而基于栈式架构的指令集却是以零地址指令为主。

总结：由于跨平台的设计，Java的指令都是根据栈来设计的。不同平台CPU架构不同，所以不能设计为基于寄存器的架构。其优点是跨平台，指令集小，编译器容易实现；缺点则是性能下降，实现同样的功能需要更多的指令。

# JVM的生命周期

## 虚拟机的启动

Java虚拟机的启动是通过引导类加载器（bootstrap class loader）穿件一个初始类（initial class）来完成的，这个类是由虚拟机的具体实现指定的。

## 虚拟机的执行

- 一个运行中的Java虚拟机有着一个清晰的任务：执行Java程序；
- 程序开始执行时它运行，程序结束时它就停止；
- 执行一个所谓的Java程序的时候，真真正正在执行的是一个叫做Java虚拟机的进程。

## 虚拟机的退出

有如下几种情况：

- 程序正常执行结束；
- 程序在执行过程中遇到了异常或错误而异常终止；
- 由于操作系统出现错误而导致Java虚拟机进程终止；
- 某线程调用 Runtime 类或 System 类的 exit 方法，或 Runtime 类的 halt 方法，并且Java安全管理器也允许这次 exit 或 halt 操作；
- 除此之外，JNI（Java Native Interface）规范描述了用JNI Invocation API来加载或卸载Java虚拟机时，Java虚拟机的退出情况。

# JVM发展历程

## SUN Classic VM

- 第一款商用Java虚拟机
- 内部只提供解释器，如果要使用JIT编译器则需要外挂
- 解释器和编译器不能配合工作。

## Exact VM

- Exact Memory Management：准确式内存管理
- 具备现代高性能虚拟机的雏形：热点探测，编译器和解释器混合工作
- 只在Solaris平台短暂使用，终被HotSpot替代

## HotSpot *

- JDK6与JDK8默认的虚拟机都是HotSpot
- HotSpot指的是它的热点代码探测技术
  1. 通过计数器找到最具有编译价值代码，出发即时编译或栈上替换
  2. 通过编译器与解释器协同工作，在最优化的程序响应时间与最佳执行性能中取得平衡

## JRockit *

- 该虚拟机不太关注启动速度，其全部代码都是靠即时编译器编译执行，不包含解释器
- JRockit提供了全面的Java运行时解决方案组合：
  1. 其JRockit Real Time提供以毫秒或微秒级的JVM响应时间，适合财务、军事指挥等需要
  2. MissionControl服务套件，是一组以极低开销来监控、管理和分析生产环境中的应用程序的工具
- 目前HotSpot整合了JRockit，移植了JRockit的优秀特性

## J9 *

- 全称：IBM Technology for Java Virtual Machine，内部代号为J9
- 市场定位与HotSpot接近

## KVM 和 CDC/CLDC HotSpot

- 针对Java ME产品的虚拟机
- KVM简单、轻量、高度可移植性，面向更低端的设备上还维持着自己的一片市场

## Azul VM

- 前面三大“高性能Java虚拟机”使用在通用硬件平台上
- Azul VM 和 BEA Liquid VM 与特定的硬件平台绑定、软硬件配合的专有虚拟机
- 每个 Azul VM 实例都可以管理至少数十个CPU和数百GB内存的硬件资源，并提供在巨大内存范围内实现可控的GC时间的垃圾回收器、专有硬件优化的线程调度等优秀特性
- 2010年，Azul Systems公司发布了自己的 Zing JVM，可以再通用x86平台上提供接近于Vega系统的特性

## Liquid VM

- 高性能Java虚拟机中的战斗机
- BEA公司开发的，直接运行在自己Hypervisor系统上
- Liquid VM 即是现在的 JRockit VE（Virtual Edition），Liquid VM 无需操作系统的支持，或者说它自己本身实现了一个专有操作系统的必要功能，如线程调度、文件系统、网络支持等

## Apache Harmony

- 它是IBM和Intel联合开发的开源JVM
- 虽然目前没有Apache Harmony被大规模商用的案例，但是它的Java类库代码被吸纳进了Android SDK

## Microsoft JVM

- 微软为了在IE3浏览器中支持Java Applets，开发了Microsoft JVM
- 只能在windows平台运行，但确实是windows下性能最好的Java VM

## TaobaoJVM

- 基于 openJDK 开发了自己定制版本的 AlibabaJDK

## Graal VM

- 2018年4月，oracle Labs公开了Graal VM，号称"Run Programs Faster Anywhere"
- Graal VM在HotSpot VM基础上增强而成的**跨语言全栈虚拟机**，**可以作为“任何语言”的运行平台使用**，包括：Java、Scala、Groovy、Kotlin、C、C++、JS、Python等
- 如果说HotSpot有一天真的被取代，Graal VM希望最大

> 名称后带*的即为当前三大Java虚拟机