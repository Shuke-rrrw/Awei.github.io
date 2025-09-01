---
layout: post
title: ginkgo｜解决ginkgo双通道数据获取异常的问题
categories: [笔记]
description: 解决ginkgo双通道数据获取异常的问题
keywords: ginkgo,u_skin,fabricunfolding
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

### 遇到问题描述

在做织物展开实验时，我需要读取两个u_skin触觉传感器的数据，我将这两个传感器分别连接在了ginkgo适配器的两个通道，当我只单独运行一个触觉传感器的节点时，数据可以正常读取，但当我同时运行两个触觉传感器的节点时，前一个开启的节点就会卡住而无法获取数据。
### 原因

核心原因在于以下代码重复打开了适配器：
```
DevNum = VCI_ScanDevice(1);  // 扫描所有设备

Status = VCI_OpenDevice(VCI_USBCAN2, devIndex, 0); // 打开整个适配器

VCI_ReadBoardInfoEx(0, &CAN_BoardInfo); // 获取设备全局信息

VCI_CloseDevice(VCI_USBCAN2, devIndex);
```

这些代码的操作对象都是整个适配器，而我为了简便，直接把一个触觉传感器的节点代码复制一下改了个canID就得到了另一个触觉传感器的节点，这样就会导致上述代码会被运行两次，从而导致问题的出现，这也解释了为什么单独运行每一个触觉传感器的代码都是可行的，但是同时运行就会出现问题。

### 解决方案

既然问题是因为针对适配器的操作代码被执行了两次，那就把两个触觉传感器节点的代码整合到一起，在新整合的节点中，只打开了一次适配器，然后分别打开适配器的两个通道can0和can1，最后问题得到解决，实现了同时读取两个触觉传感器的数据。

### 补充
除了在软件商解决问题，其实最简单的就是从硬件上解决问题，即直接用两个gingo适配器，这样就不用华大领时间去整合代码，直接换个devID就可以。但我这样尝试以后还是不行，甚至单独只开一个节点都读取不了数据了，使用命令查看usb接口信息发现系统识别到了两个gingo适配器，但我发现他们接口id好像是一样的，问题可能在这里，几番尝试无果后使用了上述软件层面的方法解决了问题，后续如果有时间再来具体研究下这是怎么回事。