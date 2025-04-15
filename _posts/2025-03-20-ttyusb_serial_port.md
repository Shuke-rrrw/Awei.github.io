---
layout: post
title: 串口通信｜解决必须获取root权限才能进行串口通信的问题
categories: [笔记,串口]
description: 必须获取root权限才能使用串口通信，否则运行ros2 run hd_servo hd_servo命令时机械爪无响应
keywords: 串口
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

### 遇到问题描述

在运行命令：
```objc
ros2 run hd_servo hd_servo
```
时机械爪无响应。但是在前面加上sudo给予root权限夹爪就有响应：
```objc
sudo ros2 run hd_servo hd_servo
```
但是引入root权限一是麻烦，而是会引入其他一些问题...

### 故障原因
这个节点是初始化六自由度二指夹爪的，夹爪和主机之间通过串口通信，查了一下，Linux下的设备使用串口都需要使用sudo或root用户才能打开，所以以普通用户执行命令的时候串口无法通信，夹爪自然就没有反应。

### 解决方案

#### 方法一.增加udev规则
使用以下命令打开70-ttyusb.rules文件：
```objc
sudo gedit /etc/udev/rules.d/70-ttyusb.rules
```
在末尾增加以下内容：
```objc
KERNEL=="ttyUSB[0-9]*",MODE="0666"
```
保存后插拔一下设备即可。

##### 原理：

这个解决方法的原理是通过 ​udev 规则动态修改设备文件的访问权限，使普通用户无需 sudo 即可访问串口设备。具体机制如下：

1. ​udev 规则触发条件
规则中的 KERNEL=="ttyUSB[0-9]*" 表示匹配所有内核识别的 USB 转串口设备（如 /dev/ttyUSB0、/dev/ttyUSB1 等）。
当设备插入时，内核会通过 netlink 套接字发送 uevent 事件，触发 udev 的规则匹配流程。
2. ​权限动态修改
MODE="0666" 的作用是设置设备文件的权限为 ​所有用户可读写​（rw-rw-rw-）。原本串口设备的默认权限可能为 rw-r-----（仅 root 用户和 dialout 组可访问）。
通过 udev 规则，设备文件在创建时即被赋予宽松的权限，绕过了默认的权限限制。
3. ​规则生效流程
规则文件保存在 /etc/udev/rules.d/ 目录下，文件名前缀的数字（如 70-）决定了规则的优先级。
当设备插入后，udev 会扫描所有规则文件，匹配到 ttyUSB 设备时，立即执行 MODE="0666" 操作。
重新插入设备时，规则生效，权限被更新，普通用户可直接访问 /dev/ttyUSB* 设备。

#### 方法二.给予权限

查看串口：
```objc
ls /dev/ttyUSB*
```
临时给予串口一次读写权限：

```objc
sudo chmod 777 /dev/ttyUSB0
```

永久给予权限：

```objc
ls -l /dev/ttyUSB0
```
如果出现如下提示：
```objc
crw-rw-rw- 1 root dialout 188, 0 12月  5 13:05 /dev/ttyUSB0
```
说明属于dialout用户组，只有root有权限。

查询当前用户：

```objc
whoami#输出为用户xxxx
```
将当前用户加入串口用户组：
```objc
sudo usermod -aG dialout xxxxxx
```
重启生效。

最终使用方法一解决问题
### 参考


