﻿---
title: Java方法签名
date: 2016-08-14 14:50:34
tags: JAVA
categories: JAVA
---

## 方法签名的意义

- 对于同名不同类、同类不同名的方法，方法签名的意义并不是很大，但是对于重载方法来说，方法签名的意义就十分巨大了。由于重载方法之间的方法名是相同的，那么我们势必要从构成方法的其他几个要素中找到另一个要素与方法名组成能够唯一标示方法的签名，方法体当然不予考虑。那么就是形参列表和返回值了，但是由于对于调用方法的人来说，方法的形参数据类型列表的重要程度要远远高于返回值，所以方法签名就由方法名+形参列表构成，也就是说，__方法名和形参数据类型列表可以唯一的确定一个方法，与方法的返回值一点关系都没有，这是判断重载重要依据，所以，以下的代码是不允许的__

```java
public long aaaa(){  
      
}  
public int aaaa(){  
      
}  
```



## 方法签名的格式

- 首先我们先看几个方法以及他们的方法签名：

```java
public void test1(){}                   test1()V
public void test2(String str)     test2(Ljava/lang/String;)V
public int test3(){}                      test3()I
```

- 从以上三个例子，我们就可以很简单的看出一些小小的规律：
JVM为我们提供的方法签名实际上是由方法名(上文的例子为了简单没有写出全类名)、形参列表、返回值三部分构成的，基本形式就是：
__全类名.方法名(形参数据类型列表)返回值数据类型__


- __Java方法签名中特殊字符/字母含义__


| 特殊字符	| 数据类型	| 特殊说明  |
|-----------|:----------|:----------|
| V	| void 	| 一般用于表示方法的返回值
| Z	| boolean	 
| B	| byte	 
| C| 	char	 
| S	| short	 
| I	| int	 
| J	| long	 
| F	| float	 
| D	| double	 
| [| 	数组| 	以[开头，配合其他的特殊字符，表示对应数据类型的数组，几个[表示几维数组
| L| 全类名;| 	引用类型	以 L 开头 ; 结尾，中间是引用类型的全类名


<!-- more -->

> 一定要注意的是方法重载时，方法返回值没有什么意义，是由方法名和参数列表决定的


## 利用javap生成方法签名

### 类库类

```java
$ javap -s java.lang.String  
Compiled from "String.java"  
public final class java.lang.String extends java.lang.Object implements java.io.Serializable,java.lang.Comparable,java.lang.CharSequence{  
public static final java.util.Comparator CASE_INSENSITIVE_ORDER;  
  Signature: Ljava/util/Comparator;  
public java.lang.String();  
  Signature: ()V  
public java.lang.String(java.lang.String);  
  Signature: (Ljava/lang/String;)V  
public java.lang.String(char[]);  
  Signature: ([C)V  
public java.lang.String(char[], int, int);  
  Signature: ([CII)V  
public java.lang.String(int[], int, int);  
  Signature: ([III)V  
public java.lang.String(byte[], int, int, int);  
  Signature: ([BIII)V  
public java.lang.String(byte[], int);  
  Signature: ([BI)V  
public java.lang.String(byte[], int, int, java.lang.String)   throws java.io.UnsupportedEncodingException;  
  Signature: ([BIILjava/lang/String;)V  
public java.lang.String(byte[], int, int, java.nio.charset.Charset);  
  Signature: ([BIILjava/nio/charset/Charset;)V  
public java.lang.String(byte[], java.lang.String)   throws java.io.UnsupportedEncodingException;  
  Signature: ([BLjava/lang/String;)V  
public java.lang.String(byte[], java.nio.charset.Charset);  
  Signature: ([BLjava/nio/charset/Charset;)V  
public java.lang.String(byte[], int, int);  
  Signature: ([BII)V  
...  
```

### 自定义类

```java
package com.demo;  
 public class SigTest {  
     public static final String name = null;  
     public int getName(int[] data,long index) {  
         return 0;  
     }  
 }  
```

- 输出

```java
$ javac SigTest.java 
$ javap -s -p com.demo.SigTest
Compiled from "SigTest.java"
public class com.demo.SigTest extends java.lang.Object{
public static final java.lang.String name;
  Signature: Ljava/lang/String;
public com.demo.SigTest();
  Signature: ()V
public int getName(int[], long);
  Signature: ([IJ)I
static {};
  Signature: ()V
}
```

- -s 表示打印签名信息

- -p 表示打印所有函数和成员的签名信息，默认只打印public的签名信息

