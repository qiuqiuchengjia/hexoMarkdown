---
title: Android Broadcast广播机制分析
date: 2016-09-11 19:06:46
tags: Android
categories: Android
---

> 基于Android 6.0的源码剖析， 分析android广播的发送与接收流程

## 概述

广播(Broadcast)机制用于进程/线程间通信，广播分为广播发送和广播接收两个过程，其中广播接收者BroadcastReceiver便是Android四大组件之一

__BroadcastReceiver分为两类：__

- 静态广播接收者：通过AndroidManifest.xml的标签来申明的BroadcastReceiver

- 动态广播接收者：通过AMS.registerReceiver()方式注册的BroadcastReceiver，动态注册更为灵活，可在不需要时通过unregisterReceiver()取消注册



__从广播发送方式可分为三类：__

- 普通广播：通过Context.sendBroadcast()发送，可并行处理

- 有序广播：通过Context.sendOrderedBroadcast()发送，串行处理

- Sticky广播：通过Context.sendStickyBroadcast()发送

## 注册广播

###  registerReceiver



<!-- more -->