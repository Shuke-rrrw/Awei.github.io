---
layout: post
title: iiwa世界坐标｜解决iiwa世界坐标错误的问题
categories: [笔记,iiwa]
description: 解决iiwa世界坐标错误的问题
keywords: iiwa,世界坐标
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

### 遇到问题描述

iiwa机械臂重新安装回基座后，手动模式下世界坐标发生改变，但在程序下是正常的。

### 故障原因
手动模式下异常是因为之前自己尝试修复机械臂时不小心修改了stationsetup.cat当中基座安装的坐标旋转角度（具体怎么修改在iiwa ROS包的readm文件有写），但是又因为之前机械臂是坏掉的，所以没发现问题。

自动模式下坐标正常是因为程序中用的是给予初始世界坐标的坐标变换（之前的师兄写的，具体在哪里操作的尚未知），所以不受影响。

### 解决方案

参照gitlab中的readme文件。



### 参考


