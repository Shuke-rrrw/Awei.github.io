---
layout: post
title: 实验｜布料展开实验步骤记录二
description: 记录一下新的实验步骤
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

新开终端启动左臂和右臂节点
```
ros2 run iiwa_move left_arm_sub
```

```
ros2 run iiwa_move left_arm_pub
```

```
ros2 run iiwa_move right_arm_sub
```

```
ros2 run iiwa_move right_arm_pub
```

### 2、指尖差速器


左差速器
```
ros2 run servo_test left_differential_test_node
```

右差速器
```
ros2 run servo_test right_differential_test_node
```

### 3、启动u_skin触觉传感器
新开终端运行
```
ros2 run forcesensor_can forcesensor_can
```
检查输出没有报错后新开终端运行
右手触觉传感器（4x4阵列）
```
ros2 run forcesensor_client forcesensor_client4x4
```
左手触觉传感器（6x4阵列）
```
ros2 run forcesensor_client forcesensor_client6x4
```

### 4、启动相机节点

左臂相机
```
ros2 run find_cloth_corner find_corner_left 
```
右臂相机
```
ros2 run find_cloth_corner find_corner_right 
```
记录相机：
```
ros2 run find_cloth_corner video_capture
```
### 5、启动自制夹爪
摆放好布料。

左臂夹爪
```
ros2 run servo_test left_gripper_test_node
```

右臂夹爪
```
ros2 run servo_test right_gripper_test_node
```
启动夹爪节点后机械臂就会开始下降抓布
### 6、发布开始命令
新开终端运行以下命令
```
ros2 run find_cloth_corner start_record_video
```
实验开始。

### 7、摊布
等待机械臂找到两个边角后，关掉之前机械臂的四个sub、pub节点，然后新开终端运行以下命令
```
ros2 run iiwa_move new_iiwa_test
```
```
ros2 run iiwa_move iiwa_move_pub
```
根据提示手动操作将布料摊平





