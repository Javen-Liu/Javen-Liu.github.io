---
title: JVM 笔记 - 13 StringTable
tags: 
    - JVM
    - 笔记
categories:
    - JVM
keywords: "JVM"
date: 2020-8-15 17:30
top_img:
cover: https://gitee.com/Javen-Liu/blogimage/raw/master/img/2020-7-18-1595057081263.png
---

# String的基本特性

- String：字符串，使用一对""引起来表示
- String声明为final的，不可被继承
- String实现了Serializable接口：表示字符串是支持序列化的；也实现了Comparable接口：表示String可以比较大小
- String在 jdk8 及以前内部定义了 final char[ ] value 用于存储字符串数据，jdk9 时改为了 byte[ ]
- String的 String Pool 是一个固定大小的Hashtable，默认值大小长度为1009.如果放进 String Pool 的String很多，就会造成Hash冲突严重，从而导致链表会很长，而链表长了后直接会造成的影响就是当调用String.intern时性能会大幅下降
- 使用-XX:StringTableSize可设置StringTable的长度
- 在jdk6中StringTable是固定的，就是1009的长度，所以如果常量池中的字符串过多就会导致效率下降很快
- 在jdk7中，StringTable的长度默认值是60013,1009是可设置的最小值

# String的内存分配

- 在Java语言中有8种基本数据类型和一种比较特殊的类型String。这些类型为了使它们在运行过程中速度更快、更节省内存，都提供了一种常量池的概念。
- 常量池就类似一个Java系统级别提供的缓存。8种基本数据类型的常量池都是系统协调的，String类型的换成常量池比较特殊。它主要使用方法有两种
  - 直接使用双引号生命出来的String对象会直接存储在常量池中。比如：String info = "abc"
  - 如果不是用双引号声明的String对象，可以使用String提供的intern()方法
- Java 6以前，字符串常量池存放在永久代中
- Java 7对字符串的逻辑做了很大的改变，即将字符串常量池的位置调整到Java堆内
  - 所有的字符串都保存在堆（Heap）中，和其他普通对象一样，这样可以在调优应用时，仅需调整堆大小即可
  - 字符串常量池概念原本使用的比较多，但是这个改动使得我们有足够的理由让我们重新考虑在Java 7中使用String.intern()
- Java 8中字符串常量在堆中

![](https://gitee.com/Javen-Liu/blogimage/raw/master/img/20200817160832.png)

![](https://gitee.com/Javen-Liu/blogimage/raw/master/img/20200817160856.png)

**为什么StringTable要调整？**

1. permSize默认比较小
2. 永久代垃圾回收频率低

# 字符串拼接操作

1. 常量与常量的拼接结果在常量池，原理是**编译器优化**
2. 常量池中不会存在相同内容的常量
3. 只要其中有一个是变量，结果就在堆中。**拼接变量的原理是StringBuilder**
4. 如果拼接的结果调用intern()方法，则主动将常量池中还没有的字符创对象放入池中，并返回此对象的地址

![](https://gitee.com/Javen-Liu/blogimage/raw/master/img/20200817170054.png)

1. 字符串拼接操作不一定使用的是StringBuilder，如果拼接符号左右两百年都是字符串变量或常量引用，则仍然使用编译器优化，即非StringBuilder的方式
2. 针对于final修饰类、方法、基本数据类型、引用数据类型的量的结构时，能用final尽量使用final

# intern()的使用

## 概述

如果不是双引号声明的String对象，可以使用String提供的intern方法；intern方法会从字符串常量池中查询当年字符串是否存在，若不存在就会将当前字符串放入常量池中。

- 比如：String myInfo = new String("abc").intern();

也就是说，如果在任意字符串上调用String.intern方法，那么其返回结果所指向的那个类实例，必须和直接以常量形式出现的字符串实例完全相同。因此下列表达式的值必定是true：

- ("a" + "b" + "c").intern() == "abc"

通俗点讲，Interned String 就是确保字符串在内存里只有一份拷贝，这样可以节约内存空间，加快字符串操作任务的执行速度。注意，这个值会被存放在字符串内部池（String Intern Pool）

---

## 问题

**Q：new String("ab")会创建几个对象？**

![](https://gitee.com/Javen-Liu/blogimage/raw/master/img/20200818205305.png)

**A：**2个，一个是"ab"字符串常量，放在常量池中，另一个是new出来的String对象实例

# StringTable的垃圾回收

-XX:+PrintStringTableStatistics，用来查看字符串信息

# G1中的String去重操作

- 背景：许多Java应用做的测试得出以下结果：

  - 堆存活数据集合里面String对象占了25%
  - 堆存活数据集合里面重复的String对象有13.5%
  - String对象的平均长度是45

- 许多大规模的Java应用的瓶颈在于内存，测试表明在这些类型的应用里面，Java堆中存活的数据集合差不多25%是String对象。更进一步，这里面差不多一半String对象都是重复的，重复的意思是说：

  string1.equals(string2) = true

  堆上存在重复的String对象必然是一种内存的浪费。这个项目将在G1垃圾收集器中实现自动持续对重复String对象进行去重，这样就能避免浪费内存

- 实现：

  - 当垃圾收集器工作的时候，会访问堆上存活的对象。对每一个访问的对象都会检查是否是候选的要去重的String对象
  - 如果是，把这个对象的一个引用插入到队列中等待后续的处理。一个去重的线程在后台运行，处理这个队列。处理队列的一个元素意味着从队列删除这个元素，然后尝试去重他引用的String对象
  - 使用hashtable来记录所有的被String对象使用的不重复的char数组。当去重的时候，会查这个hashtable，来看堆上是否已经存在一个一模一样的char数组
  - 如果存在，String对象会被调整引用那个数组，释放对原来的引用，最终会被垃圾收集器回收掉
  - 如果查找失败，char数组会被插入到hashtable，这样以后就可以共享这个数组

- 命令行选项：

  - UseStringDeduplication (bool)：开启String去重，默认是不开启的，需要手动开启
  - PrintStringDeduplicationStatistics (bool)：打印详细的去重统计信息
  - StringDeduplicationAgeThreshold (uintx)：设置被认为是去重候选对象的年龄门槛