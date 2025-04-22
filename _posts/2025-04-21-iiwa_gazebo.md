---
layout: post
title: iiwa｜iiwa无法开启Gazebo仿真环境
categories: [笔记，iiwa]
description: iiwa仿真环境
keywords: issue
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---
### 遇到问题描述

开启iiwa Gazebo仿真环境需要运行命令：
```sh
ros2 launch iiwa_bringup iiwa_bringup.launch.py use_sim:=true
```

但是会提示错误
```sh
...
[ruby $(which ign) gazebo-7] [GUI] [Msg] Loading plugin [ignition-rendering-ogre2]
[ruby $(which ign) gazebo-7] [GUI] [Msg] Loading plugin [ignition-rendering-ogre2]
[ruby $(which ign) gazebo-7] [GUI] [Dbg] [EntityContextMenuPlugin.cc:79] Entity context menu plugin is using camera [scene::Camera(65527)]
[ruby $(which ign) gazebo-7] [GUI] [Dbg] [SignalHandler.cc:141] Received signal[2].
[ruby $(which ign) gazebo-7] [GUI] [Dbg] [Gui.cc:472] Shutting down ign-gazebo-gui
[ruby $(which ign) gazebo-7] [GUI] [Dbg] [Application.cc:140] Terminating application.
[ruby $(which ign) gazebo-7] [GUI] [Msg] Loading plugin [ignition-rendering-ogre2]
[ruby $(which ign) gazebo-7] [GUI] [Dbg] [MinimalScene.cc:657] Destroy scene [scene]
[INFO] [ruby $(which ign) gazebo-7]: process has finished cleanly [pid 7256]
[INFO] [launch]: process[ruby $(which ign) gazebo-7] was required: shutting down launched system
[INFO] [spawner-6]: sending signal 'SIGINT' to process[spawner-6]
[INFO] [spawner-5]: sending signal 'SIGINT' to process[spawner-5]
[INFO] [spawner-4]: sending signal 'SIGINT' to process[spawner-4]
[INFO] [spawner-3]: sending signal 'SIGINT' to process[spawner-3]
[INFO] [spawner-2]: sending signal 'SIGINT' to process[spawner-2]
[INFO] [robot_state_publisher-1]: sending signal 'SIGINT' to process[robot_state_publisher-1]
[robot_state_publisher-1] [INFO] [1745218378.254009020] [rclcpp]: signal_handler(signum=2)
[INFO] [robot_state_publisher-1]: process has finished cleanly [pid 7244]
[ERROR] [spawner-6]: process[spawner-6] failed to terminate '5' seconds after receiving 'SIGINT', escalating to 'SIGTERM'
[ERROR] [spawner-5]: process[spawner-5] failed to terminate '5' seconds after receiving 'SIGINT', escalating to 'SIGTERM'
[ERROR] [spawner-4]: process[spawner-4] failed to terminate '5' seconds after receiving 'SIGINT', escalating to 'SIGTERM'
[ERROR] [spawner-3]: process[spawner-3] failed to terminate '5' seconds after receiving 'SIGINT', escalating to 'SIGTERM'
[ERROR] [spawner-2]: process[spawner-2] failed to terminate '5' seconds after receiving 'SIGINT', escalating to 'SIGTERM'
[INFO] [spawner-6]: sending signal 'SIGTERM' to process[spawner-6]
[INFO] [spawner-5]: sending signal 'SIGTERM' to process[spawner-5]
[INFO] [spawner-4]: sending signal 'SIGTERM' to process[spawner-4]
[INFO] [spawner-3]: sending signal 'SIGTERM' to process[spawner-3]
[INFO] [spawner-2]: sending signal 'SIGTERM' to process[spawner-2]
[ERROR] [spawner-6]: process has died [pid 7254, exit code -15, cmd '/opt/ros/humble/lib/controller_manager/spawner forward_position_controller_L -c /controller_manager --inactive --ros-args'].
[ERROR] [spawner-4]: process has died [pid 7250, exit code -15, cmd '/opt/ros/humble/lib/controller_manager/spawner left_arm_controller -c /controller_manager --ros-args'].
[ERROR] [spawner-5]: process has died [pid 7252, exit code -15, cmd '/opt/ros/humble/lib/controller_manager/spawner forward_position_controller_R -c /controller_manager --inactive --ros-args'].
[ERROR] [spawner-3]: process has died [pid 7248, exit code -15, cmd '/opt/ros/humble/lib/controller_manager/spawner right_arm_controller -c /controller_manager --ros-args'].
[ERROR] [spawner-2]: process has died [pid 7246, exit code -15, cmd '/opt/ros/humble/lib/controller_manager/spawner joint_state_broadcaster --controller-manager /controller_manager --ros-args'].
```
### 故障原因

原因未知。

### 解决办法

进入Gazebo工作空间删掉原来的编译文件重新编译：
```sh
cd ~/gazebo_ws

rm -rf build install log

colcon build
```

然后source一下再运行开启仿真环境的命令就不会报错。


