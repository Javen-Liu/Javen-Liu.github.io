---
title: IDEA热部署
tags:
    - IDEA
    - Java
categories:
    - 日常
keywords: "日常"
date: 2019-12-20 19:34:06
top_img:
cover: https://gitee.com/Javen-Liu/blogimage/raw/master/img/20200606164412.png
---

# 1. 引入spring-devtools

在项目的pom.xml文件中，添加spring-devtools的依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
</dependency>
```

同时在其中的<build>标签的<plugins>内添加

```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <configuration>
        <fork>true</fork>
    </configuration>
</plugin>
```

# 2. 设置idea

- 在File => Settings => Build, Execution, Deployment => Compiler 中勾选Build Project automatically

![x](https://gitee.com/Javen-Liu/blogimage/raw/master/img/20200602111804.png)

- 快捷键 ctrl + alt + shift + /，选择Registry，勾上Compiler autoMake allow when app running

![](https://gitee.com/Javen-Liu/blogimage/raw/master/img/20200602111848.png)

![](https://gitee.com/Javen-Liu/blogimage/raw/master/img/20200602111912.png)