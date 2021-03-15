---
title: JVM 笔记 - 10 对象的实例化内存布局与访问定位
tags: 
    - JVM
    - 笔记
categories:
    - JVM
keywords: "JVM"
date: 2020-8-10 9:47
top_img:
cover: https://gitee.com/Javen-Liu/blogimage/raw/master/img/2020-7-18-1595057081263.png
---

# 对象的实例化

![对象实例化](https://gitee.com/Javen-Liu/blogimage/raw/master/img/20200810095052.png)

过程：

1. 加载类元信息
2. 为对象分配内存
3. 处理并发问题
4. 属性的默认初始化（零值初始化）
5. 设置对象头的信息
6. 属性的显式初始化、代码块中初始化、构造器中初始化

# 对象的内存布局

![](https://gitee.com/Javen-Liu/blogimage/raw/master/img/20200810202502.png)

![](https://gitee.com/Javen-Liu/blogimage/raw/master/img/20200810203942.png)

# 对象的访问定位

![](https://gitee.com/Javen-Liu/blogimage/raw/master/img/20200810204632.png)

- 句柄访问

  ![句柄访问](https://upload.cc/i1/2020/05/23/FnTVys.png)

- 指针访问（HotSpot采用）

  ![直接指针访问](https://upload.cc/i1/2020/05/23/yMAOhs.png)