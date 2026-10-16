---
layout: post
title: 实验｜布料展开实验步骤记录三
description: 记录布料展开实验的最新节点启动顺序与操作步骤
categories: [笔记,布料展开实验]
keywords: 笔记,布料展开
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

## 实验步骤

按以下顺序启动节点并进行实验。需要同时运行的节点分别在新终端中启动，切换控制器的两条命令在同一个终端中执行。

### 1、启动左右夹爪

启动左臂夹爪：

```bash
ros2 run servo_test left_gripper_test_node
```

新开终端，启动右臂夹爪：

```bash
ros2 run servo_test right_gripper_test_node
```

### 2、启动双臂真机与MoveIt

新开终端，启动双臂真机：

```bash
ros2 launch iiwa_bringup iiwa_bringup.launch.py side:=dual
```

新开终端，启动MoveIt：

```bash
ros2 launch iiwa_moveit_config iiwa_moveit.launch.py
```

### 3、切换控制器

新开终端，进入工作目录并切换控制器：

```bash
cd ~/ws_iiwa/src/iiwafri2 && . switch_forward_controllers.bash
```

### 4、启动MoveIt-Servo

新开终端运行：

```bash
ros2 launch iiwa_moveit_config iiwa_servo.launch.py
```

### 5、启动实验系统

新开终端运行：

```bash
ros2 launch multi_nodes_launch full_system.launch.py
```

### 6、启动机械臂控制

新开终端运行：

```bash
ros2 launch multi_nodes_launch arm_control.launch.py
```

### 7、发布开始命令

新开终端运行，开始实验并启动视频记录：

```bash
ros2 run find_cloth_corner start_record_video
```

### 8、启动层数控制终端

新开终端运行：

```bash
ros2 run force_classify layer_control_console
```

### 9、摊布

进入摊布阶段后，新开终端运行：

```bash
ros2 run iiwa_move new_iiwa_pub
```

根据终端提示进行摊布操作。

### 10、发布完成信号

完成摊布操作后，新开终端发布完成信号：

```bash
ros2 topic pub /total_finish std_msgs/msg/Bool "{data: true}"
```

### 11、启动布料覆盖率节点

最后新开终端运行：

```bash
ros2 launch multi_nodes_launch cloth_coverage.launch.py
```
