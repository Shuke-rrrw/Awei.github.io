---
layout: post
title: 实验｜布料展开实验步骤记录
description: 记录一下实验步骤
categories: [笔记,CNN]
keywords: 笔记,布料展开
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

# FabricUnfolding（dynamixel分支同时支持飞特和dynamixel舵机，使用时可直接更换舵机，注意修改串口号）

# 教程
FabricUnfolding为周彦伶研究生毕业设计项目，本人在其工作基础上继续改进工作。

## 项目内容
使用kuka机械臂、二指六自由度机械手、Robotiq夹爪和~~uskin触觉传感器~~完成织物展开任务

## 环境
Ubuntu22.04+ros2 humble

## 功能包目录
cloth：寻找织物角点

iiwa_move：分段控制机械臂运动

iiwa_robotiq_user：控制机械臂和机械手的程序，旧版本 无用

servo_test：控制二指六自由度自制机械手

uskin_fri2：uskin 4x4 4x6驱动功能包

## 实验步骤

### 1、运行iiwa机械臂真机：

```
ros2 launch iiwa_bringup iiwa_bringup.launch.py side:=dual
```

新开终端，运行：
```
ros2 launch iiwa_moveit_config iiwa_moveit.launch.py
```
在rviz中将左臂位置调整到'left_gripper_cloth2',右臂位置调整到'right_gripper_cloth3'。

新开一个终端，切换控制器：
```
cd ~/ws_iiwa/src/iiwafri2

. switch_forward_controllers.bash
```
新开终端启动MoveIt-Servo：
```
ros2 launch iiwa_moveit_config iiwa_servo.launch.py
```

### 2、启动相机节点、启动自制夹爪、启动robotiq夹爪，指尖差速器

相机
```
ros2 run find_cloth_corner find_corner 
```

自制夹爪：
```
ros2 run servo_test left_gripper_node

```

robotiq夹爪：
```
ros2 run robotiq_gripper_client robotiq_control

ros2 run robotiq_gripper_client robotiq_pub

```

### 3、摆放好布料的位置，使布料位于自制夹爪下方

### 4、启动左臂节点

1）
```
ros2 run iiwa_move left_arm_sub
```

运行下面这个命令后左臂会开始下降：
```
ros2 run iiwa_move left_arm_pub
```
机械臂下降到预定位置后，在指尖差速器的终端输入2往上转布，到合适位置后按0结束转布，这个时候机械臂会上升。

2）机械臂上升到预定位置后，关闭机械臂的所有节点，切换左臂到手动模式，然后调高左臂的位置（这一步是为了方便右臂夹到布料）

3）运行右臂：
```
ros2 launch iiwa_bringup iiwa_bringup.launch.py side:=right
```

新开终端，运行：
```
ros2 launch iiwa_moveit_config iiwa_moveit.launch.py
```
在rviz中将右臂位置调整到'right_gripper_cloth3'（第一步已经调好，但是启动时机械臂会动一下，也可以不用调）。

新开一个终端，切换控制器：
```
cd ~/ws_iiwa/src/iiwafri2

. switch_forward_controllers.bash
```
新开终端启动MoveIt-Servo：
```
ros2 launch iiwa_moveit_config iiwa_servo.launch.py
```
启动右臂节点：
```
ros2 run iiwa_move right_arm_sub
```

```
ros2 run iiwa_move right_arm_pub
```
这个时候根据终端上的提示控制右臂，右臂到达预定位置后按下q夹布，如果成功夹到布，马上手动操控左臂下降到之前自动停止的位置附近，不然布料会从左臂中脱落。***（这个过程很容易失败）***

4）摄像头会开始执行边缘跟踪，等待找到角点，找到角点后，右臂会停止，右臂上的robotiq夹爪会松开。***（这个过程很容易失败）***

5）接下来进行转布操作，在指尖差速器终端可以看到命令提示，目标是把竖着的边转为水平状态，这一过程目前是手动进行，自动进行很难。***（这个过程很容易失败）***

6）转布完成后，退出转布操作，这个时候自制夹爪会变换位姿以便进行分层操作。

7）根据指尖差速器终端的提示进行分层操作。***（这个过程很容易失败）***

8）分层成功后，自制夹爪会变换位姿为边缘跟踪状态以便进行第二次边缘跟踪。

9）重新运行右臂：
```
ros2 launch iiwa_bringup iiwa_bringup.launch.py side:=right
```

新开终端，运行：
```
ros2 launch iiwa_moveit_config iiwa_moveit.launch.py
```
在rviz中将右臂位置调整到'right_gripper_cloth3'。

新开一个终端，切换控制器：
```
cd ~/ws_iiwa/src/iiwafri2

. switch_forward_controllers.bash
```
新开终端启动MoveIt-Servo：
```
ros2 launch iiwa_moveit_config iiwa_servo.launch.py
```
启动右臂节点：
```
ros2 run iiwa_move right_arm_sub
```

```
ros2 run iiwa_move right_arm_pub2
#注意这里是pub2
```
这个时候根据终端上的提示控制右臂，右臂到达预定位置后按下q夹布。***（这个过程很容易失败）***

10）等待第二次边缘跟踪完成

11）运行iiwa机械臂真机：

```
ros2 launch iiwa_bringup iiwa_bringup.launch.py side:=dual
```

新开终端，运行：
```
ros2 launch iiwa_moveit_config iiwa_moveit.launch.py
```

新开一个终端，切换控制器：
```
cd ~/ws_iiwa/src/iiwafri2

. switch_forward_controllers.bash
```
新开终端启动MoveIt-Servo：
```
ros2 launch iiwa_moveit_config iiwa_servo.launch.py
```

12）运行以下命令控制双臂：
```
ros2 run iiwa_move new_iiwa_test
ros2 run iiwa_move iiwa_move_pub
```
13）用键盘控制双臂放下布后，关闭new_iiwa_test iiwa_move_pub，然后在终端中运行以下命令放下布料：
```
ros2 topic pub /total_finish std_msgs/msg/Bool "{data: true}"
```

实验完成。




