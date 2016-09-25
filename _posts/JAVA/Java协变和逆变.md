---
title: Java协变和逆变
date: 2016-08-16 20:49:41
tags: JAVA
categories: JAVA
---

## 什么是协变和逆变

- 我们将围绕如下三个核心名词：协变性（covariance）、逆变性(contravariance)和无关性（invariant）。他们都是用来描述类型转换的性质的术语

- 到底什么是协变和逆变？先看例子：

```java
//Java
Object[] objects = new String[2];
//C# 
object[] objects = new string[2];
```

- 这就是协变，C#和Java都是支持数组协变的语言，好像说了等于没说，别急，慢慢来

我们都知道C#和Java中String类型都是继承自Object的，姑且记做__String ≦ Object__，表示String是Object的子类型，String的对象可以赋给Object的对象

而Object的数组类型Object[]，我们可以理解成是由Object构造出来的一种新的类型,可以认为是一种__构造类型__，记f(Object)（可以类比下初中数学中函数的定义），那么我们可以这么来描述协变和逆变：

- 当A ≦ B时,如果有f(A) ≦ f(B),那么f叫做协变(covariance)；

- 当A ≦ B时,如果有f(B) ≦ f(A),那么f叫做逆变(contravariance)；

- 如果上面两种关系都不成立则叫做不可变(invariant)

### __协变__

其实顾名思义，协变和逆变表示的一种类型转变的关系：“构造类型”之间相对“子类型”之间的一种关系。只不过平时我（可能也包括大家）被网上的一些文章搞糊涂了。“协”表示一种自然而然的转换关系，比如上面的String[] ≦ Object[]，这就是大家学习面向对象编程语言中经常说的：

> 子类变量能赋给父类变量，父类变量不能赋值给子类变量

### __逆变__

- 而“逆”则不那么直观，平时用的也很少，后面讲__Java泛型中的协变和逆变__会看到例子


<!-- more -->

### __不可变__

- 不可变的例子就很多了，比如Java中List< Object >和List< String >之间就是不可变的

```java
List<String> list1 = new ArrayList<String>();
List<Object> list2 = list1;
```

- 这两行代码在Java中肯定是编译不过的，反过来更不可能，C#中也是一样

### 作用

- 那么__协变__和__逆变__作用：主要是语言设计的一种考量，目的是为了增加语言的灵活性和能力


## 里氏替换原则

- 再说下面内容之前，提下这个大家都知道的原则：

> 有使用父类型对象的地方都可以换成子类型对象

- 假设有类Fruit和Apple,Apple ≦ Fruit，Fruit类有一个方法fun1，返回一个Object对象:

```java
public Object fun1() {
            return null;
}
Fruit f = new Fruit();
//...
//某地方用到了f对象
Object obj = f.fun1();
```

- 那么现在Aplle对象覆盖fun1，假设可以返回一个String对象：

```java
@Override
public String fun1() {
    return "";
}
Fruit f = new Apple();
//...
//某地方用到了f对象
Object obj = f.fun1();
```

- 那么任何使用Fruit对象的地方都能替换成Apple对象吗？显然是可以的

- 举得例子是返回值，如果是方法参数呢？调用父类方法fun2(String)的地方肯定可以被一个能够接受更宽类型的方法替代：fun2(Object)......

## 协变返回值

- 在面向对象语言中，一个协变返回值方法是一个在子类覆盖该方法的时候，方法的返回值可以被一个“更窄”的类型所替代（C#并不支持这个技术，C++和Java JDK5.0后开始支持）

```java
public static class Super { 
      Object getSomething() { 
      return null; 
   } 
} 
public static class SubClass extends Super {
@Override
     String getSomething() {
           return null;
     }
}
```

- 虽然Java是面向对象的语言，但某种程度上支持__返回值协变__，Java子类覆盖父类方法的时候能够返回一个“更窄”的子类型，所以说Java是一门可以支持__返回值协变__的语言


## 参数逆变

- 类似__参数逆变__是指子类覆盖父类方法时接受一个“更宽”的父类型。在Java和C#中这都被当作了__方法重载__

```java
class Super { 
          void doSomething(String parameter) { 
          } 
} 
 
class Sub extends Super { 
        void doSomething(Object parameter) { 
         } 
} 
```

## Java泛型中的协变和逆变

- 一般我们看Java泛型好像是不支持协变或逆变的，__List< Object>__和__List< String>__之间是不可变的。但当我们在Java泛型中引入通配符这个概念的时候，Java 其实是支持协变和逆变的

```java
// 不可变
List<Fruit> fruits = new ArrayList<Apple>();// 编译不通过
// 协变
List<? extends Fruit> wildcardFruits = new ArrayList<Apple>();
// 协变->方法的返回值，对返回类型是协变的:Fruit->Apple
Fruit fruit = wildcardFruits.get(0);
// 不可变
List<Apple> apples = new ArrayList<Fruit>();// 编译不通过
// 逆变
List<? super Apple> wildcardApples = new ArrayList<Fruit>();
// 逆变->方法的参数，对输入类型是逆变的:Apple->Fruit
wildcardApples.add(new Apple());
```

- 可见在Java泛型中通过__extends__关键字可以提供协变的泛型类型转换，通过__supper__可以提供逆变的泛型类型转换

- 关于Java泛型中__supper__和__extends__关键字的作用网上有很多文章，这里不再赘述。只举一个《Java Core》里面__supper__使用的例子：下面的代码能够对实现__Comparable__接口的对象数组求最小值

```java
public static <T extends Comparable<T>> T min(T[] a) {
    if (a == null || a.length == 0) {
            return null;
    }
    T t = a[0];
    for (int i = 1; i < a.length; i++) {
        if (t.compareTo(a[i]) > 0) {
            t = a[i];
        }
    }
    return t;
}
```

- 这段代码对__Calendar__类是运行正常的，但对__GregorianCalendar__类则无法编译通过：

```java
Calendar[] calendars = new Calendar[2];
Calendar ret3 = CovariantAndContravariant.<Calendar> min(calendars);
GregorianCalendar[] calendars2 = new GregorianCalendar[2];
GregorianCalendar ret2 = CovariantAndContravariant.<GregorianCalendar> min(calendars2);//编译不通过
```

- 如果想工作正常需要将方法签名修改为： 

```java
public static <T extends Comparable<? super T>> T min(T[] a)
```

- 至于原因，大家看下源码和网上大量关于supper的作用应该就明白了，我这里希望能够给看了上面内容的同学提供另外一个思路......


## 参考资料

- [再谈对协变和逆变的理解](https://www.zybuluo.com/zhanjindong/note/34147)

- [Java 协变性 逆变性 学习笔记](http://www.2cto.com/kf/201304/205042.html)