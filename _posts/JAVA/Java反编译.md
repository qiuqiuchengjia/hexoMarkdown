---
title: Java反编译
date: 2016-08-11 23:03:35
tags: JAVA
categories: JAVA
---

## 什么是编译

1. 利用编译程序从源语言编写的源程序产生目标程序的过程

2. 用编译程序产生目标程序的动作。 编译就是把高级语言变成计算机可以识别的2进制语言，计算机只认识1和0，编译程序把人们熟悉的语言换成2进制的。 编译程序把一个源程序翻译成目标程序的工作过程分为五个阶段：词法分析；语法分析；语义检查和中间代码生成；代码优化；目标代码生成。主要是进行词法分析和语法分析，又称为源程序分析，分析过程中发现有语法错误，给出提示信息，具体参考 [Javac编译与JIT编译](http://www.qiuchengjia.cn/2016/07/24/JVM/Javac%E7%BC%96%E8%AF%91%E4%B8%8EJIT%E7%BC%96%E8%AF%91/)


## 什么是反编译

- 计算机软件反向工程（Reverse engineering）也称为计算机软件还原工程，是指通过对他人软件的目标程序（可执行程序）进行“逆向分析、研究”工作，以推导出他人的软件产品所使用的思路、原理、结构、算法、处理过程、运行方法等设计要素，某些特定情况下可能推导出源代码。反编译作为自己开发软件时的参考，或者直接用于自己的软件产品中

<!-- more -->

## 反编译的原理

### JVM

- __JVM是什么？__我的理解简单来说是：一个能把Class字节码翻译成本机cpu能够识别的指令的程序

### 流程

> Java源码(.java文件) => 编译器  =>  Class文件 => JVM => 可执行的指令

- 不一定只有Java，例如Scala，Groovy等基于JVM的语言，只要能编译成标准Class的都可以

### Class文件

- 详情参见 [Class类文件结构](http://www.qiuchengjia.cn/2016/07/18/JVM/Class%E7%B1%BB%E6%96%87%E4%BB%B6%E7%BB%93%E6%9E%84/)

- __class文件结构介绍：__ 根据java虚拟机规范的规定，class文件格式采用一种类似c语言结构体的伪结构来存储，这种伪结构中只有两种数据类型：无符号数和表。 无符号数：无符号数属于基本的数据类型，以u1,u2,u4,u8来分别代表1个字节，2个字节，4个字节和8个字节的无符号数，无符号数可以用来描述数字、索引引用、数量值，或者按照utf-8编码构成字符串值。 表：表是由多个无符号数或者其他表作为数据项构成的复合数据类型，所有表都习惯性地以“_info“结尾。表用于描述有层次关系的复合结构的数据，整个class文件本质上就是一张表，它由下列数据项构成：

```java
ClassFile { 
    //魔数(0xCAFEBABE)，每个class文件的前4个字节称为魔数,
    //值为0xCAFEBABE。作用在于轻松的辨别class文件与非class文件。
    u4 magic;
    //下面两个是版本号，随着Java技术的发展，class文件的格式会发生变化。
    //版本号的作用在于使得虚拟机能够认识当前加载class的文件格式。从而准确的提取class文件信息
    u2 minor_version;//次版本号
    u2 major_version;//主版本号  
    u2 constant_pool_count;//常量池容量计数值  
    cp_info constant_pool[constant_pool_count-1];//常量池，具体见对照表  
    //访问标志，用来表明该class文件中定义的是类还是接口，访问
    //修饰符是public还是缺省。类或接口是否是抽象的。类是否是final的。  
    u2 access_flags;
    u2 this_class;//类索引  
    u2 super_class;//父类索引  
    u2 interfaces_count;//接口计数器  
    u2 interfaces[interfaces_count];//接口索引集合  
    u2 fields_count;//字段计数器  
    field_info fields[fields_count];//字段表  
    u2 methods_count;//方法计数器  
    method_info methods[methods_count];//方法表  
    u2 attributes_count;//属性表计数器  
    attribute_info attributes[attributes_count];//属性表集合  
}
```

### 常量对照表

| 常量表类型	| 标志值(占1 byte)| 	描述|
|---------------|:----------------|:--------|
| CONSTANT_Utf8	| 1| 	UTF-8编码的Unicode字符串
| CONSTANT_Integer| 	3| 	int类型的字面值
| CONSTANT_Float| 	4| 	float类型的字面值
| CONSTANT_Long	| 5	| long类型的字面值
| CONSTANT_Double| 	6| 	double类型的字面值
| CONSTANT_Class| 	7	| 对一个类或接口的符号引用
| CONSTANT_String| 	8	| String类型字面值的引用
| CONSTANT_Fieldref	| 9	| 对一个字段的符号引用
| CONSTANT_Methodref| 	10| 	对一个类中方法的符号引用
| CONSTANT_InterfaceMethodref| 	11| 	对一个接口中方法的符号引用
| CONSTANT_NameAndType| 	12| 	对一个字段或方法的部分符号引用

如上面表格所示，每个类型都会有对应的tag值，还有方法权限标志表，描述符表之类的。根据这些tag值来表示，例如常量为01 00 12 4C 6A 61 76 61 2F 6C 61 6E 67 2F 53 74 72 69 6E 67 3B，是int类型呢还是float呢，是public还是private？根据这些对照表来查出对应tag所表示的意义可以看出：

- 01——tag值为1，类型为CONSTANT_Utf8_info；

- 00 12——这个UTF-8编码的常量字符串长度为18；

- 4C 6A 61 76 61 2F 6C 61 6E 67 2F 53 74 72 69 6E 67 3B——18个字节的字符串，对应：Ljava/lang/String(描述符,L 代表是引用类型);

### 反编译原理

- Class按照上面说的tag值和表对照，就能分析出Class对应的Java文件结构，那么即遵循这样的规范反编译成Java文件，当然反编译出来的并非是原本一模一样的Java源码，而是根据分析重新生成的Java代码

## Java类的编译与反编译

- 我们在最初学习Java的时候，会接触到两个命令：javac和java,那个时候我们就知道，javac是用来编译Java类的，就是将我们写好的helloworld.java文件编译成helloworld.class文件

> class文件打破了C或者C++等语言所遵循的传统，使用这些传统语言写的程序通常首先被编译，然后被连接成单独的、专门支持特定硬件平台和操作系统的二进制文件。通常情况下，一个平台上的二进制可执行文件不能在其他平台上工作。而Java class文件是可以运行在任何支持Java虚拟机的硬件平台和操作系统上的二进制文件

- 那么__反编译__呢，就是通过helloworld.class文件得到java文件（或者说是程序员能看懂的Java文件）

## 什么时候会用到反编译

1. 我们只有一个类的class文件，但是我们又看不懂Java的class文件，那么我们可以把它反编译成我们可以看得懂的文件

2. 学习Java过程中，JDK的每个版本都会加入越来越多的语法糖，有些时候我们想知道Java一些实现细节，我们可以借助反编译。

## 反编译工具

1. [javap](http://www.qiuchengjia.cn/2016/08/12/JAVA%E5%91%BD%E4%BB%A4%E5%AD%A6%E4%B9%A0/Java%E5%91%BD%E4%BB%A4-javap/)

2. Jad: [官网](http://varaneckas.com/jad/)

> 这个工具下载好之后会有一个执行文件，只要在执行文件所在目录执行./jad helloworld.class 就会在当前目录下生成helloworld.jad文件，该文件里就是我们很熟悉的Java代码

- __Eclipse插件：__

[传送门](http://jadclipse.sourceforge.net/wiki/index.php/Main_Page) 在官网下载插件的jar包，然后将jar包放到eclipse的plugins目录下‘ 在打开Eclipse，__Eclipse->Window->Preferences->Java__，此时你会发现会比原来多了一个JadClipse的选项，单击，在Path to decompiler中输入你刚才放置jad.exe的位置，也可以制定临时文件的目录。当然在JadClipse下还有一些子选项，如Debug，Directives等，按照默认配置即可。 基本配置完毕后，我们可以查看一下class文件的默认打开方式，__Eclipse->Window->Preferences->General->Editors->File Associations__ 我们可以看到class文件的打开方式有两个，JadClipse和Eclipse自带的Class File Viewer，而JadClipse是默认的。 全部配置完成，下面我们可以查看源码了，选择需要查看的类，按F3即可查看源码

## 参考资料

- [Java的反编译](http://www.hollischuang.com/archives/58)

- [反编译原理浅析](http://www.jianshu.com/p/9e38f648aacd)