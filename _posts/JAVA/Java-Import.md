---
title: Java Import
date: 2016-08-21 14:16:38
tags: JAVA
categories: JAVA
---

- 在 Java 中使用import关键字来导入任意类型到同一个编译单元中

- 在代码中，导入代码应放在包声明代码之后，类型声明代码之前

Java 中有两种类型的导入声明：

1. 单类型导入（Single-type import declaration）

2. 按需类型导入（Import-on-demand declaration）


## 单类型导入

- 单类型导入用于导入指定包中的一个单独的类型（例如一个类）。语法如下：

```java
import <fully qualified name of a type>;
```

- 下面的代码演示了如何从 org.aptusource 包中导入 Dog 类：

```java
import org.aptusource.Dog;
```

- 单类型导入只能导入一个类型，如果要导入多个类型，那么可以使用多个单独的单类型导入语句

- 下面的代码导入了 pkg1 包中的 ClassOne，pkg2 包中的 ClassTwo 和 ClassThree，pkg3 包中的 ClassFour：

```java
import pkg1.ClassOne; 
import pkg2.ClassTwo; 
import pkg2.ClassThree; 
import pkg3.ClassFour;
```

- 下面的代码使用了 Dog 类的全名称

```java
public  class  Main{
   public static  void  main(String[]  args)  {
      org.aptusource.Dog jack;  // Uses  full qualified name for the   Dog  class
   }
}
```

- 下面的代码演示了如何使用单类型导入，我们将导入 org.aptusource.Dog 类，这样就可以使用简单名称来引用 Dog 类


<!-- more -->

更改后的 Main类如下

```java
import org.aptusource.Dog; // 导入 Dog 类

public  class  Main {
   public static  void  main(String[]  args)  {
      Dog  jack; // 使用 Dog 类的简单名称
   }
}
```

- 当编译器遇到简单名称 Dog 所在的代码段时，比如：

```java
Dog  jack;
```

- 编译器将会查找所有的导入声明来将简单名称转换为全名称

- 像上面的例子中，编译器会将代码段替换为：

```java
org.aptusource.Dog  jack;
```

- 导入声明可以让你在代码中使用简单名称，增加了代码的可读性


## 按需导入

- 按需导入可以使用一行导入代码来导入多个类型

- 按需导入的语法如下：

```java
import <package name>.*;
```

- 上面的语句可以看到，包名后直接跟了一个点号和一个星号（*）

- 例如，下面的代码导入了 org.aptusource 包中的所有类型：

```java
import org.aptusource.*;
```

上面的 Main 类可以使用按需导入来重写：

```java
import org.aptusource.*;
public  class  Main {
    public static  void  main(String[]  args)  {
        Dog  jack; // 使用 Dog 类的简单名称
    }
}
```

## 静态导入

### 使用说明

- 静态导入是JDK5.0引入的新特性

- 要使用静态成员（方法和变量）我们必须给出提供这个静态成员的类,使用静态导入可以使被导入类的静态变量和静态方法在当前类直接可见，使用这些静态成员无需再给出他们的类名

### Demo

- 比如先在一个包中定义一个这样的类：

```java
package com.example.learnjava;

public class Common
{

    public static final int AGE = 10;
    public static void output()
    {
        System.out.println("Hello World!");
    }
}
```

#### __使用一般导入__

- 在另一个包中使用时，如果不用静态导入，是这样用的： 
前面加入了导入语句，将Common类导入，使用其中的静态成员变量和静态方法时需要加上类名

```java
package com.example.learnjava2;

import com.example.learnjava.Common;

public class StaticImportTest
{
    public static void main(String[] args)
    {
        int a = Common.AGE;
        System.out.println(a);

        Common.output();
    }

}
```

#### __使用静态导入__

静态导入的语法是： 

- import static 包名.类名.静态成员变量; 
- import static 包名.类名.静态成员函数; 

> __注意导入的是成员变量和方法名__

- 如前面的程序使用静态导入后：

```java
package com.example.learnjava2;

import static com.example.learnjava.Common.AGE;
import static com.example.learnjava.Common.output;

public class StaticImportTest
{
    public static void main(String[] args)
    {
        int a = AGE;
        System.out.println(a);

        output();
    }

}
```

### 优点

- 减少字符输入量，提高代码的可阅读性，以便更好地理解程序

- 举一个例子来说：

```java
import static java.lang.Math.PI;
public class MathUtils{
    // 计算圆面积
    public static double calCircleArea(double r){
        return PI * r * r;
    }
    // 计算球面积
    public static double calBallArea(double r){
        return 4 * PI * r * r;
    }
}
```

### 缺点

- 滥用静态导入会使程序更难阅读，更难维护。静态导入后，代码中就不用再写类名了，但是我们知道类是“一类事物的描述”，缺少了类名的修饰，静态属性和静态方法的表象意义可以被无限放大，这会让阅读者很难弄清楚其属性或方法代表何意，甚至是哪一个类的属性（方法）都要思考一番，特别是在一个类中有多个静态导入语句时，若还使用了*通配符，把一个类的所有静态元素都导入进来了，那简直就是恶梦

## 参考资料

- [Java静态导入](http://blog.csdn.net/houhow/article/details/51567471)

- [Java import](http://codecloud.net/7299.html)


