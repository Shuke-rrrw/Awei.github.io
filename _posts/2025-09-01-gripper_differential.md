---
layout: post
title: gripper｜launch节点同时开启差速器和二指夹爪时二指夹爪姿态异常问题记录
categories: [问题记录]
description: launch节点同时开启差速器和二指夹爪时二指夹爪姿态异常问题记录
keywords: 二指夹爪,差速器,fabricunfolding
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

### 遇到问题描述

在我的launch节点中，我同时写了开启差速器和二指夹爪，运行launch节点时，右臂的二指夹爪初始化姿态就会出现异常，但是如果我将差速器的节点放在另一个launch节点，不与二指夹爪的节点同时启动就不会出现这种情况。

原因未知。

