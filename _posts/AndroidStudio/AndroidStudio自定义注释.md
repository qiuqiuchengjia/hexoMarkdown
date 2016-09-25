---
title: AndroidStudio自定义注释
date: 2016-08-22 14:02:01
tags: AndroidStudio
categories: AndroidStudio
---

## 设置类创建时自动生成头部注释

- 比如每次创建一个类自动在头部生成一个这样的头部,作为一个类的说明信息

<center>![](http://o99dg8ap9.bkt.clouddn.com/AndroidStudio%E8%87%AA%E5%AE%9A%E4%B9%89%E6%B3%A8%E9%87%8A1.png)</center>

- 设置方法

点击设置—>Editor–>File and code Templates –>Includes—>File Header
代码：其中时间是自动获取的

```java
/**
 * Description: 
 * Data：${DATE}-${TIME}
 * Blog：www.qiuchengjia.cn
 * Author: qiu
 */
```

- 效果和步骤

<center>![](http://o99dg8ap9.bkt.clouddn.com/AndroidStudio%E8%87%AA%E5%AE%9A%E4%B9%89%E6%B3%A8%E9%87%8A3.gif)</center>
 
<!-- more -->

## 设置快速生成方法注释的两种方式

### 在studio 中自定义模板

- 在studio中自定义注释模板有一定的局限性，目前已知的studio的模板只能获取的时间，并不能获取返回值以及参数等信息，那是因为获取方法名的方法运行在方法内部才会生效,运行在方法外部是不能生效.
所以在方法外部用studio自定义模板的方式有一定的局限性


- 模板

```java
/**
 * Description:$Method$
 * Blog: www.qiuchengjia.cn
 * Data: $Date$ $Time$
 * @author: qiu
 */
```

- 点击设置—>Editor–>live Templates –>点击+号 先创建1个组 再创建一个模板
编辑注释内容–>声明作用域

<center>![](http://o99dg8ap9.bkt.clouddn.com/AndroidStudio%E8%87%AA%E5%AE%9A%E4%B9%89%E6%B3%A8%E9%87%8A2.gif)</center>

### javaDoc自动生成注释

- 如果我们想获取到方法名，参数返回值的信息，想让这些信息全部自动生成的注释里面的话，我们可以借助插件JavaDoc实现，安装插件javaDoc 安装完之后重启studio

#### 效果

<center>![](http://o99dg8ap9.bkt.clouddn.com/AndroidStudio%E8%87%AA%E5%AE%9A%E4%B9%89%E6%B3%A8%E9%87%8A4.png)</center>

#### 方法配置

- 我这儿拿method的来举例。我给每个方法的注解加 一个 作者以及时间


- __@author qiu 博客：www.qiuchengjia.cn 时间：${.now?string["yyyy-MM-dd"]}\n__

- __原始配置__

```html
/**\n
 * ${name}<#if isNotVoid> ${return}</#if>.\n
<#if element.typeParameters?has_content>         * \n
</#if>
<#list element.typeParameters as typeParameter>
         * @param <${typeParameter.name}> the type parameter\n
</#list>
<#if element.parameterList.parameters?has_content>
         *\n
</#if>
<#list element.parameterList.parameters as parameter>
         * @param ${parameter.name} the ${paramNames[parameter.name]}\n
</#list>
<#if isNotVoid>
         *\n
         * @return the ${return}\n
</#if>
<#if element.throwsList.referenceElements?has_content>
         *\n
</#if>
<#list element.throwsList.referenceElements as exception>
         * @throws ${exception.referenceName} the ${exceptionNames[exception.referenceName]}\n
</#list>
 */
```

- __修改之后的配置__

```html
/**\n
<#if element.typeParameters?has_content>         * \n
</#if>
<#list element.typeParameters as typeParameter>
         * @param <${typeParameter.name}> the type parameter\n
</#list>
<#if element.parameterList.parameters?has_content>
         *\n
</#if>
<#list element.parameterList.parameters as parameter>
         * @param ${parameter.name} the ${paramNames[parameter.name]}\n
</#list>
<#if isNotVoid>
         *\n
         * @return the ${return}\n
</#if>
<#if element.throwsList.referenceElements?has_content>
         *\n
</#if>
<#list element.throwsList.referenceElements as exception>
         * @throws ${exception.referenceName} the ${exceptionNames[exception.referenceName]}\n
</#list>
         
	\n * @author qiu\n
\n *  博客：www.qiuchengjia.cn   时间：${.now?string["yyyy-MM-dd"]}\n
    
 */
```

#### 构造函数配置

- 原始配置

```html
/**\n
 * Instantiates a new ${name}.\n
<#if element.parameterList.parameters?has_content>
         *\n
</#if>
<#list element.parameterList.parameters as parameter>
         * @param ${parameter.name} the ${paramNames[parameter.name]}\n
</#list>
<#if element.throwsList.referenceElements?has_content>
         *\n
</#if>
<#list element.throwsList.referenceElements as exception>
         * @throws ${exception.referenceName} the ${exceptionNames[exception.referenceName]}\n
</#list>
 */
```
 
- 修改后的配置

```html
/**\n
<#if element.parameterList.parameters?has_content>
         *\n
</#if>
<#list element.parameterList.parameters as parameter>
         * @param ${parameter.name} the ${paramNames[parameter.name]}\n
</#list>
<#if element.throwsList.referenceElements?has_content>
         *\n
</#if>
<#list element.throwsList.referenceElements as exception>
         * @throws ${exception.referenceName} the ${exceptionNames[exception.referenceName]}\n
</#list>
\n * @author qiu\n
\n *  博客：www.qiuchengjia.cn   时间：${.now?string["yyyy-MM-dd"]}\n
 */
```

#### 使用

- 把鼠标移动到方法中，然后shift + alt + G

- 如果是对这个类所有的方法都进行注释，就是shift + ctrl + alt + G

- 也可以通过alt+insert来进行选择

- shift + alt + Z是撤销当前/选择

- shift + ctrl + alt + Z是撤销所有注释

#### 扩展

- 如果你对JavaDoc生成的模板如果还不满意，你还可以修改JavaDoc的模板，具体修改位置是 设置–>other—>javadoc
里面有对应的模板，模板语言使用的是一种 freemarker的标记语言，如果有感兴趣的同学可自己开发扩展；

- [javadoc网址](https://github.com/setial/intellij-javadocs/wiki)

## 参考资料

- [ Android studio JavaDoc的使用](http://blog.csdn.net/dreamlivemeng/article/details/51499675)

- [(原创) Android Studio 自定义注释&快速输入代码片段](http://www.codingnote.net/2016/05/12/Android-Studio-%E8%87%AA%E5%AE%9A%E4%B9%89%E6%B3%A8%E9%87%8A-%E5%BF%AB%E9%80%9F%E8%BE%93%E5%85%A5%E4%BB%A3%E7%A0%81%E7%89%87%E6%AE%B5/)