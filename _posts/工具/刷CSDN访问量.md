---
title: 刷CSDN访问量
date: 2016-10-23 16:18:50
tags:
categories:
---

## PHP刷

- 搭建PHP运行环境

- 然后写下如下PHP代码，假设保存为csdn.php：

```php
<?php
header("Content-Type: text/html;charset=utf-8");

$url = array();
$url[0] = "http://blog.csdn.net/qiuchengjia/article/details/52901693";

while(true) {
    // 随机文章
    $rand = rand(0,count($url) - 1);
    // 初始化
    $ch = curl_init();
    //设置选项，包括URL
    curl_setopt($ch, CURLOPT_URL, $url[$rand]);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
    curl_setopt($ch, CURLOPT_HEADER, 0);
    //执行并获取HTML文档内容
    $output = curl_exec($ch);
    //释放curl句柄
    curl_close($ch);
    //打印获得的数据
    // print_r($output);
    // echo $output;
    // 延迟10秒
    usleep(10000000);
}
 ?>
 ```

- 最后在终端运行，或者浏览器打开这个PHP脚本

```java
$ php csdn.php &
```

- 然后终端会出现一个进程的PID，假如是9999，如果不想刷了，kill掉它就行了

```java
$ kill 9999
```

- 如果找不到PID，可以用命令来找

```java
$ ps aux | grep php
```

- 至于浏览器的，关闭浏览器就行了

<!-- more -->

## exe工具

- 直接运行就行

- [传送门](http://oe7guoyyy.bkt.clouddn.com/csdn.exe)

- [博客轰炸机](http://o9fnxzb1g.bkt.clouddn.com/%E5%8D%9A%E5%AE%A2%E8%BD%B0%E7%82%B8%E6%9C%BA3.0.exe)

- [博客轰炸机_百度云](https://pan.baidu.com/s/1nvnmY61)

## 禁用浏览器的Cookie

- 通过禁用浏览器的cookie，然后就可以实现刷新之后浏览量加1

- 然后再使用自动刷新插件就可以刷了


## 参考博客

- [如何刷CSDN访问量](https://github.com/fengyuanzemin/blog-markdown/blob/master/%E5%A6%82%E4%BD%95%E5%88%B7CSDN%E8%AE%BF%E9%97%AE%E9%87%8F.md)

- [博客轰炸机](http://www.jtahstu.com/blog/post-14.html)

- [论如何一天时间进CSDN博客排名前500](http://www.jtahstu.com/blog/post-71.html)