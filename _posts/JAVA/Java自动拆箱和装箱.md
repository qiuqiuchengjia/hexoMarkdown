---
title: Java自动拆箱和装箱
date: 2016-08-11 14:24:02
tags: JAVA
categories: JAVA
---

<center>![](http://o99dg8ap9.bkt.clouddn.com/Java%E8%87%AA%E5%8A%A8%E6%8B%86%E7%AE%B1%E5%92%8C%E8%A3%85%E7%AE%B1.png)</center>

## 什么是自动拆箱和装箱

### 定义

- 自动装箱就是 Java 自动将原始数据类型转为对应的包装类对象 比如将 int 型的变量转成 Integer对象 自动拆箱反之(从 Java 1.5 开始引入)

### 过程

- 自动装箱时，编译器调用 valueOf() 将原始数据类型值转为对象；同时自动拆箱时，编译器调用类似 intValue(), doubleValue() 这类方法将对象转换成原始类型值


| 基本类型| 大小    | 数值范围| 默认值  | 包装类型  |
|---------|:--------|:--------|:--------|:----------|
| boolean|  ^	| true,false| false| Boolean
| byte| 8bit	| -2^7 -- 2^7-1| 0	| Byte
| char| 16bit	| \u0000 -- \uffff| \u0000| Character
| short| 16bit| -2^15 -- 2^15-1| 0	| Short
| int | 32bit	| -2^31 -- 2^31-1| 0	| Integer
| long| 64bit	| -2^63 -- 2^63-1| 0	| Long
| float | 32bit| IEEE 754| 0.0f| 	Float
| double | 64bit| IEEE 754| 0.0d| 	Double
| void| ^| 	    ^	| ^	| Void

### 基本类型与装箱基本类型的区别

- 基本类型只有值，而装箱基本类型则具有与它们的值不同的同一性。换句话说，对装箱基本类型运用 == 操作符几乎总是错的

- 基本类型只有功能完备的值，而装箱基本类型除了它对应基本类型的所有功能值外，还有一个非功能值 null，这导致了对于包装基本类型进行拆箱操作后所进行的操作存在 NPE 的风险

- 基本类型更加省空间和时间，如果对包装类型进行频繁的装箱和拆箱操作会影响性能

<!-- more -->

## 何时发生自动装箱与拆箱？

### 赋值时

- 在Java1.5之前，需要手动地进行类型转换，而现在所有的转换都是有编译器来完成

```java
//before autoboxing
Integer iObject = Integer.valueOf(3);
int iPrimitive = iObject.intValue()
 
//after java5
Integer iObject = 3;      //autobxing - primitive to wrapper conversion
int iPrimitive = iObject; //unboxing - object to primitive conversion
```

### 方法调用时

- 当在进行方法调用时，可以传入原始数据值或对象，编译器同样会自动进行转换

```java
public static Integer show(Integer iParam){
   System.out.println("autoboxing example - method invocation i: " + iParam);
   return iParam;
}
 
//autoboxing and unboxing in method invocation
show(3); //autoboxing
int result = show(3); //unboxing because return type of method is Integer
```

## 自动装箱引起的性能问题

- 如果有人告诉你：“只要修改一个字符，下面这段代码的运行速度就能提高5倍。”，你觉得可能么？

```java
long t = System.currentTimeMillis();
Long sum = 0L;
for (long i = 0; i < Integer.MAX_VALUE; i++) {
    sum += i;
}
System.out.println("total:" + sum);
System.out.println("processing time: " + (System.currentTimeMillis() - t) + " ms");
```

- 输出结果：

> total:2305843005992468481
processing time: 63556 ms

- 将Long修改为long，再来看一下运行结果

```java
long t = System.currentTimeMillis();
long sum = 0L;
for (long i = 0; i < Integer.MAX_VALUE; i++) {
    sum += i;
}
System.out.println("total:" + sum);
System.out.println("processing time: " + (System.currentTimeMillis() - t) + " ms");
```

- 输出结果：

> total:2305843005992468481
processing time: 12229 ms

- 事实证明，仅仅修改了一个字符，性能提高了不止一倍两倍。那，就究竟是什么原因导致的呢？
因为，+这个操作符不适用Integer对象，在进行数值相加操作之前会发生自动拆箱操作，转换成int，相加之后还会发生自动拆箱操作，装换成Integer对象。其内部变化如下：

```java
sum = sum.longValue() + i;
Long sum = new Long(sum);
```

- 很明显，在上面的循环中会创建2147483647个”Long“类型实例，在这样庞大的循环中，会降低程序的性能并且加重了垃圾回收的工作量

> __说明：包含在包装器中的内容不会改变。即Long对象是不可变的__


## 重载与自动装箱

- 在java 5之前，value(int)和value(Integer)是完全不相同的方法，开发者不会因为传入是int还是Integer调用哪个方法困惑，但是由于自动装箱和拆箱的引入，处理重载方法时会不会有什么变化呢？可通过下面一个例子进行探讨：

```java
public void test(int num){
    System.out.println("method with primitive argument");
}
public void test(Integer num){
    System.out.println("method with wrapper argument");
}
//calling overloaded method
AutoboxingTest autoTest = new AutoboxingTest();
int value = 3;
autoTest.test(value);  //no autoboxing 
Integer iValue = value;
autoTest.test(iValue); //no autoboxing
```

- 输出结果

> method with primitive argument
method with wrapper argument


- 从输出结果可以看出，在重载的情况下，不会发生自动装箱操作

## 注意事项

- 自动装箱与拆箱在编程过程中给我们带来了极大的方便，但也存在一些容易让人出错的问题

### 对象相等比较

- "=="既可用于原始值的比较，也可用于对象间的比较。当进行对象间的比较时，实质上比较的是对象的引用是否相等，而不是比较对象代表的值。如果要比较对象的值，应当使用对象对应的equals方法。可通过以下例子进行探讨：

```java
public class AutoboxingTest {
    public static void main(String args[]) {
        // Example 1: == comparison pure primitive – no autoboxing
        int i1 = 1;
        int i2 = 1;
        System.out.println("i1==i2 : " + (i1 == i2)); // true
 
        // Example 2: equality operator mixing object and primitive
        Integer num1 = 1; // autoboxing
        int num2 = 1;
        System.out.println("num1 == num2 : " + (num1 == num2)); // true
 
        // Example 3: special case - arises due to autoboxing in Java
        Integer obj1 = 1; // autoboxing will call Integer.valueOf()
        Integer obj2 = 1; // same call to Integer.valueOf() will return same cached Object
        System.out.println("obj1 == obj2 : " + (obj1 == obj2)); // true
 
        // Example 4: equality operator - pure object comparison
        Integer one = new Integer(1); // no autoboxing
        Integer anotherOne = new Integer(1);
        System.out.println("one == anotherOne : " + (one == anotherOne)); // false
    }
}
```

- 输出结果

> i1==i2 : true
num1 == num2 : true
obj1 == obj2 : true
one == anotherOne : false

- 值得注意的是，在Example 2中，比较是一个对象和一个原始值，出现这种情况比较的应该是对象的值

- 让人感到困惑的Example 3，在一开始我们说过，"=="用于对象间的比较时，比较的是它们的引用，那么为什么obj1 == obj2返回的结果却是true？__这是一种极端情况，处于节省内存的考虑，JVM会缓存-128到127的Integer对象__。也就是说，在创建obj1对象时，会进行自动装箱操作，并且将其对象保存至缓存中，在创建obj2对象时，同样会进行自动装箱操作，然后在缓存中查找是否有相同值的对象，如果有，那么obj2对象就会指向obj1对象。obj1和obj2实际上是同一个对象。所以使用"=="比较返回true

- 而Example 4，是通过使用构造器来创建对象的，而没有发生自动装箱操作，不会执行缓存策略，故one和anotherOne是指向不同的引用的

>　__说明：这种 Integer 缓存策略仅在自动装箱（autoboxing）的时候有用，使用构造器创建的 Integer 对象不能被缓存__

### 容易混乱的对象和原始数据值

- 一个很容易犯错的问题，就是忽略对象与原始数据值之间的差异，在进行比较操作时，对象如果没有初始化或者为null，在自动拆箱过程中obj.xxxValue，则会抛出NullPointerException，可通过以下例子进行探讨：

```java
private static Integer count;
//NullPointerException on unboxing
if( count <= 0){
  System.out.println("Count is not started yet");
}
```

### 生成无用对象增加GC压力

- 因为自动装箱会隐式地创建对象，像前面提到的那样，如果在一个循环体中，会创建无用的中间对象，这样会增加GC压力，拉低程序的性能。所以在写循环时一定要注意代码，避免引入不必要的自动装箱操作

## 参考资料

- [Java学习笔记之自动装箱与拆箱](http://gongchuangsu.com/2016/05/25/Java%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/15.Java%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0%E4%B9%8B%E8%87%AA%E5%8A%A8%E8%A3%85%E7%AE%B1%E4%B8%8E%E6%8B%86%E7%AE%B1/)  

- [自动装箱与拆箱](http://ryan-hou.github.io/blog/2016/06/08/zi-dong-zhuang-xiang-yu-chai-xiang/)