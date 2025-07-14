---
layout: post
title: u_skin｜解决u_skin数据获取异常的问题
categories: [问题记录]
description: 解决u_skin数据获取异常的问题
keywords: u_skin,fabricunfolding
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

### 遇到问题描述

在做织物展开实验时，u_skin触觉传感器总是出现一段时间后数据突然跳变到一个很大的值的现象，如下图所示
![](/images/posts/uskin.png)

### 原因

在使用u_skin力传感器的时候，需要开启forcesensor_can和forcesensor_client两个节点，为了方便我写了一个launch文件一次性启动这两个节点，然后问题出现了。问题出现的机理尚不清楚。

### 解决方案

等待forcesensor_can开启后再启动forcesensor_client。