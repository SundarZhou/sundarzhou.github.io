---
layout: post
title: "我的第一个 JAVA 程序"
date: 2022-05-24 00:03:31 +0800
categories: Java HelloWorld
---

## 我的第一个 JAVA 程序
创建文件 HelloWorld.java(文件名需与类名一致), 代码如下：

```java
public class HelloWorld {
  public static void main(String[] args) {
    System.out.println("Hello World!");
  }
}
```

命令行执行：
```
javac HelloWorld.java
java HelloWorld # output: Hello World
```