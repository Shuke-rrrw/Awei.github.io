---
layout: post
title: iiwa｜布料展开实验中改变右臂旋转半径的方法记录
categories: [笔记,iiwa]
description: 布料展开实验中改变右臂旋转半径的方法记录
keywords: iiwa
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

布料展开实验中，在进行第一次边缘跟踪之前，robotiq夹爪关闭后右臂会进行旋转，由于右臂夹布时末端低于左臂末端，所以代码中右臂会一边旋转一边升高位置以达到和左臂末端相近的高度。有时又需要右臂末端在旋转时保持高度不变，这一部分功能修改方法如下：

打开/iiwa_move/src/move_right_arm_sub_1.cpp,找到第89行代码：
```sh
theta += delta_v_twist/rate_param;
```

注释掉这行代码机械臂将在旋转时不改变高度，反之机械臂将在旋转时改变高度。

### 参考


